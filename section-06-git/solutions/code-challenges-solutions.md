# Solutions — Hands-On Challenges

Reference command sequences for the challenges in [05 — Quiz and Checkpoints](../05-quiz-and-checkpoints.md).

These solutions show the expected commands. Your output will differ slightly (hashes, timestamps, file contents) but the sequence should match.

---

## Challenge 1: First Repository

```bash
# 1. Create directory and initialize
mkdir git-practice
cd git-practice
git init
# Initialized empty Git repository in .../git-practice/.git/

# 2. Configure (if not already set globally)
git config user.name  "Your Name"
git config user.email "you@example.com"

# 3. Create .gitignore
cat > .gitignore << 'EOF'
.env
node_modules/
EOF

# 4. Create README.md
echo "# Git Practice Project" > README.md
echo "" >> README.md
echo "A project for learning Git version control." >> README.md

# 5. Create index.html
cat > index.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Git Practice</title>
</head>
<body>

</body>
</html>
EOF

# 6. First commit
git add .
git commit -m "Initial commit: project scaffold"
# [main (root-commit) abc1234] Initial commit: project scaffold
#  3 files changed, ...

# 7. Add h1 to index.html
# (Edit index.html to add <h1>Hello Git</h1> inside <body>)

# 8. Stage only index.html and commit
git add index.html
git commit -m "Add page heading"

# 9. Create style.css and link it
echo "body { margin: 0; font-family: Arial, sans-serif; }" > style.css
# (Also edit index.html to add <link rel="stylesheet" href="style.css"> in <head>)

# 10. Commit both files
git add style.css index.html
git commit -m "Add stylesheet and link from HTML"

# 11. Verify history
git log --oneline
# abc5678 (HEAD -> main) Add stylesheet and link from HTML
# abc4567 Add page heading
# abc1234 Initial commit: project scaffold
```

**Key concepts demonstrated:**
- `git add .` stages everything; `git add filename` stages a specific file
- You can stage multiple files in one `git add` call
- Each commit captures only what was staged at that moment

---

## Challenge 2: Branching Workflow

```bash
# Starting from the git-practice repo (or a new one)

# 1. Create and switch to feature branch
git switch -c feature-navbar
# Switched to a new branch 'feature-navbar'

# 2. Add <nav> to index.html
# (Edit index.html to add navigation inside <body>)

# 3. Commit
git add index.html
git commit -m "Add navigation bar structure"

# 4. Add CSS for navbar
# (Edit style.css to add nav styling)

# 5. Commit
git add style.css
git commit -m "Style navigation bar"

# 6. Switch back to main
git switch main
# Switched to branch 'main'

# 7. Verify main doesn't have the nav changes
cat index.html
# <body></body>  ← no <nav> element — it's only on feature-navbar

# 8. Merge the feature branch
git merge feature-navbar
# Updating abc5678..def9012
# Fast-forward
#  index.html | 7 +++++++
#  style.css  | 5 +++++

# 9. Verify the nav is now on main
cat index.html
# shows <nav> element ✓

# 10. Delete the feature branch
git branch -d feature-navbar
# Deleted branch feature-navbar (was def9012).

# Verify
git branch
# * main
```

**Key observation (step 7):** Before merging, `main`'s `index.html` does NOT have the navbar — it stayed on the branch. This demonstrates how branches are truly isolated until merged.

---

## Challenge 3: Simulating a Merge Conflict

```bash
# 1. Create new repo
mkdir conflict-practice
cd conflict-practice
git init

# 2. Create style.css
cat > style.css << 'EOF'
body {
  background: white;
  color: black;
}
EOF

# 3. Commit
git add style.css
git commit -m "Add initial styles"

# 4. Create feature branch
git switch -c feature-light-theme

# 5. Change background on feature branch
# Edit style.css: background: white; → background: #f0f0f0;
# (use your editor or: sed -i 's/background: white/background: #f0f0f0/' style.css)

# 6. Commit on feature branch
git add style.css
git commit -m "Use light gray background"

# 7. Switch back to main
git switch main

# 8. Change background on main to something different
# Edit style.css: background: white; → background: #1a1a2e;

# 9. Commit on main
git add style.css
git commit -m "Use dark background"

# 10. Try to merge — creates conflict
git merge feature-light-theme
# Auto-merging style.css
# CONFLICT (content): Merge conflict in style.css
# Automatic merge failed; fix conflicts and then commit the result.

# Open style.css — it now looks like:
# body {
# <<<<<<< HEAD
#   background: #1a1a2e;
# =======
#   background: #f0f0f0;
# >>>>>>> feature-light-theme
#   color: black;
# }

# 11. Resolve: choose dark background, remove all markers
# Edit style.css to contain:
# body {
#   background: #1a1a2e;
#   color: black;
# }

git add style.css
git commit
# [main ghi3456] Merge branch 'feature-light-theme'

# 12. Verify the history
git log --oneline --graph
# *   ghi3456 (HEAD -> main) Merge branch 'feature-light-theme'
# |\
# | * jkl7890 Use light gray background
# * | mno1234 Use dark background
# |/
# * pqr5678 Add initial styles
```

**Key insight:** The `--graph` flag makes the branch divergence and merge visible in the log. This is how real Git history looks when working on a team.

---

## Challenge 4: GitHub Push and Pull

```bash
# (After creating empty repo on GitHub at github.com/yourusername/my-portfolio)

# 1. Create local directory
mkdir my-portfolio
cd my-portfolio
git init

# 2. Create files
cat > .gitignore << 'EOF'
.env
node_modules/
.DS_Store
EOF

cat > README.md << 'EOF'
# My Portfolio

A personal portfolio website.

## About

Building this as I learn web development.
EOF

# 3. Initial commit
git add .
git commit -m "Initial commit: README and gitignore"

# 4. Connect to GitHub (use YOUR repo URL)
git remote add origin git@github.com:yourusername/my-portfolio.git
git branch -M main
git push -u origin main
# Enumerating objects: 3, done.
# Counting objects: 100% (3/3), done.
# Writing objects: 100% (3/3), ...
# Branch 'main' set up to track remote branch 'main' from 'origin'.

# 5. (On GitHub browser: edit README.md, add "## Status: In progress", commit)

# 7. Back in terminal — fetch first
git fetch
# remote: Counting objects: 3, done.
# From github.com:yourusername/my-portfolio
#    abc1234..def5678  main -> origin/main

# Check what changed
git log HEAD..origin/main --oneline
# def5678 Update README.md

git diff HEAD origin/main
# (shows the "## Status: In progress" addition)

# 8. Pull to integrate
git pull
# Updating abc1234..def5678
# Fast-forward
#  README.md | 3 +++

# 9. Verify
cat README.md
# shows "## Status: In progress" ✓
```

**Key insight (step 7):** `git fetch` updates `origin/main` but not your local `main`. The `git log HEAD..origin/main` shows commits that are on the remote but not yet on your local branch. This is how you inspect changes before merging them.

---

## Challenge 5: Pull Request Simulation

```bash
# 1–2. Create feature branch in my-portfolio
git switch main
git pull   # always start from latest main
git switch -c feature-about-section

# 3. Create about.html
cat > about.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>About Me</title>
</head>
<body>
  <header>
    <nav>
      <a href="index.html">Home</a> |
      <a href="about.html">About</a>
    </nav>
  </header>
  <main>
    <h1>About Me</h1>
    <p>I'm learning web development from the ground up.</p>
  </main>
</body>
</html>
EOF

# Commit 1: the about page
git add about.html
git commit -m "Add About Me page with bio"

# Commit 2: link it from index.html (if you have one)
# (Edit index.html to add a link to about.html)
git add index.html
git commit -m "Add navigation link to About page from homepage"

# 5. Push branch to GitHub
git push -u origin feature-about-section
# Enumerating objects: 5, done.
# ...
# Branch 'feature-about-section' set up to track remote branch 'feature-about-section' from 'origin'.

# 6–7. On GitHub:
# - You'll see "Compare & pull request" banner — click it
# - Title: "Add About Me page"
# - Description:
#   ## What changed
#   Added an About Me page (about.html) with personal bio.
#   Linked from the main navigation.
#
#   ## How to test
#   1. Open index.html
#   2. Click the "About" link in nav
#   3. Verify about.html loads with bio text
#
# - Click "Create pull request"
# - Review the "Files changed" tab — you'll see the diff of both commits
# - Click "Merge pull request" → "Confirm merge"

# 9. Back in terminal: get the merged changes
git switch main
git pull
# Fast-forward (or merge commit depending on GitHub merge method)
# about.html | 18 ++++++++++++++++++

ls
# about.html is now on local main ✓

# 10. Clean up
git branch -d feature-about-section
# Deleted branch feature-about-section

git push origin --delete feature-about-section
# Deleted remote branch feature-about-section ✓
```

**Key insight:** The PR workflow is: push branch → open PR → review → merge on GitHub → pull locally → delete branch. After merging on GitHub, the changes don't automatically appear locally — you must `git pull` to synchronize.

---

## Bonus: Resolving a Push Rejection

This scenario comes up constantly when working with a team:

```bash
# Your colleague pushed to main while you were working
git push
# ! [rejected] main -> main (fetch first)

# Solution:
git pull                   # fetch + merge their commits
# (resolve any conflicts if they arise)
git push                   # now succeeds

# Alternative with rebase (cleaner):
git pull --rebase          # puts your commits on top
git push
```

---

## Common Mistakes and Fixes

### "I committed to main instead of a feature branch"

```bash
# If not pushed yet:
git branch feature-name             # create branch at current state
git reset --hard HEAD~1             # undo the commit on main (keeps it on feature-name)
git switch feature-name             # go work on the branch
```

### "I want to see what a file looked like in a previous commit"

```bash
git show abc1234:path/to/file.js
git checkout abc1234 -- path/to/file.js   # restore it to working dir
```

### "I accidentally deleted a branch with unmerged commits"

```bash
git reflog                 # shows recent HEAD movements — find the lost commit hash
git switch -c recovered-branch abc1234   # recreate branch from that commit
```

### "git status shows modified files I don't want"

```bash
git diff                   # see what changed
git restore .              # discard ALL unstaged changes (PERMANENT)
git restore filename       # discard changes to one file
```

---

*Back to challenges: [05 — Quiz and Checkpoints](../05-quiz-and-checkpoints.md)*
