# 01 — Why Version Control? The Problem It Solves

## Learning Objectives

By the end of this page, you will be able to:

- Describe the problems that version control solves
- Explain the difference between local, centralized, and distributed version control
- Describe how Git stores history as snapshots
- Define the key terms: repository, commit, branch, remote, working directory

---

## The World Without Version Control

Imagine you're working on a website project. You make a big change, and it breaks everything. You want to go back to how it was yesterday — but you've been saving over the same files.

Or you're working with a friend. You each have a copy of `index.html`. You make changes. They make changes. Now you need to combine your work. How do you know whose changes to keep? How do you avoid overwriting each other's work?

These are the problems every developer faced before version control existed. The common "solution" looked like this:

```
project-final/
project-final-v2/
project-final-REALLY-FINAL/
project-final-REALLY-FINAL-use-this-one/
project-final-jan15/
project-backup-before-I-broke-it/
```

> **This is not version control.** It's chaos with a timestamp.

---

## What Version Control Does

A **version control system (VCS)** tracks changes to files over time, allowing you to:

- **Revert** any file (or the whole project) to a previous state
- **See what changed**, when it changed, and who changed it
- **Collaborate** — multiple people work on the same codebase without overwriting each other
- **Branch** — work on new features without disturbing the stable code
- **Merge** — combine work from different people or feature branches
- **Experiment safely** — try something risky; roll it back if it fails

> **Analogy:** Version control is like Google Docs' revision history for your entire project. You can see every change ever made, who made it, and roll back to any point in time. But far more powerful — you can also work in parallel timelines and merge them together.

---

## A Brief History of Version Control

### Local Version Control Systems (1970s–1980s)

The earliest tools kept a database of file changes locally on one machine:

- **SCCS** (Source Code Control System, 1972) — one of the first; stored deltas (differences between versions)
- **RCS** (Revision Control System, 1982) — tracked individual files; still used in some legacy systems

**Problem:** Everything lived on one machine. No collaboration. No backup if the disk died.

### Centralized Version Control Systems (1990s–2000s)

A single central server holds all the version history. Developers check files out from the server, work on them, and check them back in:

- **CVS** (Concurrent Versions System, 1990) — widely used in open source; notoriously tricky to use
- **Subversion (SVN, 2000)** — improved CVS significantly; still used in many companies today

```
Developer A ─── checkout/commit ───▶ Central Server
Developer B ─── checkout/commit ───▶ Central Server
Developer C ─── checkout/commit ───▶ Central Server
```

**Problems:**
- If the central server goes down, nobody can commit or access history
- Branching and merging were painful and slow
- You need network access to do almost anything (commit, diff, history)

### Distributed Version Control Systems (2000s–present)

Every developer has a **complete copy** of the entire repository, including full history:

- **Git** (2005, Linus Torvalds) — created to manage the Linux kernel's development; now dominant
- **Mercurial** (2005) — similar concept; used at Facebook/Meta for years

```
Developer A: [full repo copy] ←─ sync ─▶ Remote (GitHub)
Developer B: [full repo copy] ←─ sync ─▶ Remote (GitHub)
Developer C: [full repo copy] ←─ sync ─▶ Remote (GitHub)
```

**Advantages:**
- Full history available offline — commit, diff, branch, all without internet
- No single point of failure — if GitHub goes down, everyone still has a full copy
- Branching and merging are fast and central to the workflow
- Encourages experimentation — branches are cheap

---

## Why Git Won

Git was created by **Linus Torvalds** in 2005 specifically to manage the Linux kernel — one of the world's largest and most complex software projects, with thousands of contributors worldwide.

His requirements were uncompromising:
- Must be fast (CVS was too slow at Linux kernel scale)
- Distributed — no central bottleneck
- Strong integrity guarantees — data cannot be silently corrupted
- Support non-linear development (thousands of parallel branches)

Git met all these requirements. It was adopted rapidly beyond the Linux community, and today:

- **Git is used by 94%+ of professional developers** (Stack Overflow survey, 2022)
- Nearly all open source projects on GitHub use Git
- It is the de facto standard — knowing Git is non-negotiable for any developer

---

## How Git Thinks About Data

Most version control systems store data as a list of changes to files (delta-based):

```
Version 1:  base files
Version 2:  + change to file A
Version 3:  + change to file B
Version 4:  + change to files A and C
```

Git is different. Git thinks of its data as a **series of snapshots** of the entire project:

```
Snapshot 1: [file A v1] [file B v1] [file C v1]
Snapshot 2: [file A v2] [file B v1] [file C v1]   ← A changed, B and C just ref prior snapshot
Snapshot 3: [file A v2] [file B v2] [file C v1]
```

If a file didn't change between commits, Git doesn't store it again — it just stores a reference to the previous snapshot. This is both efficient and fast.

> **Analogy:** A traditional VCS keeps a movie of changes — to see version 50, you have to play through all 49 previous frames. Git keeps a photo album — each photo is a complete picture of the project at that moment. Looking at commit 50 is instant, regardless of how many commits came before it.

### Data Integrity

Every file and commit in Git is identified by a **SHA-1 hash** — a 40-character fingerprint derived from the content:

```
commit a3f4b9c2d8e17f5a6b0c3d4e5f6a7b8c9d0e1f2a
```

If even one byte of any file changes, the hash changes. This means you always know your history is intact — corruption is detectable, and tampering is nearly impossible.

---

## Key Git Concepts

Before going hands-on, internalize these terms:

| Term | Definition |
|------|-----------|
| **Repository (repo)** | The directory tracked by Git, containing your files and the complete `.git` history database |
| **Commit** | A snapshot of the project at a specific point in time, with a message describing what changed |
| **Working directory** | The files you see and edit on disk — what's in your project folder right now |
| **Staging area (index)** | A preparation zone where you assemble the next commit before saving it |
| **Branch** | A named pointer to a commit — a moveable label representing a line of development |
| **HEAD** | A special pointer indicating the commit you currently have checked out |
| **Remote** | A version of the repository hosted elsewhere (GitHub, GitLab, etc.) |
| **Clone** | A full copy of a repository, including all history |
| **Push** | Send your local commits to a remote |
| **Pull** | Fetch remote commits and merge them into your local branch |

> **Commit = save point.** Just like saving your game, a commit preserves the exact state of your project at that moment. Unlike a game save, you can have thousands of save points, jump between them freely, and create branching saves that diverge and later merge.

---

## What Git Is NOT

- **Not a backup system** — commits on your local machine aren't backed up until you push to a remote
- **Not a deployment tool** — though it's often used in deployment pipelines
- **Not GitHub** — Git is the software; GitHub is a website that hosts Git repositories and adds collaboration features (pull requests, issues, CI/CD)

**Further Reading:**
- [About Version Control — Git Book](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control)
- [A Short History of Git — Git Book](https://git-scm.com/book/en/v2/Getting-Started-A-Short-History-of-Git)
- [Git vs SVN — Atlassian](https://www.atlassian.com/git/tutorials/svn-to-git-prepping-your-team-migration)

---

## Check Your Understanding

1. What are two problems that occur when developers collaborate on the same files without version control?
2. What is the difference between a centralized VCS (like SVN) and a distributed VCS (like Git)?
3. Why does Git store snapshots rather than deltas, and what is the advantage?
4. What is the difference between Git and GitHub?
5. What does SHA-1 hashing provide for Git's data integrity?

---

*Next: [02 — Git Basics](02-git-basics.md)*
