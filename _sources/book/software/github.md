# Git & GitHub

[Git](https://git-scm.com/) is a version control system that tracks changes to files over time, letting you save snapshots of your project, undo mistakes, and collaborate without overwriting each other's work. [GitHub](https://github.com/) is a cloud platform for hosting Git repositories, and it doubles as a free static web host — which is how we will deploy the web mapping projects you build in this course (via **GitHub Pages**).

## Creating a GitHub Account

1. Go to ['https://github.com/'](https://github.com/) and sign up for a free account.
2. Use a professional username — this account, and the repositories on it, may become part of your public portfolio for employers and graduate programs.
3. (Optional but recommended) Apply for the [GitHub Student Developer Pack](https://education.github.com/pack) if you're not already enrolled in it, for extra free tools.

## Installing Git

**Download:** [https://git-scm.com/downloads](https://git-scm.com/downloads)

- **Windows:** Download and run **Git for Windows**. Accept the default options during setup unless instructed otherwise; this also installs **Git Bash**, a terminal you can use for Git commands.
- **macOS:** Git often comes pre-installed. Open Terminal and type `git --version`. If it's not installed, macOS will prompt you to install the Xcode Command Line Tools — accept the prompt. Alternatively, install via [Homebrew](https://brew.sh/): `brew install git`.
- **Linux:** Install via your package manager, e.g. `sudo apt install git` (Debian/Ubuntu) or `sudo dnf install git` (Fedora).

Confirm installation by opening a terminal (or Git Bash on Windows) and running:

```bash
git --version
```

## Connecting Git to GitHub

Configure your identity locally (this labels your commits):

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

You'll authenticate to GitHub using either:

- **GitHub CLI** (`gh auth login`) — simplest option for beginners, or
- **Personal Access Token (PAT)** used in place of a password for HTTPS remotes, or
- **SSH keys** — more advanced but avoids repeated logins

We will walk through authentication together during Lab 0 (dev environment setup); don't worry about getting this perfect before the semester starts.

## The Basic Git Workflow

| Command | What it does |
| :---- | :---- |
| `git clone <url>` | Copies a remote repository to your computer |
| `git status` | Shows what's changed since your last commit |
| `git add <file>` | Stages a file's changes to be committed |
| `git commit -m "message"` | Saves a snapshot of staged changes |
| `git push` | Uploads your commits to GitHub |
| `git pull` | Downloads changes from GitHub to your computer |

A typical session looks like: edit files in VS Code → `git add .` → `git commit -m "describe what you changed"` → `git push`.

## GitHub Pages

GitHub Pages lets you publish a static website directly from a repository, for free, at a URL like `https://yourusername.github.io/repo-name/`. We will use this to deploy web mapping projects throughout the semester — including the final Public Web GIS Inquiry project. Instructions for enabling Pages on a specific repository will be provided in the relevant lab.

## Using Git in VS Code

VS Code has Git integration built in (Source Control panel, `Ctrl+Shift+G`), and the **GitHub Pull Requests** and **GitLens** extensions (see the [VS Code](vscode) page) make it easier to visualize history and manage repositories without leaving the editor.

## Verify Your Setup

- [ ] Running `git --version` in a terminal (or Git Bash on Windows) prints a version number
- [ ] Running `git config --global user.name` and `git config --global user.email` print the values you set
- [ ] You can log into [github.com](https://github.com/) with your account
- [ ] You've successfully cloned at least one repository (we'll do this together in Lab 0, but feel free to test it early with any public repo, e.g. `git clone https://github.com/octocat/Hello-World.git`)

If `git` isn't recognized as a command on Windows, close and reopen your terminal — the installer needs a fresh terminal session to update your PATH.

## References

- [Git Documentation](https://git-scm.com/doc)
- [GitHub Docs: Pages](https://docs.github.com/en/pages)
- [GitHub Skills (interactive tutorials)](https://skills.github.com/)
