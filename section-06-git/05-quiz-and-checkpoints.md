# 05 — Section Quiz and Checkpoints

## Section 6 Learning Objectives Review

Before taking this quiz, confirm you can:

- [ ] Explain the problem version control solves
- [ ] Describe the difference between centralized and distributed VCS
- [ ] Install and configure Git with your name, email, and default branch
- [ ] Initialize a repository, stage files, and make commits
- [ ] Interpret `git status` and `git log --oneline`
- [ ] Write a meaningful commit message
- [ ] Create and use a `.gitignore` file
- [ ] Connect a local repository to GitHub
- [ ] Push commits and pull remote changes
- [ ] Create branches, switch between them, and merge them
- [ ] Identify and resolve a merge conflict
- [ ] Use `git stash` to save work in progress
- [ ] Open a Pull Request on GitHub

---

## Part 1: Multiple Choice

Answer key: [solutions/05-quiz-solutions.md](solutions/05-quiz-solutions.md)

---

**1.** What is the fundamental difference between a centralized VCS (like SVN) and a distributed VCS (like Git)?

- A) Distributed systems require internet access for all operations; centralized do not
- B) In a distributed VCS, every developer has a full copy of the repository and its history
- C) Centralized systems support branching; distributed systems do not
- D) Distributed systems only work with text files; centralized systems handle all file types

---

**2.** After running `git init` and creating a file, `git status` shows the file as "Untracked." What does this mean?

- A) The file is tracked but has unsaved changes
- B) The file has been staged but not committed
- C) Git has never seen this file — it is not being tracked at all
- D) The file is in the repository but has been deleted from disk

---

**3.** You have made changes to `index.html` and run `git add index.html`. What area is `index.html` now in?

- A) Working directory only
- B) Staging area (ready to be committed)
- C) The repository (permanently committed)
- D) The remote repository

---

**4.** What does the following command do?

```bash
git commit -m "Fix navigation hover color"
```

- A) Stages all modified files and saves them with the given message
- B) Saves a snapshot of the **staged** files into the repository with the given message
- C) Pushes all local commits to the remote with the given message as a tag
- D) Creates a new branch named "Fix navigation hover color"

---

**5.** Which file tells Git to ignore `node_modules/` and `.env`?

- A) `.gitconfig`
- B) `git.ignore`
- C) `.gitignore`
- D) `ignore.git`

---

**6.** You run `git push` and receive:

```
! [rejected] main -> main (fetch first)
error: failed to push some refs
```

What is the most likely cause?

- A) You have uncommitted changes in your working directory
- B) Your SSH key has expired
- C) The remote has commits that your local repository does not have
- D) Your `main` branch has been deleted on the remote

---

**7.** What does `git fetch` do differently from `git pull`?

- A) `git fetch` downloads and merges remote changes; `git pull` only downloads
- B) `git fetch` downloads remote changes without merging; `git pull` downloads and merges
- C) `git fetch` works only with SSH; `git pull` works with both SSH and HTTPS
- D) There is no difference — they are aliases for the same operation

---

**8.** You are on `main` and run `git switch -c feature-login`. What happens?

- A) A new `feature-login` branch is created starting from the initial commit
- B) You switch to an existing `feature-login` branch
- C) A new `feature-login` branch is created at the current `main` commit, and you switch to it
- D) `main` is renamed to `feature-login`

---

**9.** A fast-forward merge is possible when:

- A) Both branches have new commits since they diverged
- B) The target branch (e.g., `main`) has not moved since the feature branch was created
- C) The branches share no common commit history
- D) There are merge conflicts that need to be auto-resolved

---

**10.** You see this in a file after running `git merge`:

```
<<<<<<< HEAD
color: blue;
=======
color: red;
>>>>>>> feature-theme
```

What should you do?

- A) Delete the file and recreate it from scratch
- B) Run `git commit` immediately — Git will auto-resolve it
- C) Edit the file to contain only the desired content, remove all conflict markers, then `git add` and `git commit`
- D) Run `git merge --abort` — this is an unrecoverable error

---

**11.** `git stash` is most useful when:

- A) You want to permanently delete uncommitted changes
- B) You need to switch branches but have uncommitted work you're not ready to commit
- C) You want to revert the last commit
- D) You need to download changes from a remote repository

---

**12.** What is `HEAD` in Git?

- A) The first commit ever made in the repository
- B) A pointer to the remote repository's latest commit
- C) A pointer to the commit you currently have checked out
- D) The name of the default branch

---

**13.** Which command shows a compact, one-line-per-commit history with an ASCII branch graph?

- A) `git status --verbose`
- B) `git log --oneline --graph`
- C) `git history --tree`
- D) `git show --all`

---

**14.** A Pull Request on GitHub is:

- A) A Git command that downloads changes from a remote
- B) A GitHub feature that proposes merging one branch into another and enables code review
- C) A request sent to the team to pull their changes into your repository
- D) An automatic merge that happens when two branches are pushed simultaneously

---

**15.** Why should `node_modules/` always be in `.gitignore`?

- A) Node.js files are binary and Git cannot track them
- B) `node_modules/` contains your project's configuration and should be private
- C) It contains thousands of files that are not your code, can be regenerated with `npm install`, and would bloat the repository enormously
- D) Git automatically ignores it regardless of `.gitignore`

---

## Part 2: Short Answer

Suggested answers: [solutions/05-quiz-solutions.md](solutions/05-quiz-solutions.md)

**1.** Explain the three areas of Git (working directory, staging area, repository) using an analogy that is NOT the one used in the course materials.

**2.** What is the difference between `git revert` and `git restore`? When would you use each?

**3.** A colleague asks: "What's the point of the staging area? Why not just commit everything that changed?" Write a convincing response.

**4.** Explain merge conflicts: what causes them, how Git signals them, and what the resolution process is.

**5.** Describe the feature branch workflow in 5–6 steps. Why is it better than everyone committing directly to `main`?

---

## Part 3: Hands-On Challenges

These challenges require you to use Git in your terminal. Solutions show the expected command sequences in [solutions/code-challenges-solutions.md](solutions/code-challenges-solutions.md).

---

### Challenge 1: First Repository

Build a complete Git history from scratch.

1. Create a new directory called `git-practice` and initialize a Git repository
2. Configure your user name and email if not already set
3. Create a `.gitignore` that ignores `.env` and `node_modules/`
4. Create `README.md` with a project title and one-sentence description
5. Create `index.html` with a basic HTML skeleton
6. Make your first commit: `"Initial commit: project scaffold"`
7. Add a `<h1>` heading to `index.html`
8. Stage only `index.html` and commit: `"Add page heading"`
9. Create `style.css` with `body { margin: 0; }`
10. Commit both files together: `"Add stylesheet and link from HTML"`
11. Run `git log --oneline` and verify you have 3 commits

---

### Challenge 2: Branching Workflow

Continue from Challenge 1 or a new repo.

1. Create and switch to a branch called `feature-navbar`
2. Add a `<nav>` element to `index.html` with three placeholder links
3. Commit with message: `"Add navigation bar structure"`
4. Add CSS for the navbar in `style.css`
5. Commit: `"Style navigation bar"`
6. Switch back to `main`
7. Verify `main`'s `index.html` does NOT have the `<nav>` element (it stayed on the branch)
8. Merge `feature-navbar` into `main`
9. Confirm the `<nav>` is now in `main`'s `index.html`
10. Delete the `feature-navbar` branch

---

### Challenge 3: Simulating a Merge Conflict

This challenge creates an intentional conflict to practice resolution.

1. Create a new repo called `conflict-practice`
2. Create `style.css` with:
   ```css
   body {
     background: white;
     color: black;
   }
   ```
3. Commit: `"Add initial styles"`
4. Create branch `feature-light-theme`
5. Change `background` to `#f0f0f0` on the branch
6. Commit: `"Use light gray background"`
7. Switch back to `main`
8. Change `background` to `#1a1a2e` on `main` (different value than the branch)
9. Commit: `"Use dark background"`
10. Try to merge `feature-light-theme` into `main` — you should get a conflict
11. Open `style.css`, choose the dark background value (`#1a1a2e`), remove all conflict markers
12. Stage, commit, and verify the merge succeeded with `git log --oneline --graph`

---

### Challenge 4: GitHub Push and Pull

This requires a GitHub account.

1. Create a new **private** repository on GitHub called `my-portfolio` (no README, no .gitignore — we'll add them locally)
2. Locally: create a directory, initialize Git, add a `.gitignore` and `README.md`, make an initial commit
3. Connect your local repo to GitHub with `git remote add origin <your-repo-url>`
4. Push to GitHub: `git push -u origin main`
5. Verify the files appear on GitHub in your browser
6. On GitHub, edit `README.md` directly in the browser (add a line of text) and commit directly on GitHub
7. Back in your terminal, run `git fetch` — then inspect the difference between local and remote
8. Run `git pull` to integrate the change
9. Verify your local `README.md` now has the line you added on GitHub

---

### Challenge 5: Pull Request Simulation

This challenge practices the full PR workflow on GitHub.

1. In your `my-portfolio` repository from Challenge 4:
2. Locally, create a branch: `feature-about-section`
3. Create a file `about.html` with a basic "About Me" page (use the template from Section 3)
4. Make two commits on this branch (e.g., the HTML in one commit, a link to it from `index.html` in the second)
5. Push the branch to GitHub: `git push -u origin feature-about-section`
6. On GitHub, open a Pull Request from `feature-about-section` into `main`
7. Write a proper PR description (what you added, why, how to test)
8. Merge the PR on GitHub
9. Back in your terminal, switch to `main` and run `git pull` — verify the `about.html` file is now on your local `main`
10. Delete the feature branch locally and on the remote

---

## Completion Checklist

- [ ] I understand why version control exists and the key problems it solves
- [ ] I can initialize a repository and make well-structured commits
- [ ] I know what `.gitignore` is for and can write one for a web project
- [ ] I have connected a local repo to GitHub and pushed/pulled successfully
- [ ] I can create branches, switch between them, and merge them
- [ ] I can read and resolve a merge conflict
- [ ] I can open a Pull Request on GitHub with a useful description
- [ ] I completed all five hands-on challenges

---

*Ready for the next section? → [Section 7: TypeScript and Object-Oriented Programming](../section-07-typescript-oop/01-why-typescript.md)*

*Review sources: [06 — Sources](06-sources.md)*
