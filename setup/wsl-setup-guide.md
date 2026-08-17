# Setting Up WSL and Essential Coding Tools on Windows

This guide walks you through setting up a complete coding environment on a Windows
computer using **WSL** (Windows Subsystem for Linux). No prior computer science
background is needed — just follow each step in order, and use the ✅ **Check for
success** boxes to confirm each step worked before moving on.

> **What is WSL?** WSL lets you run Linux (a developer-friendly operating system)
> *inside* Windows. Most professional coding tools work best on Linux, so this gives
> you the best of both worlds.

---

## Table of Contents

1. [Install WSL](#step-1-install-wsl)
2. [Install uv (Python package manager)](#step-2-install-uv)
3. [Install Python using uv](#step-3-install-python-using-uv)
4. [Install VS Code (code editor)](#step-4-install-vs-code)
5. [Install Claude Code](#step-5-install-claude-code)

---

## Before You Start

- You will need a Windows 10 (version 2004+) or Windows 11 computer.
- You will need **administrator rights** on the computer (the ability to install
  software).
- Steps 1 uses **PowerShell** (a Windows program). Steps 2–5 use the **WSL terminal**
  — open it from the Start menu by typing "WSL" and clicking it. (This is the same
  Ubuntu Linux program you install in Step 1; "WSL" and "Ubuntu" open the same
  terminal.)

> 💡 **Tip:** Whenever this guide shows a command in a gray box, you can copy it and
> paste it into the terminal. To paste into a terminal, **right-click** or press
> `Ctrl+Shift+V`.

---

## Step 1: Install WSL

### What you'll do
Install WSL, which automatically installs Ubuntu (a popular version of Linux).

### Detailed steps

1. Click the **Start** menu (Windows icon, bottom-left of your screen).
2. Type `PowerShell`.
3. When **Windows PowerShell** appears in the results, **right-click** it and choose
   **"Run as administrator"**.
   - A blue window will open. If Windows asks *"Do you want to allow this app to make
     changes?"*, click **Yes**.
4. In the blue PowerShell window, type the following command and press **Enter**:

   ```powershell
   wsl --install
   ```

5. Wait for the download and installation to finish. This can take several minutes.
6. **Restart your computer** when it tells you to (or restart it yourself to be safe).
7. After restarting, an **Ubuntu** window will open automatically and ask you to
   create a username and password.
   - **Username:** type a simple lowercase name (for example, your first name) and
     press Enter.
   - **Password:** type a password and press Enter. **The screen will not show any
     characters as you type — this is normal.** Type it again to confirm.
   - ⚠️ **Remember this password!** You'll need it later for commands that start with
     `sudo`.
   - 🔑 **Forgot it?** It can't be *retrieved*, but it's easy to reset. In PowerShell,
     run `wsl -u root`, then `passwd yourname` (use your username) to set a new one.

### ✅ Check for success

Open **WSL** from the Start menu (type "WSL" and click it — this opens the same Ubuntu
Linux terminal). You should see a prompt that looks like this:

```text
yourname@COMPUTER:~$
```

You can also verify from PowerShell. Open PowerShell again and run:

```powershell
wsl -l -v
```

You should see Ubuntu listed with **VERSION 2**, similar to:

```text
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

> If `wsl --install` says it's not recognized, your Windows may be out of date. Run
> **Windows Update** (Start menu → "Check for updates"), install all updates, restart,
> and try again.

---

## Step 2: Install uv

> **What is uv?** `uv` is a fast, modern tool for installing Python and Python
> packages. It makes managing Python much simpler.

### What you'll do
Install `uv` inside your WSL terminal.

### Detailed steps

1. Open **WSL** from the Start menu (type "WSL" and click it — this is your Linux
   terminal).
2. Copy and paste the following command, then press **Enter**:

   ```bash
   curl -LsSf https://astral.sh/uv/install.sh | sh
   ```

3. Wait for it to finish. You'll see messages about downloading and installing.
4. **Close the WSL window and open it again.** This refreshes the terminal so it
   can find the newly installed `uv` command.

   > (Advanced alternative: instead of reopening, you can run `source $HOME/.bashrc`
   > to refresh the current window.)

### ✅ Check for success

In the WSL terminal, run:

```bash
uv --version
```

You should see a version number, for example:

```text
uv 0.5.11
```

If you see a version number, `uv` is installed correctly. If you see
`command not found`, close and reopen the WSL window and try again.

---

## Step 3: Install Python using uv

### What you'll do
Use `uv` to install Python — the programming language you'll write code in.

### Detailed steps

1. In the **WSL** terminal, run:

   ```bash
   uv python install 3.13
   ```

   This downloads and installs Python version 3.13.

2. Wait for the download to complete. You'll see a confirmation message when done.

### ✅ Check for success

Ask `uv` which Python versions it has installed:

```bash
uv python list
```

You should see Python 3.13 listed as installed, for example:

```text
cpython-3.13.x-linux-...    (installed)
```

You can also create a quick test to confirm Python runs:

```bash
uv run python --version
```

Expected output:

```text
Python 3.13.x
```

> 💡 **Tip:** From now on, whenever you work on a Python project, `uv` can create an
> isolated environment for it. A common first step in a new project folder is
> `uv init` followed by `uv add <package-name>` to add libraries.

---

## Step 4: Install VS Code

> **What is VS Code?** Visual Studio Code (VS Code) is a free, popular code editor.
> It runs on Windows but connects seamlessly into WSL so you can edit and run your
> Linux code with a nice graphical interface.

### What you'll do
Install VS Code on Windows, add two helpful extensions, and connect it to WSL.

### Detailed steps

#### 4a. Install VS Code on Windows

1. Open your web browser and go to: **https://code.visualstudio.com**
2. Click the **Download for Windows** button.
3. Open the downloaded file (usually in your **Downloads** folder) and follow the
   installer.
   - ✅ When the installer asks about **"Additional Tasks"**, make sure
     **"Add to PATH"** is checked (it usually is by default). This lets you launch
     VS Code from the terminal.
4. Finish the installation and open **VS Code**.

#### 4b. Install the WSL extension

1. In VS Code, click the **Extensions** icon on the left sidebar (it looks like four
   squares, one flying away). Or press `Ctrl+Shift+X`.
2. In the search box, type `WSL`.
3. Find the extension named **"WSL"** published by **Microsoft** and click
   **Install**.

#### 4c. Install the Pylance extension (for Python)

> **What is Pylance?** Pylance reads and understands your Python code — it provides
> autocomplete, error highlighting, and helpful pop-ups as you type.

1. Still in the Extensions panel, search for `Pylance`.
2. Find **"Pylance"** published by **Microsoft** and click **Install**.
   - This will also install the **Python** extension automatically, which you need.

#### 4d. Open VS Code inside WSL (automatic setup)

1. Open your **WSL** terminal.
2. Type the following and press **Enter** (the `.` means "this current folder"):

   ```bash
   code .
   ```

3. **The first time you do this**, VS Code will automatically download and start a
   small helper program (the "VS Code Server") inside WSL. Just wait — this is normal
   and only happens once.
4. A VS Code window will open on Windows, now connected to your Linux environment.

### ✅ Check for success

- Look at the **bottom-left corner** of the VS Code window. You should see a
  green/blue box that says **`WSL: Ubuntu`** (with a `><` icon next to it). This
  confirms VS Code is connected to WSL.

  ![The green/blue "WSL: Ubuntu" box in the bottom-left corner of VS Code](images/vscode-wsl-badge.png)

  > 📸 **How to add this screenshot:** The image above will appear once you save a
  > real screenshot at `images/vscode-wsl-badge.png` (relative to this guide). To
  > capture it: with VS Code open and connected to WSL, press `Windows + Shift + S`,
  > drag a box around the **bottom-left green badge**, then paste and save the image
  > to that path. Until then, the badge looks like a small green rectangle reading
  > `>< WSL: Ubuntu`.

- To confirm Pylance works, create a test file:
  1. In VS Code, create a new file called `test.py`.
  2. Type `import os` and then on a new line type `os.` — a pop-up list of
     suggestions should appear. That pop-up is Pylance working correctly. ✅

> 💡 **Tip:** When VS Code asks *"Select Python Interpreter"*, choose the one from
> `uv` / version 3.13 that you installed in Step 3.

---

## Step 5: Install Claude Code

> **What is Claude Code?** Claude Code is Anthropic's AI coding assistant that runs in
> your terminal. It can read your code, make changes, run commands, and help you build
> software using plain-English instructions.

### What you'll do
Install Claude Code inside WSL.

### Detailed steps

1. Open your **WSL** terminal.
2. Copy and paste this command, then press **Enter**:

   ```bash
   curl -fsSL https://claude.ai/install.sh | bash
   ```

3. Wait for the installation to finish.
4. **Close the WSL window and open it again** so the terminal can find the new
   `claude` command.

### ✅ Check for success

Check the version:

```bash
claude --version
```

You should see a version number, for example:

```text
2.x.x (Claude Code)
```

You can also run Claude Code's built-in health check, which verifies everything is
set up correctly:

```bash
claude doctor
```

### Start using Claude Code

1. In the terminal, go into a project folder (or make a new one):

   ```bash
   mkdir my-first-project
   cd my-first-project
   ```

2. Start Claude Code by typing:

   ```bash
   claude
   ```

3. The first time, it will guide you through **logging in** with your Claude account
   (Claude Pro/Max or Anthropic Console). Follow the on-screen prompts.
4. Once logged in, you'll see a prompt where you can type instructions in plain
   English, for example:

   ```text
   > write a hello world program in python
   ```

---

## 🎉 You're All Set!

You now have a complete, professional coding environment:

| Tool          | What it's for                                  | How to check       |
| ------------- | ---------------------------------------------- | ------------------ |
| **WSL**       | Runs Linux inside Windows                      | `wsl -l -v`        |
| **uv**        | Installs Python and Python packages            | `uv --version`     |
| **Python**    | The programming language you write code in     | `uv run python --version` |
| **VS Code**   | Editor for writing and running code            | `WSL: Ubuntu` shown in corner |
| **Claude Code** | AI assistant that helps you write code       | `claude --version` |

> 🔗 **Next:** To set up GitHub and install the GitHub CLI (`gh`) for tasks like
> creating Pull Requests, see the companion **`github-setup-guide.md`**.

### Everyday workflow tips

- **Always open the WSL terminal** (not plain PowerShell) for coding work.
- **Keep your code inside the Linux home folder** (e.g. `/home/yourname/projects/`),
  not on the Windows `C:` drive — it runs much faster there.
- To open any project in VS Code, `cd` into its folder and run `code .`.

---

## Troubleshooting

| Problem | Solution |
| ------- | -------- |
| `wsl --install` not recognized | Run Windows Update, restart, and try again. |
| `command not found` after installing a tool | Close and reopen the WSL terminal. |
| Forgot your WSL password | Reset it: in PowerShell run `wsl -u root`, then `passwd yourname`. |
| `code .` doesn't open anything | Make sure VS Code is installed on **Windows** with "Add to PATH" checked, then reopen the WSL terminal. |
| Permission errors when installing | Use `sudo` before the command and enter your WSL password. |
