`git branch` lets you to list, create, delete and rename branches. A **branch** is simply a **pointer to a commit** plus a moving HEAD marker.
### 1. `git branch` 
List all (local) branches.
- `git branch -a` --> List local + remote branches.
- `git branch -r` --> List remote branches only.
- `git branch <name>` --> Create a new branch.
- `git branch -d <name>` --> Delete a branch (safe: checks for merges).
- `git branch -D <name>` --> Force-delete a branch (ignores unmerged commits).
- `git branch -m <old> <new>` --> Rename a branch.
- `git branch -M <old> <new>` --> Force rename (overwrite if exists).
- `git branch --contains <commit>` --> Show branches that contain a commit.
- `git branch --merged` --> List branches already merged into current HEAD.
- `git branch --no-merged` --> List branches NOT yet merged.
- `git branch -vv` --> List branches with last commit info and tracking status.
### 2. `git switch <branch>` 
Switches to a branch (modern).
* `git switch -c <new-branch>` --> Create a new branch and switch to it.
* `git switch -C <branch>` --> Create or reset branch to current commit.
-  `git switch --detach` --> Checkout a commit in detached HEAD mode.
-  `git switch --discard-changes <branch>` --> Force switch even if there are local changes (rare).

> `git checkout <branch>` and `git checkout -b <new-branch>` are legacy commands for switching to a branch and creating a branch, respectively.
### 3. `git restore` 
Discard working changes (modern).
* `git restore <file>` --> Restore specific file in working directory.
* `git restore --source=<branch> <file>` --> Restore file from a different branch.
* `git restore --staged <file>` --> Unstage file (keep working dir changes).

> `git checkout -- <file>` and 	`git reset HEAD <file>` are legacy commands for discarding changes and unstaging.
### 4. `git merge <branch>` 
Merge `<branch>` into current branch.
* `git merge --no-ff <branch>` --> Force a merge commit (disable fast-forward).
* `git merge --squash <branch>` --> Merge changes but squash into single commit.
* `git merge --abort` --> Abort a conflicted merge safely.
* `git merge --continue` --> Continue after resolving merge conflicts.
* `git merge --strategy-option=<option>` --> Advanced conflict strategies (`ours`, `theirs`).
### 5. `git rebase <branch>` 
Re-apply commits of current branch on top of `<branch>`.
* `git rebase -i <branch>` --> Interactive rebase: reorder, squash, edit commits.
* `git rebase --abort` --> Abort a rebase in progress.
* `git rebase --continue` --> Continue after fixing conflicts.
* `git rebase --skip` --> Skip the current conflicting commit.
* `git rebase --onto <newbase> <upstream> <branch>` --> Rebase selected commits onto a new base.
### 6. `git cherry-pick <commit>` 
Apply a specific commit to current branch.
* `git cherry-pick A B C` --> Apply multiple commits in order.
* `git cherry-pick --continue` --> Continue after resolving conflicts.
* `git cherry-pick --abort` --> Abort a cherry-pick.
### 7. `git checkout` 
Original all-in-one for switching & restoring.
   * `git checkout <branch>` --> Switch branches (legacy, now `switch`).
   * `git checkout -b <branch>` --> Create + switch branch (legacy).
   * `git checkout -- <file>` --> Restore file (legacy, now `restore`).
   * `git checkout <commit>` --> Detached HEAD at commit.
   * `git checkout <commit> -- <file>` --> Checkout file version from a commit.
### 8. `git reflog` 
See branch & HEAD history.
   * `git reflog` --> Shows where HEAD and branches have pointed.
   * Use to recover lost branches or commits.

---
