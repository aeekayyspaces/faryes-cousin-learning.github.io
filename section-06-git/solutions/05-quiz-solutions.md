# Solutions — 05 Section Quiz

Answer key for [05 — Quiz and Checkpoints](../05-quiz-and-checkpoints.md).

---

## Part 1: Multiple Choice — Answer Key

| # | Answer | Explanation |
|---|--------|-------------|
| 1 | **B — Every developer has a full copy of the repository and its history** | In a distributed VCS, each clone is a complete, self-sufficient repository with full history. Developers can commit, branch, diff, and inspect logs entirely offline. Centralized systems (SVN, CVS) keep history only on the central server — if it goes down, operations are blocked. |
| 2 | **C — Git has never seen this file** | "Untracked" means the file exists in the working directory but has never been added to Git's tracking. It is not in the staging area and not in the repository. Git shows it so you know it exists but is being ignored by version control. |
| 3 | **B — Staging area (ready to be committed)** | `git add` moves changes from the working directory to the staging area (also called the index). The file is not yet in the repository — it's in the preparation zone waiting for `git commit` to save it permanently. |
| 4 | **B — Saves a snapshot of the staged files into the repository** | `git commit` takes whatever is in the staging area and saves it as a permanent snapshot (commit) in the repository. It does NOT stage anything — only files explicitly added via `git add` are included. It does not push to any remote. |
| 5 | **C — `.gitignore`** | `.gitignore` is the file Git reads for patterns of files/directories to exclude from tracking. `.gitconfig` is the global Git configuration file. The other options are not valid file names that Git recognizes. |
| 6 | **C — The remote has commits your local repository does not have** | Git requires that your local history include all remote commits before you can push new ones. Someone pushed commits to the remote after your last `git pull`. Solution: `git pull` to integrate their changes, then `git push`. |
| 7 | **B — `git fetch` downloads without merging; `git pull` downloads and merges** | `git fetch` updates your remote-tracking branches (like `origin/main`) but leaves your local branch (`main`) unchanged. `git pull` is effectively `git fetch` + `git merge` — it downloads and immediately merges the remote changes into your current branch. |
| 8 | **C — A new branch is created at the current commit, and you switch to it** | `git switch -c feature-login` is equivalent to `git branch feature-login` (create) + `git switch feature-login` (switch). The new branch starts at whatever commit `HEAD` currently points to — in this case, the latest commit on `main`. |
| 9 | **B — The target branch has not moved since the feature branch was created** | A fast-forward merge is possible when the branch you're merging into (e.g., `main`) has no new commits since the feature branch was created. Git can simply move the `main` pointer forward to the tip of the feature branch — no merge commit needed. |
| 10 | **C — Edit to desired content, remove conflict markers, `git add`, `git commit`** | The conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) must be completely removed. You decide the final content, save the file, stage it with `git add`, and complete the merge with `git commit`. Option B is wrong — Git will not auto-resolve; the conflict markers in the file would be committed as-is. |
| 11 | **B — Need to switch branches but have uncommitted work not ready to commit** | `git stash` saves your current working directory and staging area changes to a temporary stack and cleans up so you can safely switch branches. Use `git stash pop` to restore your work afterward. It does not delete changes (A), revert commits (C), or download changes (D). |
| 12 | **C — A pointer to the commit you currently have checked out** | `HEAD` is a special ref that tracks where you currently are in the repository. Normally it points to the tip of the current branch. When you switch branches, `HEAD` moves. When you commit, `HEAD` (via the branch it points to) moves forward to the new commit. |
| 13 | **B — `git log --oneline --graph`** | `--oneline` compresses each commit to one line; `--graph` draws an ASCII art visualization of the branch and merge structure. Adding `--all` shows all branches (not just the current one). |
| 14 | **B — A GitHub feature that proposes merging one branch into another** | A Pull Request is a GitHub (not Git) feature. It proposes merging a source branch into a target branch, shows a diff, allows comments and reviews, and provides a merge button. It is not a Git command — you can't do PRs without a hosting platform like GitHub. |
| 15 | **C — Contains thousands of non-your-code files regeneratable with `npm install`** | `node_modules/` typically contains tens of thousands of files totaling hundreds of megabytes. These are third-party dependencies, not your code, and are listed in `package.json` — anyone can regenerate them with `npm install`. Committing them would massively bloat the repository. |

---

## Part 2: Short Answer — Suggested Responses

### 1. The Three Git Areas — Original Analogy

*(The course used a letter-writing analogy. Here are others — your answer should capture the same concept.)*

**Option: Film photography**
- **Working directory** = the photographer taking shots — in the moment, messy, not yet permanent
- **Staging area** = the contact sheet — you choose which frames to print
- **Repository** = the finished prints in an album — permanent, organized, retrievable

**Option: A restaurant kitchen**
- **Working directory** = ingredients being prepped on counters — raw, in progress
- **Staging area** = the pass — dishes are plated and waiting to be sent out
- **Repository** = orders delivered to the table — completed, recorded, part of the service record

**Key concepts any analogy should capture:**
1. The working directory is your active workspace — can be messy, not yet saved
2. The staging area is selective — you choose exactly what goes into the next snapshot
3. The repository is permanent — once committed, it's in the record

---

### 2. `git revert` vs `git restore`

**`git restore <file>`** discards uncommitted changes in the working directory (or staging area with `--staged`). It affects only files that haven't been committed. The changes are gone forever — there's no undo. Used when you made a mistake and want to discard it before committing.

**`git revert <commit-hash>`** creates a *new commit* that undoes the changes introduced by a specific past commit. The original commit stays in history — nothing is rewritten. Used to undo committed changes, especially on shared branches where rewriting history would cause problems for your team.

**When to use each:**
- `git restore` — "I haven't committed this yet and I want to throw it away"
- `git revert` — "I committed and pushed this change, and I need to undo it safely"

---

### 3. The Case for the Staging Area

The staging area gives you **precision control** over what goes into each commit.

In real development, you often have multiple unrelated changes in progress at once:
- A bug fix you were working on
- A new feature half-done
- A refactor you started
- A whitespace cleanup you made while editing

Without the staging area, you'd be forced to commit all of these together, making your history harder to understand and harder to revert selectively.

With the staging area:
```bash
# Commit only the bug fix, leave the rest for later
git add src/auth/login.js
git commit -m "Fix login redirect loop on mobile"

# Later, commit the feature
git add src/features/dark-mode.js src/styles/dark.css
git commit -m "Add dark mode toggle"
```

The staging area also enables `git add -p` — interactive staging where you can choose individual **hunks** (sections) of a file, committing some changes while leaving others unstaged. This is impossible if commit = "everything that changed."

Clean, focused commits make `git log`, `git bisect`, and `git revert` dramatically more powerful.

---

### 4. Merge Conflicts — Cause, Signal, Resolution

**Cause:** A merge conflict occurs when two branches have each modified the same lines of the same file since they diverged. Git cannot automatically decide which version is correct — it requires human judgment.

**How Git signals a conflict:**
- `git merge` prints a message: `CONFLICT (content): Merge conflict in filename`
- The merge is paused — not completed
- `git status` shows affected files as "both modified"
- Git inserts conflict markers directly into the file content

**The conflict markers:**
```
<<<<<<< HEAD          ← your branch's version begins
your changes here
=======               ← separator
incoming changes here
>>>>>>> other-branch  ← incoming branch's version ends
```

**Resolution process:**
1. Open each conflicted file
2. Decide what the final content should be (keep one version, keep both, write something new)
3. Remove ALL conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) — the file must be valid code
4. `git add <file>` — mark the conflict as resolved
5. Repeat for all conflicted files
6. `git commit` — completes the merge (Git pre-fills the message)

---

### 5. Feature Branch Workflow — 5–6 Steps

1. **Pull `main` to get latest:** `git pull origin main` — start from the most recent stable code
2. **Create a feature branch:** `git switch -c feature/user-authentication` — isolated workspace
3. **Make commits:** Work freely on the branch — commit often with clear messages. Breaking things here doesn't affect `main`
4. **Push and open a Pull Request:** `git push -u origin feature/user-authentication`, then open a PR on GitHub with a description of what changed and why
5. **Code review:** Teammates review the diff, leave comments, suggest changes. You push fixes to the same branch — the PR updates automatically
6. **Merge and clean up:** PR is approved → merge into `main` → delete the feature branch

**Why it's better than committing directly to `main`:**

- **Stability:** `main` always contains working code — it can be deployed at any time. Half-finished features never land there
- **Isolation:** Bugs introduced in your feature don't affect other developers until the feature is complete and reviewed
- **Review:** PRs ensure a second set of eyes catches bugs, style issues, and design problems before they reach production
- **Reversibility:** If a feature causes problems, it can be reverted as one clean unit (reverting the merge commit), not as a tangle of commits mixed with other work

---

*Back to quiz: [05 — Quiz and Checkpoints](../05-quiz-and-checkpoints.md)*
