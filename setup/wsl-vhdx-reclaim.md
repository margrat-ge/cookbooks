# Reclaiming Disk Space from a WSL Distro's VHDX

When you delete data inside a WSL2 distro (e.g. `docker system prune`), the space is freed
*inside* the Linux filesystem, but the virtual disk file (`ext4.vhdx`) that backs the distro on
the Windows host does **not** shrink automatically. This runbook walks through checking,
trimming, and compacting that VHDX to actually return space to the `C:` drive.

Run PowerShell steps in an elevated PowerShell window. Run the `sudo` steps inside the WSL
distro itself.

## 1. Clean up data inside the distro first

Compaction only removes space that's already free inside the filesystem — always clean up the
actual data before touching the VHDX.

```bash
# example: Docker cleanup
docker builder prune -af
docker system prune -a --volumes -f
```

## 2. Confirm which distro you're working on

```powershell
wsl -l -v
```

```
  NAME            STATE           VERSION
* Ubuntu-24.04    Running         2
  Ubuntu-20.04    Stopped         2
```

The `*` marks the default distro. Make sure this matches the distro you actually ran cleanup
commands in — if you have multiple distros (or Docker Desktop's `docker-desktop-data`), it's easy
to trim/compact the wrong one.

## 3. Locate the real VHDX path

**Don't** assume the file lives under `%LOCALAPPDATA%\Packages\...` — that's only true for
distros installed as Microsoft Store packages. Distros installed via `wsl --install`/`--import`
on newer WSL versions may live under `%LOCALAPPDATA%\wsl\{guid}\`.

Get the authoritative path from the registry instead of guessing:

```powershell
Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Lxss" | ForEach-Object {
  Get-ItemProperty $_.PSPath
} | Select-Object DistributionName, BasePath
```

```
DistributionName BasePath
---------------- --------
Ubuntu-24.04     C:\Users\<you>\AppData\Local\wsl\{8dd82c31-bc43-45e5-a483-340acfff0047}
```

The VHDX file is `<BasePath>\ext4.vhdx`.

**Case: `Get-ChildItem -Recurse -Filter *.vhdx` under `Packages`/`Docker` found the wrong
file (e.g. an old Ubuntu-20.04 vhdx) or nothing at all.**
→ That search only covers Store-package and Docker Desktop install locations. If your distro
was installed/moved outside those paths, the registry lookup above is the only reliable way to
find it.

## 4. Trim the filesystem inside the distro

Sparse-file reclamation on the Windows side depends on the guest filesystem actually issuing
`discard`/TRIM for freed blocks. Ubuntu's `fstrim.timer` only runs weekly by default, so force it:

```bash
sudo fstrim -av
```

## 5. Enable sparse mode (auto-reclaim going forward)

```powershell
wsl --shutdown
wsl --manage Ubuntu-24.04 --set-sparse true
```

This makes the VHDX auto-shrink as blocks are freed/trimmed in the future. It does **not**
retroactively compact space that was already allocated before trimming — that's what step 6/7
are for.

## 6. Verify the real size (not just "used space" on C:)

`Get-Item file.Length` (and Explorer's plain size column) reports the file's **logical** size,
not how much is physically allocated on disk for a sparse file. Check the real allocation:

```powershell
Get-Item "<BasePath>\ext4.vhdx" | Select-Object FullName, @{n='SizeGB';e={$_.Length/1GB}}
```

and compare **"Size"** vs **"Size on disk"** in Explorer → right-click the file → Properties.
"Size on disk" is the number that reflects actual reclaimed space.

**Case: C: free space "hasn't changed" right after `--set-sparse true`.**
→ Reclamation happens around VM shutdown/trim events, not instantly. Re-check after a fresh
`wsl --shutdown`, and check "Size on disk" specifically rather than a cached Explorer/drive view.

## 7. Manual compaction fallback (if sparse alone isn't enough)

**Case: `diskpart` fails with:**
```
DiskPart has encountered an error: The requested operation could not be completed due to a
virtual disk system limitation. Virtual hard disk files must be uncompressed and unencrypted
and must not be sparse.
```
→ DiskPart's `compact vdisk` cannot operate on a VHDX that already has sparse mode enabled —
the two mechanisms are mutually exclusive. Temporarily disable sparse, compact, then re-enable:

```powershell
wsl --shutdown
wsl --manage Ubuntu-24.04 --set-sparse false

diskpart
```
```
select vdisk file="<BasePath>\ext4.vhdx"
attach vdisk readonly
compact vdisk
detach vdisk
exit
```
```powershell
wsl --manage Ubuntu-24.04 --set-sparse true
```

**Case: Hyper-V's `Optimize-VHD` is preferred instead of DiskPart.**
```powershell
Optimize-VHD -Path "<BasePath>\ext4.vhdx" -Mode Full
```
→ Only works if the Hyper-V PowerShell module is installed/enabled. If you get a
"command not recognized" error, Hyper-V isn't available — use the DiskPart route above instead.
(Optimize-VHD has the same sparse restriction as DiskPart — disable sparse first if it errors.)

## 8. Verify

```powershell
Get-Item "<BasePath>\ext4.vhdx" | Select-Object @{n='SizeGB';e={$_.Length/1GB}}
Get-PSDrive C
```

## Extra: cleaning up orphaned/unused distros

If `wsl -l -v` shows old distros you no longer use (e.g. a leftover `Ubuntu-20.04` from a prior
upgrade) consuming space independently of the one above:

```powershell
wsl --unregister Ubuntu-20.04
```

⚠️ This permanently deletes that distro and all data inside it — confirm you don't need it
before running.

## Summary flow

1. Clean data inside the distro (`docker system prune`, etc.)
2. `wsl -l -v` → confirm target distro
3. Registry lookup → confirm real VHDX `BasePath`
4. `sudo fstrim -av` inside the distro
5. `wsl --shutdown` + `wsl --manage <distro> --set-sparse true`
6. Check "Size on disk", not logical size
7. If still bloated: disable sparse → DiskPart/Optimize-VHD compact → re-enable sparse
8. Verify C: free space increased

## Appendix: Docker system cleanup reference

Source: [Bloomberg Tutti — Running out of space on laptop](https://tutti.prod.bloomberg.com/local-development/recommendations/housekeeping)

Check Docker's disk usage:

```bash
docker system df
```

```
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          18        2         20.66GB   18.28GB (88%)
Containers      2         0         1.078GB   1.078GB (100%)
Local Volumes   6         6         1.941kB   0B (0%)
Build Cache     420       0         10.32GB   10.32GB
```

### Removing everything

Removes all stopped containers, unused networks, dangling images, and dangling build cache:

```bash
# see https://bburl/purge-docker (run with -h for options)
```

**Note**: this script may not clear named volumes — see the Local Volumes section below to
remove those manually.

### Containers

```bash
# list all containers
docker ps -a

# filter for exited containers
docker ps -a -f status=exited

# remove a single container
docker rm <container_hash>

# remove all exited containers
docker container prune

# stop a running container before removing it
docker stop <container_hash>
```

Tip: use `docker run --rm` so one-off containers clean themselves up on exit.

### Images

```bash
# list all images
docker images -a

# remove a single image
docker rmi <image_hash>

# filter for dangling (untagged, unused) images
docker images -f dangling=true

# remove all dangling images
docker image prune
```

### Local volumes

```bash
# list all local volumes
docker volume ls

# filter volumes by name
docker volume ls --filter name=<some_substring>

# remove a single volume
docker volume rm <volume_name>

# remove multiple volumes filtered by name
docker volume ls --quiet --filter name=<some_substring> | xargs -r docker volume rm
```

**Case: `docker volume rm` fails with `volume is in use - [<container_id>]`.**
→ Stop and remove the container referenced in the error first (see Containers above), then
retry the volume removal.

### Build cache

```bash
docker builder prune
```

### Also clear WSL temp files before compacting

Before running the VHDX compaction steps earlier in this doc, also clear `/tmp` inside the
distro to free up additional space:

```bash
rm -rf /tmp/*
```

### Delete old R+ binaries

If you build/run R+ binaries, old versions accumulate indefinitely unless cleaned:

```bash
rp bin clean
```
