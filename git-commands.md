# Git Commands Reference
> Updated daily as I learn new commands

---

## Setup & Config

| Command | What it does |
|---|---|
| `git --version` | Check installed Git version |
| `git config --global user.name "Name"` | Set your name for all commits |
| `git config --global user.email "email"` | Set your email for all commits |
| `git config --list` | View all current Git configuration |

## Basic Workflow

| Command | What it does |
|---|---|
| `git init` | Initialize a new Git repository in current folder |
| `git add <file>` | Stage a specific file for commit |
| `git add .` | Stage ALL changed files |
| `git commit -m "message"` | Save staged changes with a message |
| `git status` | Show current state of working directory and staging area |

## Viewing Changes

| Command | What it does |
|---|---|
| `git log` | Show full commit history |
| `git log --oneline` | Compact one-line commit history |
| `git diff` | Show changes not yet staged |
| `git diff --staged` | Show changes that are staged (ready to commit) |

## Branching

| Command | What it does |
|---|---|
| `git branch` | List all local branches |
| `git branch <name>` | Create a new branch |
| `git checkout <branch>` | Switch to a branch |
| `git checkout -b <name>` | Create and switch to a new branch |
| `git merge <branch>` | Merge a branch into the current one |
| `git branch -d <name>` | Delete a branch (safe — won't delete unmerged) |

### Merge behavior notes
- **Fast-forward merge**: happens when the target branch (e.g. `main`) hasn't moved since the feature branch was created — Git just moves the pointer forward, no merge commit.
- **Merge commit**: happens when both branches have new commits since they diverged — Git creates a commit with two parents (shows as a diamond `|\ ... |/` in `git log --graph`).
- **Merge conflict**: happens when both branches edited the same line of the same file. Git inserts `<<<<<<<`, `=======`, `>>>>>>>` markers — resolve by editing the file, removing the markers, then `git add` + `git commit`.

## Rebase

| Command | What it does |
|---|---|
| `git rebase <branch>` | Replay current branch's commits on top of `<branch>`, rewriting commit hashes |
| `git rebase --continue` | Continue rebase after resolving a conflict |
| `git rebase --abort` | Cancel the rebase and return to the pre-rebase state |
| `git rebase --skip` | Skip the current commit during a rebase conflict |

> **Rule:** Only rebase commits that are still local and unpushed. Rebasing shared/pushed commits rewrites their hashes and breaks history for anyone who already pulled them.

## Squash Merge

| Command | What it does |
|---|---|
| `git merge --squash <branch>` | Stages all changes from `<branch>` as one set, without committing and without preserving individual commit history |
| `git commit -m "message"` (after squash) | Creates the single combined commit on the current branch |

> Squash merge collapses many small/WIP commits into one clean commit on `main`. Trade-off: you lose the granular commit-by-commit history (useful for `bisect`/`blame`).

## Stash

| Command | What it does |
|---|---|
| `git stash` / `git stash push` | Save uncommitted changes (staged + unstaged) and revert working directory to clean state |
| `git stash push -m "message"` | Stash with a descriptive label |
| `git stash list` | List all stashes |
| `git stash apply` | Reapply the most recent stash, **keeping it** in the stash list |
| `git stash apply stash@{n}` | Reapply a specific stash by index, keeping it in the list |
| `git stash pop` | Reapply the most recent stash **and delete it** from the list |
| `git stash pop stash@{n}` | Reapply a specific stash and delete it from the list |
| `git stash drop stash@{n}` | Delete a specific stash without applying it |
| `git stash clear` | Delete all stashes |

> `apply` keeps the stash around for reuse (e.g., applying to multiple branches); `pop` is a one-time apply-and-remove. Use stash to safely switch branches without committing half-finished work.

## Cherry Pick

| Command | What it does |
|---|---|
| `git cherry-pick <commit-hash>` | Apply the changes from a single specific commit onto the current branch as a new commit |
| `git cherry-pick --continue` | Continue after resolving a cherry-pick conflict |
| `git cherry-pick --abort` | Cancel the cherry-pick and return to the pre-pick state |
| `git cherry-pick --skip` | Skip the current commit during a cherry-pick conflict |

> Cherry-pick creates a **new commit hash** — it copies the change, not the original commit. Useful for backporting a single fix without merging an entire branch. Watch for conflicts if the target branch has diverged, and for missing dependencies if the picked commit relies on earlier commits you didn't pick.

## Remote Repositories

| Command | What it does |
|---|---|
| `git remote add origin <url>` | Connect local repo to a remote (GitHub) |
| `git remote -v` | View connected remote repositories |
| `git push origin main` | Push commits to the remote main branch |
| `git pull` | Fetch and merge changes from remote |
| `git clone <url>` | Copy a remote repository to your machine |
| `git fetch` | Download changes from remote but don't merge yet |

## Undoing Changes

| Command | What it does |
|---|---|
| `git restore <file>` | Discard changes in working directory (unstaged) |
| `git restore --staged <file>` | Unstage a file (remove from staging area) |
| `git revert <commit>` | Create a new commit that undoes a previous one (safe) |
| `git reset --soft HEAD~1` | Undo last commit, keep changes staged |
| `git reset --hard HEAD~1` | Undo last commit and discard all changes (DANGEROUS) |
