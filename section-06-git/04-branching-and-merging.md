# 04 — Branching and Merging

## Learning Objectives

By the end of this page, you will be able to:

- Explain what a branch is and why branches are central to Git workflow
- Create, switch, list, and delete branches
- Merge branches with fast-forward and three-way merges
- Identify and resolve merge conflicts
- Use `git stash` to temporarily shelve uncommitted work
- Open and merge a Pull Request on GitHub
- Follow a feature branch workflow

---

## What Is a Branch?

A **branch** is a lightweight, moveable pointer to a specific commit.

When you make commits on `main`, the `main` pointer moves forward with each commit:

```
A ──▶ B ──▶ C ──▶ D
                  ↑
                 main (pointer)
                  ↑
                 HEAD (you are here)
```

When you create a branch, you create a new pointer that starts at the same commit:

```
A ──▶ B ──▶ C ──▶ D
                  ↑
                 main
                  ↑
                feature  ← new branch, same commit
```

Now you can commit on `feature` — the new pointer moves forward while `main` stays put:

```
A ──▶ B ──▶ C ──▶ D ──▶ E ──▶ F
                  ↑              ↑
                 main           feature
```

`main` is untouched. You can work on `feature` freely — break things, experiment, start over — without affecting the stable code on `main`.

> **Analogy:** A branch is like a parallel timeline in a science fiction story. You and a copy of yourself split off at the same point. Each timeline diverges — you do different things. Later you can **merge** the timelines back together, combining both sets of events.

---

## Creating and Switching Branches

### Modern Syntax (Git 2.23+)

```bash
git switch -c feature-login     # create AND switch to new branch
git switch main                 # switch to an existing branch
git switch -                    # switch to the previous branch (handy!)
```

### Traditional Syntax (also works)

```bash
git checkout -b feature-login   # create and switch
git checkout main               # switch
```

### Listing Branches

```bash
git branch                      # list local branches (* = current)
git branch -a                   # list local AND remote branches
git branch -v                   # list with latest commit on each
```

### Deleting Branches

```bash
git branch -d feature-login     # delete (safe — refuses if unmerged)
git branch -D feature-login     # force delete (even if unmerged — be careful)

# Delete a remote branch:
git push origin --delete feature-login
```

---

## Making Commits on a Branch

```bash
# Start from main, create feature branch
git switch main
git switch -c feature-dark-mode

# Make changes
echo ".dark { background: #111; color: #eee; }" >> style.css

git add style.css
git commit -m "Add dark mode CSS variables"

# More work
# ... edit JavaScript ...
git add script.js
git commit -m "Add dark mode toggle button logic"

git log --oneline
# 9f3b2a1 (HEAD -> feature-dark-mode) Add dark mode toggle button logic
# 7c4d1e8 Add dark mode CSS variables
# a3f4b9c (main) Last commit on main
```

---

## `git merge` — Combining Branches

When your feature is ready, merge it back into `main`.

### Fast-Forward Merge

If `main` hasn't moved since you branched, Git can simply move the `main` pointer forward — no merge commit needed:

```
Before:  A ─ B ─ C (main)
                   └─ D ─ E (feature)

git switch main
git merge feature

After:   A ─ B ─ C ─ D ─ E (main, feature)
```

```bash
git switch main
git merge feature-dark-mode
# Updating a3f4b9c..9f3b2a1
# Fast-forward
#  script.js | 20 ++++++++++++++++++++
#  style.css | 5 +++++
#  2 files changed, 25 insertions(+)
```

### Three-Way Merge

If `main` has moved forward since you branched (someone else pushed to main), Git creates a **merge commit** — a special commit with two parents:

```
Before:  A ─ B ─ C ─ F (main)
                   └─ D ─ E (feature)

After merge:  A ─ B ─ C ─ F ─ M (main)
                           └─ D ─ E ─┘
              M = merge commit (has two parents: F and E)
```

```bash
git switch main
git merge feature-dark-mode
# Merge made by the 'ort' strategy.
# Auto-merging style.css
# MERGE_COMMIT_EDITMSG: Merge branch 'feature-dark-mode'
```

---

## Merge Conflicts

A **merge conflict** occurs when both branches changed the same part of the same file. Git can't automatically decide which version to keep — you must resolve it manually.

### When Conflicts Happen

```bash
git merge feature-dark-mode
# Auto-merging style.css
# CONFLICT (content): Merge conflict in style.css
# Automatic merge failed; fix conflicts and then commit the result.
```

### Conflict Markers

Git adds markers to the conflicted file:

```css
<<<<<<< HEAD
body {
  background-color: white;
  color: #333;
}
=======
body {
  background-color: #1a1a2e;
  color: #e0e0ff;
}
>>>>>>> feature-dark-mode
```

- `<<<<<<< HEAD` — start of your current branch's version
- `=======` — divider between the two versions
- `>>>>>>> feature-dark-mode` — start of the incoming branch's version

### Resolving Conflicts

**Step 1:** Open the file. Decide what the final version should look like. Remove ALL conflict markers:

```css
/* Resolved: use the dark theme from the feature branch */
body {
  background-color: #1a1a2e;
  color: #e0e0ff;
}
```

**Step 2:** Stage the resolved file:

```bash
git add style.css
```

**Step 3:** Complete the merge:

```bash
git commit   # Git pre-fills the merge commit message — just save and close
```

### VS Code Merge Editor

VS Code has an excellent visual merge editor. When a conflict exists, clicking a file opens a three-panel view:
- **Current change** (HEAD) on the left
- **Incoming change** (other branch) on the right
- **Result** (what you're creating) at the bottom

Use the "Accept Current," "Accept Incoming," "Accept Both," or edit the result directly. This is much easier than resolving conflicts in raw text.

### Checking for Conflicts

```bash
git status          # shows files with conflicts as "both modified"
git diff            # shows conflict markers in all conflicted files
```

### Aborting a Merge

If you want to back out of a conflicted merge and start over:

```bash
git merge --abort
```

---

## `git stash` — Shelving Uncommitted Work

Sometimes you're mid-feature and need to switch context urgently (a bug on `main`, a colleague needs you to look at something). You can't commit unfinished code, and you can't switch branches with uncommitted changes without losing them.

`git stash` saves your uncommitted changes to a temporary stack:

```bash
git stash                       # save and clean working directory
git stash push -m "WIP: dark mode toggle"   # save with a description

git switch main                 # now safe to switch
# ... fix the bug ...
git switch feature-dark-mode    # come back

git stash pop                   # restore stashed changes and remove from stack
# OR:
git stash apply                 # restore but keep on stack
```

### Managing Multiple Stashes

```bash
git stash list                  # see all stashes
# stash@{0}: WIP: dark mode toggle
# stash@{1}: WIP: user profile

git stash pop stash@{1}         # restore a specific stash
git stash drop stash@{0}        # delete a specific stash
git stash clear                 # delete all stashes
```

---

## Pull Requests: Code Review on GitHub

A **Pull Request (PR)** is a GitHub feature (not a Git feature) that:
1. Proposes merging one branch into another
2. Shows a diff of all changes
3. Allows team members to comment, request changes, or approve
4. Provides a place to discuss the implementation before it reaches `main`

### Creating a Pull Request

```bash
# 1. Create and work on a feature branch
git switch -c feature-contact-form
# ... make commits ...

# 2. Push the branch to GitHub
git push -u origin feature-contact-form
```

3. GitHub shows a banner: **"Compare & pull request"** — click it
4. Fill in:
   - **Title:** brief description of the change
   - **Description:** what changed, why, how to test it, screenshots for UI changes
5. Click **Create pull request**

### The PR Lifecycle

```
Open PR → Review → Request Changes → Update → Approve → Merge → Delete Branch
```

- Reviewers can leave **line comments** on specific code
- You push new commits to the same branch — the PR updates automatically
- Once approved, **Merge pull request** merges into the base branch
- **Delete branch** cleans up the feature branch on GitHub

### PR Description Template (Good Practice)

```markdown
## What changed
Brief description of the changes.

## Why
What problem does this solve? Link to issue: closes #42

## How to test
1. Open the contact page
2. Submit the form with an invalid email
3. Verify error message appears inline

## Screenshots
[attach screenshots for UI changes]

## Checklist
- [x] Tested on mobile
- [x] No console errors
- [x] README updated if needed
```

---

## Branching Workflows

### Feature Branch Workflow

The most common pattern for small-to-medium teams:

```
main (production-ready code)
  │
  ├─ feature/contact-form   (one developer, short-lived)
  ├─ feature/dark-mode      (another developer)
  └─ bugfix/login-redirect  (urgent fix)
```

**Rules:**
1. `main` is always deployable — never commit broken code directly to it
2. Every change comes from a branch via a Pull Request
3. Branches are short-lived (days, not weeks)
4. Delete branches after merging

### Git Flow (More Complex Teams)

For projects with formal release cycles:

```
main        (production — only tagged releases)
develop     (integration branch)
  │
  ├─ feature/*   (new features, branch from develop)
  ├─ release/*   (release preparation, from develop)
  └─ hotfix/*    (urgent production fixes, from main)
```

### GitHub Flow (Simple and Modern)

GitHub's own workflow — essentially Feature Branch Workflow:
1. Branch from `main`
2. Add commits
3. Open PR
4. Review and discuss
5. Merge to `main` and deploy

---

## Common Branch Commands Quick Reference

```bash
# Create and switch
git switch -c feature-name

# See what branch you're on
git branch
git status   # also shows current branch

# See divergence
git log --oneline --graph --all

# Update your branch with latest main
git switch feature-name
git merge main          # or: git rebase main

# Merge feature into main
git switch main
git merge feature-name

# Clean up after merging
git branch -d feature-name
git push origin --delete feature-name
```

**Further Reading:**
- [Git Branching — Pro Git Book](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)
- [Merge Conflicts — Atlassian](https://www.atlassian.com/git/tutorials/using-branches/merge-conflicts)
- [Understanding the GitHub Flow](https://docs.github.com/en/get-started/using-github/github-flow)
- [Git Feature Branch Workflow — Atlassian](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow)

---

## Check Your Understanding

1. What is a Git branch, technically? (What does it actually store?)
2. When does a fast-forward merge occur? When does a three-way merge occur?
3. What causes a merge conflict, and what is the resolution process?
4. What does `git stash` do, and when would you use it?
5. What is a Pull Request? Is it a Git feature or a GitHub feature?

---

*Previous: [03 — Working with GitHub](03-working-with-github.md)*
*Next: [05 — Quiz and Checkpoints](05-quiz-and-checkpoints.md)*
