It is a Git feature to **temporarily save (stash) your local changes** without committing them, so you can: switch branches, pull latest changes or fix something urgent  — **without losing your work-in-progress**.

```shell
git stash
```

Git saves the difference between the *last commit* (`HEAD`) and your *working/staging* areas. It then resets both the *working/staging* areas to match `HEAD`.

> The stashed changes are stored as a special hidden commit inside `.git/refs/stash`.

1. `git stash` --> Save uncommitted changes.
	- `git stash -u` --> Include untracked changes too.
	- `git stash -a` --> Include untracked + ignored changes too.
	- `git stash push -m <message>` --> Save *with a custom message*.
2. `git stash list` --> See all stashes.
3. `git stash show` --> Show latest stash.
	- `git stash show -p` --> Show *full difference*.
4. `git stash apply` --> Apply last stash, but keep it in list.
5. `git stash pop` --> Apply last stash, and remove it from list.
6. `git stash clear` --> Delete all stashes.

Git numbers stashes like `stash@{0}`, `stash@{1}`... So, we can target specific stashes as well.
- `git stash apply stash@{<number>}` --> Apply specific stash (and keep it).
- `git stash pop stash@{<number>}` --> Apply specific stash (and remove it).
- `git stash drop stash@{<number>}` --> Delete specific stash.
- `git stash branch <branch> stash@{<number>}` --> Create a new branch from a stash and apply it there.
