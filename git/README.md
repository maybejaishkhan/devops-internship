# Git - Version Control System

Basically the default VCS that is being used by 90% of the world. It is a terminal application that makes it possible to contribute and share code.

- [Setup](#setup)
- [Start a Git repository](#start-a-git-repository)
  - [Folder Anatomy of `.git`](#folder-anatomy-of-git)
- [Project Areas](#project-areas)
- [Branches](#branches)
  - [Merging and Rebasing](#merging-and-rebasing)
  - [Merge Conflicts](#merge-conflicts)
  - [Stash and Worktree](#stash-and-worktree)
- [Inspection](#inspection)
- [Submodules](#submodules)

---

## Setup

1. **Windows** --> Get the installer from [https://git-scm.com/downloads]. Don't install via package manager (winget/choco/scoop) as you'd have to manually configure it.
2. **Linux** --> Use the package manager.
    1. Ubuntu/Debian: `sudo apt install git`
    2. Fedora/CentOS: `sudo dnf install git`
    3. Arch: `sudo pacman -S git`
3. **MacOS** --> Use homebrew `brew install git`

```shell
# Verify Installation
git --version

# Set a Global Username and Email
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# Check Full Config
git config --list
```

## Start a Git repository

There are 2 ways of starting a Git project:

1. *Local-first* --> **`git init`** creates a new **empty Git repository** in your current directory. This turns an ordinary folder into a **Git version-controlled project** (with a `.git` folder).
2. *Remote-first* --> `git clone <url>` copies an existing remote repository locally and configures the remote origin as well.

> We can also "undo" a Git project by doing `rm -rf .git` (Linux).

There is also the concept of a **mirror clone** done by `git clone <repo> --mirror`. This clones it as a workarea-less which has all branches/commits (exact mirror of our remote repo). We can then go `git clone <path-to-mirrored-repo>` to get a proper repo.
 
### Folder Anatomy of `.git`

In a Git repository, run `tree -a .git`  (or use `ls -a .git` if no tree). You’ll see:

```shell
.git/
├── HEAD                # Points to current branch (`refs/heads/main`)
├── config              # Repo-specific config (user.name, remotes ...)
├── description         # Used by Gitweb, mostly legacy
├── hooks/              # Client-side scripts (pre-commit, pre-push, etc.)
├── info/               # Extra info, e.g. exclude file for local ignores
├── objects/            # All commits/trees/blobs stored here
├── refs/               # Contains refs: branches, tags
```

## Project Areas

A Git project has **3 main areas** (plus 1 more when working with others). Understanding these helps you know where your files are at each step.

1. **Working Directory (Working Tree)** --> This is your actual project folder on your computer. You create, edit, delete files here like normal. By default, Git does not track new files until you tell it to.
2. **Staging Area (Index)** --> This is a holding area for changes you want to include in the next commit. It lets you build a perfect commit, exactly what you want to save.
3. **Local Repository (History)** --> This is the hidden `.git` folder. When you make a commit, Git takes a snapshot of what's in the Staging Area and saves it forever. You can go back in time, see who changed what, undo mistakes (all locally).
4. **Remote Repository (Shared Repo)** --> This is the copy of your repo on a server, like GitHub, GitLab, or Bitbucket. Used for sharing and collaboration.

| Command | Opposite |
| --- | --- |
| `git init` | `rm -rf .git` |
| `git add .`<br/>`git add <file>`<br/>**Working Directory** to **Staging Area** | `git restore --staged .` <br /> `git restore --staged <file>` <br /> or <br /> `git reset`<br />`git reset <file>` |
| `git commit -m "<message>"`<br />**Staging Area** to **Local Repository** | `git revert` <br />`git reset --mixed HEAD~1` <br />`git reset --soft HEAD~1`<br /> `git reset --hard HEAD~1` <br /> `git commit --amend` |
| `git push`<br />**Local Repository** to **Remote Repository** | `git fetch`<br />`git pull` |

- `git restore --staged` is a much safer alternative to `git reset`.
- `git revert` makes a new commit that undoes whatever the last commit did.
- `git reset` takes the HEAD and moves it back to change commit history. The 3 parameters decide what happens to the changes:
    - `--mixed` (unstaged).
    - `--soft` (staged).
    - `--hard` (erased).
- `git commit --amend` is for changing the last commit.
- `git pull` is a combination of `git fetch` and `git merge`.

## Branches
>
> Branches let you work on multiple "versions" of your project at the same time. A branch is basically a lightweight movable pointer to a commit.

1. See (Local): `git branch`
    - Add `-r`, `-a` or `-v` to see Remote, All or Latest Commit branches.
    - Add `--merged` or `--no-merged` to see Merged or Unmerged branches.
2. Create: `git branch <name>`
3. Switch: `git switch <name>` or `git checkout <name>`
4. Create & Switch: --> `git switch -c <name>` or `git checkout -b <name>`
5. Delete (Local): `git branch -d <name>` (Safe) or `git branch -D <name>` (Force)
6. Push (Remote): `git push origin <branch>`
7. Delete (Remote): `git push origin --delete <branch>`
8. Rename: `git branch -m <old> <new>`

### Merging and Rebasing

Suppose you have 2 branches: **main** and **feature**.

- The **main** branch starts with commits `{A, B}`.
- You create the **feature** branch at commit `B`.
- You add two new commits on **feature**: `C` and `D`.

```
main:    A — B  
feature: A — B — C — D
```

Since **feature** includes everything from **main**, we can run: `git merge feature` (on main).

This will copy the *extra* commits (`C` and `D`) onto **main** as well. This is called a **Fast-Forward Merge** because no new *merge commit* is needed — Git just moves the branch pointer forward.

Now, If someone added new commits to **main** (say, `E` and `F`), then:

```
main:    A — B — E — F
feature: A — B — C — D
```

Now the branches have diverged — they both share `{A,B}` but each has unique commits after that. There are now 2 ways of combining:

1. **Merge Commit** --> On *main*, run: `git merge feature`
    - Git creates a special **merge commit** which has two parents:
        - the latest commit on **main** (`F`)
        - the latest commit on **feature** (`D`).
    - Result:

  ```
  main:    A — B — E — F — M
                        /   
  feature: A — B — C — D
  ```

2. **Rebase** --> On *feature*, run: `git rebase main`
    - What Happens:
        1. Git finds the **common ancestor** (`B`).
        2. It temporarily removes **feature**’s unique commits (`C` and `D`).
        3. It fast-forwards **feature** to match **main** (`E` and `F`).
        4. It re-applies `C` and `D` *on top* of `F` as new commits (`C'`, `D'`).
    - Result:

  ```
  main:    A — B — E — F

  feature: A — B — E — F — C' — D'
  ```

We can see that **Merge Commits** keep the commit histories intact BUT are messy and **Rebasing** makes the commit history linear BUT it rewrites history and changes commit hashes (becomes a problem on shared branches).

- Use **merge** for teamwork.
- Use **rebase** for cleaning up before merging or for local branches.

To get these changes onto the remote repository, `git push` wouldn't work as it doesn't overwrite branches. So, we use `git push --force-with-lease`.

- NEVER use `git push --force` as it'd destroy changes made by others on the remote repo.

### Merge Conflicts

There are merge conflicts when git can't automerge branches together. To fix that, do `git status` and go to each file individually to fix the conflicts. Git marks them with <<<<<<<<<===========>>>>>>>>>.

Once done, do `git commit` (Merge) or `git rebase --continue` (Rebase) to carry on.

### Stash and Worktree

> Stash is a git feature to temporarily save (stash) your local changes without committing them, so you can: switch branches, pull latest changes or fix something urgent  — **without losing your work-in-progress**.

Git saves the difference between the *last commit* (`HEAD`) and your *working/staging* areas. It then resets both the *working/staging* areas to match `HEAD`.

1. See (All): `git stash list`
2. See (Latest): `git stash show`
3. Save (Uncommitted Changes): `git stash`
    - Add `-u` or `-a` to stash away Untracked or All changes.
    - Add `-m <message>` to attach a message to the stash.
4. Apply last stash (Keep): `git stash apply`
5. Apply last stash (Remove): `git stash pop`
6. Delete all stashes: `git stash clear`

Git numbers stashes like `stash@{0}`, `stash@{1}`... So, we can target specific stashes as well.

- Delete specific stash: `git stash drop stash@{<number>}`
- Create branch from stash: `git stash branch <branch> stash@{<number>}`.

> Worktree lets you check out multiple branches at once in different folders — without messing up your main working directory.Useful when you want to test or fix things side-by-side.

Internally, Worktrees shares the same .git folder, saving disk space.

1. See (All): `git worktree list`
2. Create: `git worktree add <path> <branch>`
3. Delete: `git worktree remove <path>`
4. Cleanup: `git worktree prune`
5. Create branch from worktree: `git worktree add <path> -b <branch>`

## Inspection

`git status` is the main command for inspection. It shows staged, untracked, modified etc.

- `git diff` to see unstaged changes.
- `git diff --staged` to see staged changes.

To check the commit history we can do `git log`.

- Add `--online` for one-liner overviews for each commit.
- Add `--graph` to see ASCII commit history graph.
- Add `--all` to see all branches.

To see

- What changes a commit did `git show <commit>`
- What changes affected a file `git log <file>`
- Who last modified each line of a file `git blame <file>`. Add `-L 10,20` to only blame lines 10-20.

`git reflog` shows all the HEAD movements (commit/rebase/checkout).

We can inspect "remotes" with their URLs by doing `git remote -v` (for detailed `git remote show <remote>`).

## Submodules
