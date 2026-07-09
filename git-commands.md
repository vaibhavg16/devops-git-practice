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
| `git switch <branch>` | Modern alternative to `checkout` for switching branches |
| `git switch -c <name>` | Modern alternative to `checkout -b` — create and switch to a new branch |
| `git merge <branch>` | Merge a branch into the current one |
| `git branch -d <name>` | Delete a branch (safe — won't delete unmerged) |
| `git branch -D <name>` | Force-delete a branch even if unmerged |

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
| **Fork** (GitHub feature, not a raw git command) | Creates your own copy of someone else's repo under your GitHub account; clone your fork locally, push changes there, then open a PR back to the original repo |

## Undoing Changes — Restore

| Command | What it does |
|---|---|
| `git restore <file>` | Discard changes in working directory (unstaged) |
| `git restore --staged <file>` | Unstage a file (remove from staging area) |

## Reset & Revert

| Command | What it does |
|---|---|
| `git reset --soft HEAD~1` | Move branch pointer back 1 commit; changes stay **staged** |
| `git reset --mixed HEAD~1` (default) | Move branch pointer back 1 commit; changes become **unstaged** (kept in working directory) |
| `git reset --hard HEAD~1` | Move branch pointer back 1 commit; changes are **discarded entirely** (DANGEROUS — working directory reverted too) |
| `git revert <commit>` | Create a **new commit** that undoes a previous commit's changes — original commit stays in history (SAFE for shared branches) |
| `git revert --continue` | Continue a revert after resolving a conflict |
| `git reflog` | Show a log of every place `HEAD` has pointed — the safety net to recover commits after a `reset --hard` (until garbage collected) |

> **Reset vs Revert:** `reset` rewrites history by moving the branch pointer — only safe on local, unpushed commits. `revert` never rewrites history — it adds a new "undo" commit on top, so it's safe even on shared/pushed branches. Rule of thumb: **reset for private/local cleanup, revert for anything already shared.**

> **Recovering from a hard reset:** run `git reflog` to find the commit hash you reset away from, then `git reset --hard <hash>` to restore it — as long as it hasn't been garbage collected yet.

## Branching Strategies (Reference)

| Strategy | How it works | Best for |
|---|---|---|
| **GitFlow** | Long-lived `main` + `develop`, plus `feature/*`, `release/*`, `hotfix/*` branches | Large teams, scheduled/versioned releases, need to maintain multiple production versions |
| **GitHub Flow** | Single long-lived `main` (always deployable) + short-lived `feature/*` branches merged via PR | Startups / web apps shipping fast with CI/CD |
| **Trunk-Based Development** | Everyone commits to `main` (trunk) frequently, short-lived branches (hours, not days), feature flags for incomplete work | High-velocity teams at scale (e.g. Kubernetes, Google) with strong CI/CD |



```bash
# ─── GitHub CLI — Auth ────────────────────────────────────────
gh auth login                           # authenticate with GitHub
gh auth status                          # check which account is active
gh auth logout                          # log out
export GH_TOKEN="ghp_..."              # authenticate via environment variable

# ─── GitHub CLI — Repositories ───────────────────────────────
gh repo create <name> --public --add-readme  # create a new repo
gh repo clone owner/repo                # clone using shorthand (handles auth)
gh repo view owner/repo                 # view repo details
gh repo list                            # list all your repos
gh repo delete owner/repo --yes         # delete a repo (permanent!)
gh browse                               # open current repo in browser
gh browse --repo owner/repo             # open a specific repo in browser

# ─── GitHub CLI — Issues ─────────────────────────────────────
gh issue create --title "" --body "" --label ""  # create an issue
gh issue list                           # list open issues
gh issue list --label "bug"             # filter by label
gh issue view <number>                  # view a specific issue
gh issue close <number>                 # close an issue
gh issue close <number> --comment ""    # close with a comment
gh issue reopen <number>                # reopen an issue

# ─── GitHub CLI — Pull Requests ──────────────────────────────
gh pr create --title "" --body "" --base main   # create a PR
gh pr create --fill                     # auto-fill from commit message
gh pr list                              # list open PRs
gh pr view <number>                     # view PR details
gh pr checks <number>                   # view CI check statuses
gh pr diff <number>                     # view code changes in a PR
gh pr checkout <number>                 # check out PR branch locally
gh pr merge <number> --merge --delete-branch    # merge commit + delete branch
gh pr merge <number> --squash           # squash merge
gh pr merge <number> --rebase           # rebase merge
gh pr merge <number> --auto             # auto-merge when checks pass
gh pr review <number> --approve         # approve a PR
gh pr review <number> --request-changes --body ""  # request changes
gh pr review <number> --comment --body ""          # comment on a PR

# ─── GitHub CLI — Actions / Workflows ────────────────────────
gh run list                             # list recent workflow runs
gh run list --status failure            # only failed runs
gh run view <id>                        # view a specific run
gh run watch <id>                       # watch a run live in terminal
gh run rerun <id>                       # re-run a failed run
gh run download <id> --dir ./artifacts  # download workflow artifacts
gh workflow run <workflow.yml>          # trigger a workflow manually

# ─── GitHub CLI — Advanced ───────────────────────────────────
gh api <endpoint>                       # raw GitHub API call
gh api rate_limit                       # check API rate limit
gh api --method PUT <endpoint>          # API call with specific HTTP method
gh gist create <file> --public          # create a public Gist
gh gist list                            # list your Gists
gh gist view <id>                       # view a Gist
gh release create <tag> --generate-notes  # create a release
gh release list                         # list releases
gh release download <tag> --dir ./      # download release assets
gh alias set <name> "<command>"         # create a command shortcut
gh alias list                           # list your shortcuts
gh search repos "<query>" --limit 10    # search GitHub repos
gh search repos "<query>" --language Go # filter by language
```
