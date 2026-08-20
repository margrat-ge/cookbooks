# Setting Up Python with `uv` in WSL

A step-by-step guide to creating virtual environments and running Python code using
[`uv`](https://docs.astral.sh/uv/) — a fast, drop-in replacement for `pip` and `venv`.

Written for this WSL environment (Ubuntu, Python 3.13, Bloomberg internal PyPI mirror
behind the corporate proxy). Examples use `python3.13`; substitute your own version if it
differs.

---

## Table of Contents

1. [Before You Start](#1-before-you-start)
2. [Install uv](#2-install-uv)
3. [Point uv at the Internal Index (Bloomberg internal)](#3-point-uv-at-the-internal-index)
4. [Create the Virtual Environment](#4-create-the-virtual-environment)
5. [Install Packages](#5-install-packages)
6. [Run Your Code](#6-run-your-code)
7. [Optional: Managed Project Mode](#7-optional-managed-project-mode)
8. [WSL-Specific Notes](#8-wsl-specific-notes)
9. [Quick Reference](#9-quick-reference)
10. [Troubleshooting](#10-troubleshooting)

---

## 1. Before You Start

Check what you already have:

```bash
python3 --version     # e.g. 3.13.x — any recent 3.x is fine
uv --version          # "command not found" means uv isn't installed yet
```

**If both commands print a version, you're already set up — skip straight to
[section 4](#4-create-the-virtual-environment).** Otherwise install whatever is missing in
the next section.

**Why `uv` instead of `python -m venv` + `pip`?**

| | `venv` + `pip` | `uv` |
|---|---|---|
| Speed | baseline | 10–100× faster installs |
| Lockfiles | needs pip-tools / poetry | built in (`uv.lock`) |
| Python versions | manual | can install/manage them for you |
| Commands to learn | two tools | one tool |

---

## 2. Install uv

> **Already have `uv`?** If `uv --version` printed a version in section 1, skip this
> section and go to [section 3](#3-point-uv-at-the-internal-index).

The simplest route is via `pip`, since your proxy and internal PyPI mirror are already
configured for it:

```bash
python3 -m pip install --user uv
```

Then add `~/.local/bin` to your `PATH` so the `uv` command is found:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Verify:

```bash
uv --version
```

> **Alternative:** the upstream installer works too and will also succeed through your
> proxy:
> ```bash
> curl -LsSf https://astral.sh/uv/install.sh | sh
> ```
> The `pip` route is preferred here because it keeps you on the internal mirror.

---

## 3. Point uv at the Internal Index

<details>
<summary><strong>Bloomberg internal only</strong> — expand if you're on the corporate network behind the internal PyPI mirror. On a public network, skip this section entirely.</summary>

<br>

> ⚠️ **`uv` does not read `~/.pip/pip.conf`.**
> Skip this step and every install will try to reach public PyPI and fail.

Configure it once, globally:

```bash
mkdir -p ~/.config/uv
cat > ~/.config/uv/uv.toml <<'EOF'
[[index]]
url = "https://artprod.dev.bloomberg.com/artifactory/api/pypi/bloomberg-pypi/simple"
default = true
EOF
```

Your `http_proxy` / `https_proxy` environment variables are already exported in this
shell, and `uv` honors them automatically — no extra proxy config needed.

**Verify the config is picked up:**

```bash
uv pip install --dry-run requests
```

</details>

---

## 4. Create the Virtual Environment

```bash
cd /home/wge9/bql_int
uv venv
```

This creates a `.venv/` directory in your project using the system Python.

**Pin a specific Python version** (use whatever version you need):

```bash
uv venv --python 3.13
```

**Use a different directory name:**

```bash
uv venv myenv
```

**Force uv to use an already-installed Python** (if downloading one is blocked by the
proxy):

```bash
uv venv --python-preference only-system
```

---

## 5. Install Packages

```bash
uv pip install requests pandas
```

From a requirements file:

```bash
uv pip install -r requirements.txt
```

Editable install of a local package:

```bash
uv pip install -e .
```

> 💡 You do **not** need to activate the virtualenv first. `uv pip` automatically
> detects and targets `./.venv`.

List what's installed:

```bash
uv pip list
```

---

## 6. Run Your Code

You have two options.

### Option A — Without activating (recommended)

`uv run` resolves the virtualenv for you:

```bash
uv run python script.py
uv run pytest
uv run python -c "import pandas; print(pandas.__version__)"
```

### Option B — With activation

Useful if you want a plain `python` command on your prompt for a long working session:

```bash
source .venv/bin/activate

python script.py
pytest

deactivate
```

While active, your prompt is prefixed with `(bql_int)` or similar so you can tell at a
glance which environment you're in.

---

## 7. Optional: Managed Project Mode

If you'd rather have `uv` own a `pyproject.toml` and a lockfile than manage a bare venv
yourself:

```bash
uv init                 # creates pyproject.toml + .python-version
uv add requests         # installs the dep AND records it, updating uv.lock
uv remove requests      # the inverse
uv run python main.py   # auto-syncs the environment before running
uv sync                 # recreate the exact locked environment (e.g. on another machine)
```

Add the internal index to `pyproject.toml` so it travels with the repo and teammates
don't hit the same PyPI problem:

```toml
[[tool.uv.index]]
url = "https://artprod.dev.bloomberg.com/artifactory/api/pypi/bloomberg-pypi/simple"
default = true
```

**Which mode should you pick?**

- **Bare venv** (sections 4–6) — quick scripts, ad-hoc analysis, existing projects with
  a `requirements.txt`.
- **Managed project** (this section) — anything shared, versioned, or that needs
  reproducible builds.

---

## 8. WSL-Specific Notes

**Keep your project on the Linux filesystem.**
Work under `/home/wge9/...` — not `/mnt/c/...`. Virtualenv creation and package installs
are dramatically slower across the Windows 9p mount, and file permissions behave
unpredictably.

**`.venv` is not portable between WSL and Windows.**
The venv contains absolute paths and Linux binaries. If you also open the same folder
with Windows Python, create a separately-named environment (e.g. `.venv-win`) to avoid
clobbering.

**Add `.venv/` to `.gitignore`.**
Commit `requirements.txt` or `uv.lock` instead — never the environment itself.

**Watch your disk usage.**
Virtualenvs accumulate inside the WSL VHDX, which grows but doesn't automatically
shrink. See `wsl-vhdx-reclaim.md` in this directory if space becomes an issue.

---

## 9. Quick Reference

| Task | Command |
|---|---|
| Install uv | `python3 -m pip install --user uv` |
| Create venv | `uv venv` |
| Create venv with specific Python | `uv venv --python 3.13` |
| Activate | `source .venv/bin/activate` |
| Deactivate | `deactivate` |
| Install a package | `uv pip install <pkg>` |
| Install from requirements | `uv pip install -r requirements.txt` |
| List installed packages | `uv pip list` |
| Freeze current env | `uv pip freeze > requirements.txt` |
| Run a script | `uv run python script.py` |
| Run tests | `uv run pytest` |
| Start a project | `uv init` |
| Add a tracked dependency | `uv add <pkg>` |
| Sync from lockfile | `uv sync` |

---

## 10. Troubleshooting

**`uv: command not found` after installing**
`~/.local/bin` isn't on your `PATH`. Re-run the `export` line from
[section 2](#2-install-uv), then `source ~/.bashrc`.

**Installs hang or fail with connection/timeout errors**
Almost always the index config from [section 3](#3-point-uv-at-the-internal-index)
is missing. Confirm the file exists:
```bash
cat ~/.config/uv/uv.toml
```

**`No solution found` / package not on the mirror**
The internal mirror may not carry that package or version. Try relaxing the version
constraint, or check whether the package needs to be requested for the mirror.

**uv tries to download a Python interpreter and it fails**
Force it to use the system one:
```bash
uv venv --python-preference only-system
```

**Wrong Python being used inside the venv**
Delete and recreate — it's cheap:
```bash
rm -rf .venv && uv venv --python 3.13
```

**Imports fail even though the package installed**
You're likely running the system `python` rather than the venv's. Use `uv run python ...`
or confirm activation with `which python` (it should point inside `.venv/bin`).

---

## Further Reading

- Official docs: <https://docs.astral.sh/uv/>
- `uv --help` and `uv <subcommand> --help` are genuinely good
