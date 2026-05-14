# 02 — Git Basics: init, add, commit, log

## Learning Objectives

By the end of this page, you will be able to:

- Install and configure Git on Windows and macOS
- Initialize a new repository with `git init`
- Understand the three areas: working directory, staging area, and repository
- Stage changes with `git add`
- Save snapshots with `git commit`
- View history with `git log`
- Check project status with `git status` and `git diff`
- Create a `.gitignore` file to exclude files from tracking

---

## Installing Git

### macOS

Git comes pre-installed on macOS. Check by running:

```bash
git --version
# git version 2.39.3 (Apple Git-145)
```

If not installed, macOS will prompt you to install Xcode Command Line Tools — follow the prompt. Or install the latest version via Homebrew:

```bash
brew install git
```

### Windows

Download the installer from [git-scm.com/download/win](https://git-scm.com/download/win).

During installation:
- **Default editor:** choose VS Code if you have it installed
- **Default branch name:** change to `main` (not `master`)
- **PATH environment:** choose "Git from the command line and 3rd-party software"
- All other options: keep defaults

After installing, open **Git Bash** (installed with Git) or **Windows Terminal**.

### Linux (Ubuntu/Debian)

```bash
sudo apt update && sudo apt install git
```

### Verify Installation

```bash
git --version
# git version 2.43.0
```

---

## First-Time Configuration

Before your first commit, tell Git who you are. This information is attached to every commit you make:

```bash
git config --global user.name  "Your Name"
git config --global user.email "you@example.com"
```

Set VS Code as the default editor (for commit messages):

```bash
git config --global core.editor "code --wait"
```

Set `main` as the default branch name for new repositories:

```bash
git config --global init.defaultBranch main
```

View all your settings:

```bash
git config --list
```

Settings are saved in `~/.gitconfig` on macOS/Linux or `C:\Users\YourName\.gitconfig` on Windows.

---

## The Three Areas of Git

This is the most important mental model to internalize. Every file in your project can be in one of three places:

```
┌─────────────────────────┐   git add    ┌──────────────────┐   git commit   ┌────────────────┐
│                         │  ──────────▶  │                  │  ────────────▶  │                │
│   Working Directory     │              │  Staging Area     │                │  Repository    │
│                         │  ◀──────────  │  (Index)         │                │  (.git folder) │
│  Files you can see      │  git restore  │                  │                │  Permanent     │
│  and edit               │              │  Changes you've  │                │  history       │
└─────────────────────────┘              │  marked for next  │                └────────────────┘
                                         │  commit           │
                                         └──────────────────┘
```

- **Working Directory:** The files on your disk — what you see in your editor
- **Staging Area (Index):** A draft of your next commit. You decide exactly what goes into the snapshot.
- **Repository:** The permanent record. Every commit here is permanent history.

> **Analogy:** Writing a letter.
> - The **working directory** is your desk — papers, drafts, revisions all over the place
> - The **staging area** is an envelope — you pick which papers to put in it
> - The **repository** is the mailbox — once mailed (committed), it's part of the permanent record

---

## `git init` — Creating a Repository

Navigate to your project folder and initialize a Git repository:

```bash
mkdir my-project
cd my-project
git init
# Initialized empty Git repository in /home/user/my-project/.git/
```

This creates a hidden `.git` folder containing the entire Git database. **Never delete or edit the `.git` folder manually.**

```bash
ls -la
# drwxr-xr-x  .git/   ← the repository database
# (empty — no files yet)
```

Git is now watching this directory. To stop tracking a project in Git, delete the `.git` folder.

---

## `git status` — What's Going On?

`git status` is the most-used Git command. Run it constantly to understand what state your project is in:

```bash
git status
# On branch main
# No commits yet
# nothing to commit (create/copy files and use "git add" to track)
```

Create a file and check again:

```bash
echo "# My Project" > README.md
git status
# On branch main
# No commits yet
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#         README.md
#
# nothing added to commit but untracked files present
```

**Status categories:**
- **Untracked:** New file Git has never seen — not in the staging area or repository
- **Staged:** Added to the staging area, ready for the next commit
- **Modified:** A tracked file has been changed but not yet staged
- **Unmodified:** A tracked file with no changes since the last commit

---

## `git add` — Staging Changes

Move changes from the working directory to the staging area:

```bash
git add README.md          # stage a specific file
git add index.html style.css   # stage multiple files
git add src/               # stage an entire directory
git add .                  # stage ALL changes in the current directory (use carefully)
git add -p                 # interactively choose which hunks to stage (advanced, powerful)
```

After staging:

```bash
git status
# On branch main
# No commits yet
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#         new file:   README.md
```

### Unstaging

If you staged something by mistake:

```bash
git restore --staged README.md    # unstage (keep changes in working dir)
```

---

## `git commit` — Saving a Snapshot

Commit saves everything in the staging area as a permanent snapshot:

```bash
git commit -m "Add README with project description"
# [main (root-commit) a3f4b9c] Add README with project description
#  1 file changed, 1 insertion(+)
#  create mode 100644 README.md
```

The `-m` flag provides the commit message inline. Without it, Git opens your configured editor for you to write the message.

### Writing Good Commit Messages

A commit message should explain **why** the change was made, not just what:

```bash
# ❌ Bad — what, not why
git commit -m "fix"
git commit -m "changes"
git commit -m "update stuff"

# ✅ Good — specific and purposeful
git commit -m "Fix login button not responding on mobile"
git commit -m "Add email validation to registration form"
git commit -m "Remove deprecated user API v1 endpoints"
```

**Conventional format** (used by many teams):

```
type(scope): description

feat: add dark mode toggle
fix: prevent form double submission
docs: update API authentication docs
style: reformat CSS with Prettier
refactor: extract validation logic into helper module
test: add unit tests for email validator
chore: upgrade dependencies
```

### Skipping the Staging Area

For tracked files, you can stage-and-commit in one step:

```bash
git commit -am "Fix typo in README"
# -a = stage all modified tracked files
# Does NOT include untracked new files
```

---

## `git log` — Viewing History

```bash
git log
# commit a3f4b9c2d8e17f5a6b0c3d4e5f6a7b8c9d0e1f2a (HEAD -> main)
# Author: Alex Rivera <alex@example.com>
# Date:   Wed May 7 14:23:01 2026 +0000
#
#     Add README with project description
```

### Useful `git log` Flags

```bash
git log --oneline           # one line per commit — compact view
git log --oneline --graph   # ASCII art branch graph
git log --oneline -10       # last 10 commits
git log --author="Alex"     # commits by a specific author
git log --since="2 weeks ago"
git log -- filename.js      # commits that touched a specific file
git log --stat              # show which files changed in each commit
```

Example of `--oneline --graph`:

```
* a3f4b9c (HEAD -> main) Add dark mode toggle
* 7e2c1d4 Fix form validation on mobile
* 5a9b3f1 Add contact page
* 2d8e6c0 Initial commit
```

### Viewing a Specific Commit

```bash
git show a3f4b9c         # full diff + metadata for a commit
git show HEAD            # the most recent commit
git show HEAD~1          # the commit before HEAD (parent)
git show HEAD~3          # 3 commits before HEAD
```

---

## `git diff` — Seeing What Changed

```bash
git diff                    # unstaged changes (working dir vs staging area)
git diff --staged           # staged changes (staging area vs last commit)
git diff HEAD               # all changes since last commit
git diff main..feature      # differences between two branches
git diff a3f4b9c..7e2c1d4   # differences between two commits
```

Output format:

```diff
diff --git a/style.css b/style.css
index 2c4b5d3..8a9f1e2 100644
--- a/style.css
+++ b/style.css
@@ -10,7 +10,7 @@
 body {
-  background-color: white;
+  background-color: #1a1a2e;
   font-family: Arial;
 }
```

Lines starting with `-` were removed; lines starting with `+` were added.

---

## `.gitignore` — Excluding Files

Some files should never be committed:
- Dependencies (`node_modules/`) — large, regeneratable, not your code
- Environment variables (`.env`) — contains secrets (passwords, API keys)
- Build output (`dist/`, `build/`) — generated from source code
- OS files (`.DS_Store`, `Thumbs.db`) — not part of the project
- Editor files (`.vscode/`, `.idea/`) — personal preferences

Create a `.gitignore` file in the repository root:

```gitignore
# Dependencies
node_modules/
vendor/

# Environment and secrets
.env
.env.local
.env.*.local

# Build output
dist/
build/
out/
.next/

# OS generated files
.DS_Store
Thumbs.db
Desktop.ini

# Editor directories and files
.vscode/
.idea/
*.swp
*.swo

# Logs
*.log
logs/

# Test coverage
coverage/
```

Once a file is in `.gitignore`, Git ignores it completely — `git status` won't show it, `git add .` won't include it.

> **Important:** If you accidentally commit a file you meant to ignore, adding it to `.gitignore` later doesn't remove it from history. Use `git rm --cached filename` to stop tracking it (removes from Git's tracking but keeps the file on disk).

**Use gitignore.io** ([gitignore.io](https://www.gitignore.io)) to generate `.gitignore` files for specific languages and frameworks automatically.

---

## Undoing Things

### Amend the Last Commit (Before Pushing)

Fix a typo in the last commit message, or add a file you forgot:

```bash
git commit --amend -m "Corrected commit message"
# or: add the file first, then:
git add forgotten-file.js
git commit --amend --no-edit   # keeps the existing message
```

> **Warning:** Never amend a commit that has already been pushed to a shared remote. This rewrites history and will cause problems for anyone who has already pulled.

### Discard Changes in Working Directory

```bash
git restore README.md        # discard unstaged changes to a file
git restore .                # discard ALL unstaged changes
```

> **Warning:** These changes are **gone forever** — not in the trash, not recoverable.

### Revert a Commit (Safe Undo for Pushed Commits)

Creates a **new commit** that undoes the changes of a previous commit — safe for shared history:

```bash
git revert a3f4b9c          # creates a new commit that undoes a3f4b9c
```

---

## A Complete First Workflow

Let's put it all together:

```bash
# 1. Create and enter a project directory
mkdir my-website
cd my-website

# 2. Initialize Git
git init

# 3. Create a .gitignore
echo "node_modules/" > .gitignore
echo ".env" >> .gitignore

# 4. Create some files
echo "# My Website" > README.md
echo "<!DOCTYPE html><html><body><h1>Hello</h1></body></html>" > index.html

# 5. Check what Git sees
git status

# 6. Stage everything
git add .

# 7. Confirm what's staged
git status

# 8. Commit
git commit -m "Initial commit: add README and homepage"

# 9. View history
git log --oneline

# 10. Edit a file
echo "/* styles */" > style.css
# ... make edits to index.html ...

# 11. Check what changed
git status
git diff

# 12. Stage and commit
git add style.css index.html
git commit -m "Add stylesheet and update homepage layout"

# 13. View history
git log --oneline
```

**Further Reading:**
- [Pro Git — Getting Started](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)
- [Pro Git — Git Basics](https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository) — The authoritative free Git book
- [Interactive Git Tutorial — Atlassian](https://www.atlassian.com/git/tutorials/setting-up-a-repository)

---

## Check Your Understanding

1. What are the three areas of Git and what does each hold?
2. What does `git add .` do versus `git add -p`?
3. Why is it important to write good commit messages?
4. What is `.gitignore` for? Why should `node_modules/` always be in it?
5. What is the difference between `git restore README.md` and `git revert <commit-hash>`?

---

*Previous: [01 — Why Version Control?](01-why-version-control.md)*
*Next: [03 — Working with GitHub](03-working-with-github.md)*
