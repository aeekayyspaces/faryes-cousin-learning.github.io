# 03 — Working with GitHub: push, pull, clone

## Learning Objectives

By the end of this page, you will be able to:

- Explain the difference between Git and GitHub
- Create a GitHub account and a remote repository
- Connect a local repository to a remote with `git remote`
- Push local commits to GitHub with `git push`
- Pull remote changes with `git pull` and `git fetch`
- Copy an existing repository with `git clone`
- Set up SSH key authentication
- Write a good `README.md`
- Understand the basics of the GitHub interface

---

## Git vs GitHub

This distinction matters and confuses many beginners:

| | Git | GitHub |
|---|-----|--------|
| **What it is** | Software you install locally | A website (cloud service) |
| **Who made it** | Linus Torvalds (2005) | GitHub Inc. (2008), now owned by Microsoft |
| **What it does** | Tracks history, manages branches locally | Hosts repositories online; adds pull requests, issues, CI/CD, project management |
| **Works without the other?** | Yes — Git works fully offline | No — GitHub stores Git repositories |
| **Alternatives** | Mercurial, Fossil | GitLab, Bitbucket, Gitea (self-hosted) |

> **Analogy:** Git is Microsoft Word — it's the software that creates and edits documents. GitHub is Google Drive — it's the cloud service where you store and share those documents. You can use Word without Google Drive (local files), but Google Drive stores Word documents.

---

## Setting Up GitHub

### Create an Account

Go to [github.com](https://github.com) and sign up with:
- A username (this is public — choose professionally)
- Your email address
- A secure password

### Creating a New Repository on GitHub

1. Click the **+** icon (top right) → **New repository**
2. Fill in:
   - **Repository name** — use lowercase with hyphens: `my-website`
   - **Description** — optional but helpful
   - **Public** — anyone can see it; or **Private** — only you and collaborators
   - ✅ **Add a README file** (or leave unchecked if you have an existing local repo)
3. Click **Create repository**

---

## Authentication: SSH Keys (Recommended)

When you push code to GitHub, GitHub needs to verify you're authorized. Two methods:

### HTTPS (Simple but requires token)

GitHub removed password authentication in 2021. HTTPS now requires a **Personal Access Token (PAT)**:
1. GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Generate token with `repo` scope
3. Use this token as your password when prompted

### SSH Keys (Recommended — Set Once, Works Forever)

SSH keys use a cryptographic key pair. You keep the private key; GitHub gets the public key.

**Step 1: Generate a key pair**

```bash
ssh-keygen -t ed25519 -C "you@example.com"
# Press Enter to accept the default file location (~/.ssh/id_ed25519)
# Optionally set a passphrase for extra security
```

This creates two files:
- `~/.ssh/id_ed25519` — your **private key** (never share this)
- `~/.ssh/id_ed25519.pub` — your **public key** (share this with GitHub)

**Step 2: Copy your public key**

```bash
cat ~/.ssh/id_ed25519.pub
# ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... you@example.com
```

Copy the entire output.

**Step 3: Add to GitHub**

1. GitHub → Settings → SSH and GPG keys → **New SSH key**
2. Title: `My Laptop` (or whatever helps you identify the machine)
3. Paste the public key
4. Save

**Step 4: Test the connection**

```bash
ssh -T git@github.com
# Hi yourusername! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## Connecting a Local Repo to GitHub

### Scenario A: You Already Have a Local Repo

After creating an empty repo on GitHub (no README), connect them:

```bash
# Add GitHub as the "origin" remote
git remote add origin git@github.com:yourusername/my-website.git

# Rename your default branch to "main" if needed
git branch -M main

# Push and set origin/main as the upstream tracking branch
git push -u origin main
```

After `-u origin main`, future pushes can just use `git push` — no need to specify where.

### Scenario B: Start from Scratch on GitHub

Create the repo on GitHub with a README, then clone it:

```bash
git clone git@github.com:yourusername/my-website.git
cd my-website
```

---

## Managing Remotes

```bash
git remote -v               # list all remotes and their URLs
# origin  git@github.com:yourusername/my-website.git (fetch)
# origin  git@github.com:yourusername/my-website.git (push)

git remote add upstream git@github.com:original-author/project.git
                            # add a second remote (useful for forks)

git remote rename origin github    # rename a remote
git remote remove upstream         # remove a remote
git remote set-url origin git@github.com:yourusername/new-name.git
                            # change a remote's URL
```

---

## `git push` — Sending Commits to GitHub

After committing locally, push to the remote:

```bash
git push                    # push current branch to its upstream (after -u is set)
git push origin main        # explicit: push main branch to origin
git push -u origin feature  # push new branch and set it as upstream
git push --all              # push all branches
```

### What Push Does

```
Local: main ──▶ a3f4b9c ──▶ 7e2c1d4 (HEAD)

Remote: main ──▶ a3f4b9c         ← behind by one commit

git push →

Remote: main ──▶ a3f4b9c ──▶ 7e2c1d4 (synced)
```

### Push Rejection

If the remote has commits you don't have locally, Git rejects the push:

```bash
git push
# ! [rejected] main -> main (fetch first)
# error: failed to push some refs
# hint: Updates were rejected because the remote contains work that you do not have locally.
```

You must pull first, integrate the remote changes, then push again.

---

## `git fetch` — Download Without Merging

`git fetch` downloads commits from the remote to your local repository but does NOT change your working files or current branch:

```bash
git fetch                   # fetch all remotes
git fetch origin            # fetch from origin
git fetch origin main       # fetch a specific branch

# After fetching, compare:
git log HEAD..origin/main   # commits on remote not yet on local
git diff HEAD origin/main   # see what changed
```

> **Analogy:** `git fetch` is like checking your mailbox — you see what arrived without opening any letters yet. `git pull` opens the letters and puts them on your desk immediately.

---

## `git pull` — Download and Merge

`git pull` is shorthand for `git fetch` + `git merge`:

```bash
git pull                    # pull from tracked upstream branch
git pull origin main        # pull a specific branch
```

### Fast-Forward Pull

If your local branch has no new commits, the pull is a simple **fast-forward** — your branch pointer just moves forward:

```
Before:  local main: A ─ B
         remote main: A ─ B ─ C ─ D

After git pull:  local main: A ─ B ─ C ─ D
```

### Pull with Diverged History

If both local and remote have new commits, a **merge commit** is created:

```
Before:  local main:  A ─ B ─ E
         remote main: A ─ B ─ C ─ D

After git pull:
         local main: A ─ B ─ E ─ M   (M = merge commit)
                          └ C ─ D ─┘
```

### Pull with Rebase (Cleaner History)

```bash
git pull --rebase           # replays local commits on top of remote commits
```

Result: a linear history without merge commits. Many teams prefer this for cleaner logs.

---

## `git clone` — Copying a Repository

Clone downloads a complete copy of a repository, including all history and branches:

```bash
git clone git@github.com:yourusername/my-website.git
# Clones into ./my-website/

git clone git@github.com:yourusername/my-website.git my-project
# Clones into ./my-project/ (custom directory name)

git clone --depth 1 git@github.com:large/repo.git
# Shallow clone — only latest commit (faster for large repos you just want to read)
```

After cloning, `origin` is automatically set to the source URL.

---

## The Typical Daily Workflow

```bash
# Morning: get latest changes from team
git pull

# Work on your feature
# ... edit files ...
git add .
git commit -m "Add user profile page"

# More work
# ... edit files ...
git add src/profile.css
git commit -m "Style profile page with responsive layout"

# End of day: share your work
git push
```

---

## Writing a Good README.md

A `README.md` is the first thing anyone sees when they visit your repository. It's rendered as formatted HTML on GitHub.

```markdown
# My Website

A personal portfolio website built with HTML, CSS, and JavaScript.

## Features

- Responsive design (mobile-first)
- Dark mode toggle
- Contact form with validation

## Getting Started

### Prerequisites

- A web browser
- A text editor (VS Code recommended)

### Running Locally

1. Clone the repository:
   ```bash
   git clone git@github.com:yourusername/my-website.git
   cd my-website
   ```
2. Open `index.html` in your browser — no build step needed.

## Project Structure

```
my-website/
├── index.html      # Main HTML file
├── style.css       # Global styles
├── script.js       # Main JavaScript
└── README.md       # This file
```

## Contributing

Pull requests are welcome. For major changes, please open an issue first.

## License

[MIT](LICENSE)
```

---

## GitHub Interface Overview

When you visit a repository on GitHub, you'll see:

| Tab | Purpose |
|-----|---------|
| **Code** | Browse files, view README, download/clone |
| **Issues** | Bug reports, feature requests, tasks |
| **Pull Requests** | Code reviews, merging branches |
| **Actions** | CI/CD workflows (automated tests, deployments) |
| **Projects** | Kanban boards for project management |
| **Wiki** | Documentation pages |
| **Settings** | Collaborators, branch protection, webhooks |

### Key UI Elements

- **Commits** — view the full commit history
- **Branches** — see all branches and their status
- **Releases** — tagged versions of the project
- **Fork** — create your own copy of someone else's repo
- **Star** — bookmark a project you like (also a popularity signal)
- **Watch** — get notifications for activity

**Further Reading:**
- [Getting Started with GitHub — GitHub Docs](https://docs.github.com/en/get-started)
- [SSH Key Setup — GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [About READMEs — GitHub Docs](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes)

---

## Check Your Understanding

1. What is the difference between `git fetch` and `git pull`?
2. Why is SSH authentication recommended over HTTPS for GitHub?
3. What does `git push -u origin main` do that plain `git push` does not?
4. You clone a repository and make commits. Your colleague also pushes commits to the same repository. When you try to push, Git rejects it. What do you do?
5. What does `origin` represent in Git?

---

*Previous: [02 — Git Basics](02-git-basics.md)*
*Next: [04 — Branching and Merging](04-branching-and-merging.md)*
