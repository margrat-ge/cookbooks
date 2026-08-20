# Coding System Setup

A start-to-finish guide to getting a complete coding environment running on a
Windows machine — Linux, Python, GitHub, and Claude Code — with no prior computer
science background assumed.

Work through the guides below **in order**. Each one builds on the tools installed
in the previous step, and each has ✅ *Check for success* boxes so you can confirm a
step worked before moving on.

> 💡 Most of this work happens in the **WSL terminal** (a Linux terminal running
> inside Windows). Keep your code and projects inside the Linux home folder
> (e.g. `/home/yourname/...`), not on the Windows `C:` drive — it runs much faster
> there.

---

## Setup Order

### 1. [`wsl-setup-guide.md`](wsl-setup-guide.md) — Base environment
**Start here.** Installs WSL (Linux inside Windows) and the core coding tools:
`uv`, Python, VS Code, and Claude Code. By the end you'll have a working terminal
and editor.

### 2. [`github-setup-guide.md`](github-setup-guide.md) — Version control & GitHub
Create a GitHub account and learn version control: installing Git, cloning repos,
the everyday commit/push/pull workflow, personal access tokens, and the GitHub CLI
(`gh`) for creating Pull Requests from the terminal.

### 3. [`uv-python-setup-guide.md`](uv-python-setup-guide.md) — Python environments
Go deeper on managing Python projects with `uv`: virtual environments, installing
packages, running code, and managed project mode. Includes the **Bloomberg
internal** setup (internal PyPI mirror behind the corporate proxy) and WSL-specific
notes.

### 4. [`claude-getting-started.md`](claude-getting-started.md) — Working with Claude
Set up your Claude account and learn how to get the most out of Claude Code:
picking a language, planning mode, effort levels, `/init`, writing tests, and
running code reviews.

---

## Prerequisites

- Windows 10 (version 2004+) or Windows 11
- **Administrator rights** (the ability to install software)

---

## Where you'll end up

After completing all four guides you'll have:

| Tool          | What it's for                              | Quick check                    |
| ------------- | ------------------------------------------ | ------------------------------ |
| **WSL**       | Runs Linux inside Windows                  | `wsl -l -v`                    |
| **uv**        | Installs Python and Python packages        | `uv --version`                 |
| **Python**    | The language you write code in             | `uv run python --version`      |
| **VS Code**   | Editor for writing and running code        | `WSL: Ubuntu` shown in corner  |
| **Git**       | Tracks changes to your code                | `git --version`                |
| **gh**        | GitHub tasks (like PRs) from the terminal  | `gh --version`                 |
| **Claude Code** | AI assistant that helps you write code   | `claude --version`             |

---

## Also in this directory

- [`wsl-vhdx-reclaim.md`](wsl-vhdx-reclaim.md) — how to reclaim disk space when the
  WSL virtual disk grows over time.
</content>
</invoke>
