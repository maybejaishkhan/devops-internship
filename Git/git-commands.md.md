| Command                   | Complement                |
| ------------------------- | ------------------------- |
| `git clone <url>`         | `git push`                |
| `git init`                | `rm -rf .git`             |
| `git add <files>`         | `git reset`               |
| `git commit -m <message>` | `git reset --soft HEAD~1` |
| `git push`                | `git pull`                |
| `git fetch`               | `git push`                |
| `git merge`               | `git reset --hard`        |
| `git checkout <branch>`   | `git checkout -`          |
| `git checkout <file>`     | `git add <file>`          |
| `git stash`               | `git stash pop`           |
| `git stash`               | `git stash apply`         |
| `git branch <name>`       | `git branch -d <name>`    |
| `git tag <tag>`           | `git tag -d <tag>`        |
| `git log`                 | `git reflog`              |
| `git diff`                | `git diff --staged`       |
| `git revert`              | `git cherry-pick`         |
| `git remote add`          | `git remote remove`       |
