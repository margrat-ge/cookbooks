# Getting Started with GitHub 🚀

A beginner-friendly guide to setting up your GitHub account, creating your first
repository, and using Git to manage your code.

---

## Table of Contents

1. [What Is GitHub (and Version Control)?](#1-what-is-github-and-version-control)
2. [Setting Up a New GitHub Account](#2-setting-up-a-new-github-account)
3. [Creating a New Repository](#3-creating-a-new-repository)
4. [Generating a Personal Access Token (for Cloning)](#4-generating-a-personal-access-token-for-cloning)
5. [Install Git](#5-install-git)
6. [Common Git Commands](#6-common-git-commands)
7. [Install the GitHub CLI (gh)](#7-install-the-github-cli-gh)

---

## 1. What Is GitHub (and Version Control)?

### Version control in plain English

Imagine writing an essay and saving copies called `essay_final.doc`,
`essay_final_v2.doc`, `essay_REALLY_final.doc`... It gets messy fast, and if two
people edit at once, someone's work gets lost.

**Version control** solves this. It's a system that tracks every change to your
files over time, so you can:

- See exactly *what* changed, *when*, and *who* changed it
- Go back to any earlier version if something breaks
- Let multiple people work on the same project without overwriting each other

**Git** is the most popular version control tool. It runs on your computer and
keeps a complete history of your project.

### Where does GitHub fit in?

**GitHub** is a website that hosts your Git projects in the cloud. Think of Git
as the engine and GitHub as the garage where you park and share your work.
GitHub adds collaboration features on top of Git:

- A backup of your code that lives online
- A place to share projects publicly or keep them private
- Tools for teams: reviewing changes, tracking bugs, and discussing ideas

> 📖 **Learn more:** [GitHub's official documentation](https://docs.github.com/en/get-started)
> and the [GitHub Hello World tutorial](https://docs.github.com/en/get-started/start-your-journey/hello-world).

**A quick note on vocabulary:**

| Term | What it means |
|------|---------------|
| **Repository (repo)** | A project folder that Git tracks |
| **Commit** | A saved snapshot of your changes |
| **Branch** | A separate line of work, so you can experiment safely |
| **Clone** | Download a copy of a repo to your computer |
| **Push / Pull** | Send your changes up / bring others' changes down |

---

## 2. Setting Up a New GitHub Account

1. Go to **[github.com](https://github.com)** and click **Sign up**.
2. Enter your **email address**, then create a **password** and a **username**.
   - 💡 Pick a username you won't mind sharing professionally — many people use
     it like a résumé.
3. Solve the puzzle to verify you're human, then click **Create account**.
4. **Verify your email**: GitHub sends you a code. Enter it to confirm your
   address.
5. Answer a few optional setup questions (or skip them). The **free plan** is all
   you need to start.

### Recommended: turn on two-factor authentication (2FA)

2FA adds a second step at login (usually a code from your phone) so your account
stays secure even if your password leaks. GitHub now requires it for many users.

- Go to **Settings → Password and authentication → Two-factor authentication**
  and follow the prompts.

---

## 3. Creating a New Repository

1. Click the **+** icon in the top-right corner of GitHub, then choose
   **New repository**.
2. Fill in the details:
   - **Repository name** — e.g. `my-first-project`
   - **Description** *(optional)* — a short note about what it does
   - **Public** (anyone can see it) or **Private** (only you and people you invite)
3. Check **Add a README file**. A README is the front page of your project —
   it's where you explain what the project is.
4. *(Optional)* Add a **.gitignore** (tells Git which files to skip) and a
   **license** (tells others how they may use your code).
5. Click **Create repository**. 🎉

You now have a repo with a URL like:
`https://github.com/your-username/my-first-project`

---

## 4. Generating a Personal Access Token (for Cloning)

When you clone or push over HTTPS, GitHub asks you to log in — but it **no longer
accepts your account password** on the command line. Instead you use a
**Personal Access Token (PAT)**, which acts like a temporary, revocable password.

This is especially important when cloning a **collaborator's private repo**.

### Steps to create a token

1. On GitHub, click your **profile photo** (top right) → **Settings**.
2. Scroll down to **Developer settings** (bottom of the left sidebar).
3. Click **Personal access tokens → Tokens (classic)** →
   **Generate new token (classic)**.
   - *(Fine-grained tokens are also available and let you limit access to
     specific repos — a good choice for tighter security.)*
4. Configure the token:
   - **Note** — a name so you remember what it's for (e.g. `laptop-cloning`)
   - **Expiration** — pick a sensible limit like 30 or 90 days
   - **Scopes** — scopes control *what the token is allowed to do*. Only check
     what you actually need — a token with fewer scopes is safer if it ever leaks.

   **✅ Recommended for cloning and pushing:** just check **`repo`**. That's all
   you need to clone (including private repos), pull, and push.

   Here's what the common scopes mean:

   | Scope | What it allows | Do you need it? |
   |-------|----------------|-----------------|
   | **`repo`** | Full read/write access to your repositories — clone, pull, push, and manage private repos | ✅ **Yes** — this is the one you want for cloning a collaborator's repo |
   | **`workflow`** | Update GitHub Actions workflow files (the `.github/workflows` automation) | Only if you'll edit CI/CD automation files |
   | **`admin:org`** | Manage an organization — its teams, members, and settings | Only if you administer a GitHub organization |
   | **`admin:public_key`** | Manage the SSH keys on your account | Rarely — not needed for HTTPS cloning |
   | **`gist`** | Create and edit gists (small shared code snippets) | Only if you use gists |
   | **`notifications`** | Read and manage your notifications | Rarely needed |
   | **`delete_repo`** | Permanently delete repositories | Best left **unchecked** for safety |

   > 💡 **Rule of thumb:** when in doubt, start with just **`repo`**. You can
   > always generate a new token with more scopes later if a tool asks for them.
5. Click **Generate token**.
6. **Copy the token immediately** and store it somewhere safe (a password
   manager is ideal).

> ⚠️ **You will only see the token once!** If you lose it, just generate a new one.

### Using the token to clone

When you run `git clone` on a private repo, Git will prompt for credentials:

```bash
git clone https://github.com/collaborator-username/their-repo.git
# Username: your-github-username
# Password: paste-your-token-here   (NOT your account password)
```

### Save your credentials (so you don't type them every time)

By default Git asks for your username and token on **every** clone, push, and
pull. A **credential helper** remembers them for you after the first time, so you
authenticate once and Git handles the rest.

> 🔑 Wherever these steps ask for a "password", paste your **Personal Access
> Token** from above — never your account password.

**The simplest option: the built-in `gh` login.** If you plan to install the
GitHub CLI (Section 7), `gh auth login` sets all of this up for you automatically
— no extra steps needed. Skip ahead to Section 7 if you'd rather do it that way.

**Otherwise, turn on Git's own credential storage.** Pick one of these:

#### Option A — Store permanently (easiest)

Saves your token to a file in your home folder so you never have to type it again:

```bash
git config --global credential.helper store
```

The **next** time Git prompts you for your username and token (on your next push
or pull), it saves them and won't ask again.

> ⚠️ **Note:** This writes the token in **plain text** to `~/.git-credentials`.
> That's fine on your own personal machine, but avoid it on shared or public
> computers. For stronger security, use Option B or the Git Credential Manager
> below.

#### Option B — Remember temporarily (more secure)

Keeps your token in memory for a set time (here, 1 hour = 3600 seconds), then
forgets it:

```bash
git config --global credential.helper 'cache --timeout=3600'
```

Nothing is written to disk — good for shared machines. You'll re-enter the token
once per session.

### ✅ Check for success

After setting a helper, do one push or pull and enter your username + token when
prompted. Then run another Git command that talks to GitHub (e.g. `git pull`):

- With **Option A**, it should **not** ask again — ever.
- With **Option B**, it won't ask again until the timeout expires.

You can also confirm which helper is active:

```bash
git config --global credential.helper
```

> 💡 **Even more secure:** the
> [Git Credential Manager](https://github.com/git-ecosystem/git-credential-manager)
> stores credentials in your operating system's secure vault instead of a plain
> file. It's the most robust option, but the choices above are enough to stop the
> repeated prompts.

---

## 5. Install Git

Git is the tool that actually runs every command in the next section.

### First, check if you already have it

Many WSL / Ubuntu setups come with Git **already installed**. Before installing
anything, check the version:

```bash
git --version
```

- ✅ If you see a version number (for example `git version 2.43.0`), Git is already
  installed — **skip the install step below** and jump straight to
  [Introduce yourself](#introduce-yourself).
- ❌ If you instead see `command not found` (or a similar error), continue to the
  install step below.

### Install Git (only if the check above failed)

On **WSL / Ubuntu / Debian**, install it with:

```bash
sudo apt update && sudo apt install git -y
```

> 💡 If asked for your password, type your Linux login password (nothing appears as
> you type).
>
> *(On Windows outside WSL, download the installer from
> [git-scm.com/downloads](https://git-scm.com/downloads) instead. On macOS, run
> `brew install git`.)*

Then confirm it installed correctly by running `git --version` again — you should
now see a version number.

### Introduce yourself

Tell Git who you are — this stamps your name on every commit:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

---

## 6. Common Git Commands

### Everyday workflow

```bash
# 📥 CLONE — download a repo to your computer
git clone https://github.com/username/repo-name.git

# 🔍 STATUS — see what has changed
git status

# ➕ ADD — stage files for the next commit
git add filename.txt      # a specific file
git add .                 # everything that changed

# 💾 COMMIT — save a snapshot with a message
git commit -m "Describe what you changed"

# 📤 PUSH — send your commits up to GitHub
git push

# 📥 PULL — bring down changes others have made
git pull
```

### Working with branches

Branches let you work on a feature without touching the main code until you're
ready.

```bash
# List all branches (the * marks your current one)
git branch

# Create a new branch AND switch to it
git checkout -b my-new-feature

# Switch between existing branches
git checkout main

# Push a new branch to GitHub for the first time
git push -u origin my-new-feature
```

### A typical day, start to finish

```bash
git pull                              # 1. get the latest changes
git checkout -b add-login-page        # 2. start a feature branch
# ... edit your files ...
git add .                             # 3. stage your work
git commit -m "Add login page"        # 4. save a snapshot
git push -u origin add-login-page     # 5. share it on GitHub
```

Then open a **Pull Request** on GitHub to ask teammates to review and merge your
branch into the main project.

### Handy reference

| Command | What it does |
|---------|--------------|
| `git clone <url>` | Copy a remote repo to your machine |
| `git status` | Show what's changed |
| `git add <file>` | Stage changes for commit |
| `git commit -m "msg"` | Save a snapshot with a message |
| `git push` | Upload commits to GitHub |
| `git pull` | Download and merge others' changes |
| `git branch` | List branches |
| `git checkout -b <name>` | Create and switch to a new branch |
| `git log --oneline` | View a compact history of commits |

---

## 7. Install the GitHub CLI (gh)

> **What is `gh`?** `gh` is GitHub's official command-line tool. It lets you do
> GitHub tasks — like creating **Pull Requests (PRs)** — directly from your terminal,
> without opening the website. (A Pull Request is how you propose your changes for
> others to review and merge.)

### Install it

These steps install `gh` on **Ubuntu / WSL / Debian Linux**. (On Windows or macOS,
see the [official install guide](https://github.com/cli/cli#installation) instead.)

1. Open your terminal.
2. Copy and paste the **entire block below** (it's several lines that run together),
   then press **Enter**. If asked for your password, type your Linux login password
   (nothing appears as you type):

   ```bash
   sudo mkdir -p -m 755 /etc/apt/keyrings \
   && wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
   && sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
   && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
   && sudo apt update \
   && sudo apt install gh -y
   ```

   > This adds GitHub's official software source and then installs `gh`. It's the
   > method recommended in GitHub's own documentation.

### Log in to your account

Once it finishes, connect `gh` to your GitHub account:

```bash
gh auth login
```

Answer the questions using the **arrow keys** and **Enter**:

- *What account?* → **GitHub.com**
- *Preferred protocol?* → **HTTPS**
- *Authenticate Git with your GitHub credentials?* → **Yes**
- *How would you like to authenticate?* → **Login with a web browser**
- It will show a **one-time code** (like `AB12-CD34`). Copy it, press Enter, and your
  browser will open. Paste the code and approve the login.

> 💡 Logging in this way also lets Git push and pull over HTTPS **without** a Personal
> Access Token — a convenient alternative to Section 4.

### ✅ Check for success

Confirm `gh` is installed:

```bash
gh --version
```

Expected output (version number may differ):

```text
gh version 2.63.2 (2024-...)
https://github.com/cli/cli/releases/latest
```

Confirm you're logged in:

```bash
gh auth status
```

You should see something like:

```text
github.com
  ✓ Logged in to github.com account yourusername
  - Active account: true
```

### Create a Pull Request from the terminal

Once you've pushed a branch (see Section 6), you can open a PR without leaving the
terminal:

```bash
gh pr create
```

`gh` will prompt you for a title and description, then give you a link to the new
Pull Request on GitHub. 🎉

---

## You're Ready! 🎉

You now know how to create an account, set up a repository, authenticate with a
token, and use the core Git commands. The best way to learn is by doing — create
a practice repo and experiment freely.

> 📚 **Keep learning:**
> - [GitHub Docs](https://docs.github.com/en) — official guides
> - [GitHub Skills](https://skills.github.com/) — free interactive courses
> - [Pro Git book](https://git-scm.com/book/en/v2) — free, in-depth Git reference
