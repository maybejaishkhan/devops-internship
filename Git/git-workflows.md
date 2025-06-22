## Workflows

1. Initialize a new repo and push it to remote.
```shell
mkdir my-project && cd my-project      # Create folder & enter
git init                               # Initialize Git repo
git add .                              # Stage everything
git commit -m "<message>"              # Commit with message
git branch -M main                     # Rename 'master' to 'main'
git remote add origin <remote-url>     # Add remote
git push -u origin main                # Push & set upstream
```

2. Clone an existing remote repo.
```shell
git clone <remote-url>                 # Download remote repo & setup origin
cd repo-name                           # Enter the folder
```

3. Make changes and push updates.
```shell
git status                             # Check changes
git add <file>                         # Stage file(s)
git commit -m "<message>"              # Commit with message
git push                               # Push to remote
```

4. Create and work on a new branch.
```shell
git checkout -b <new-branch>            # Create & switch to new branch
git push -u origin <new-branch>         # Push branch to remote & set upstream
```

5. Merge a branch into another.
```shell
git checkout <branch1>                  # Switch to <branch1>
git pull                                # Make sure it's up to date
git merge <branch2>                     # Merge <branch1> into <branch2>
git push                                # Push updated main to remote
```

6. Delete a branch (locally + remotely)
```shell
git branch -d <branch>                  # Delete local branch
git push origin --delete <branch>       # Delete remote branch
```

7. Tag a release and Push it.
```shell
git tag -a v1.0 -m "<message>"          # Create annotated tag with message
git push origin v1.0                    # Push tag to remote
```

8. Stash changes and reapply later.
```shell
git stash                               # Save uncommitted changes
git pull                                # Update branch safely
git stash pop                           # Reapply stashed changes
```
