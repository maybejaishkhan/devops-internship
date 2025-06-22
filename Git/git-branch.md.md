`git branch` lets you to list, create, delete and rename branches. A **branch** is simply a **pointer to a commit** plus a moving HEAD marker.

1. `git branch` --> List all (local) branches.
	- `git branch -a` --> Remote branches as well.
	- `git branch <name>` --> Create a branch.
	- `git branch -d <name>` --> Delete a branch.
	- `git branch -D <name>` --> Force-delete a branch.
	- `git branch -m <old> <new>` --> Rename a branch.
2. `git switch <branch>` --> Switches to a branch.
	- `git checkout <branch>` (Legacy)
	- `git switch -c <new-branch>` --> Create a branch and switch to it.
		- `git checkout -b <new-branch>` (Legacy)
3. `git restore` --> Discard working changes.
	- `git checkout --` (Legacy)
	- `git restore --staged` --> Unstage.
		- `git reset HEAD` (Legacy)
4. `git merge <branch>` --> Merge `<branch>` into current one.
	- `git merge --no-ff <branch>` --> Force it. No Fast Forwarding.
	- `git merge --abort <branch>` --> Cancel a merge.
5. `git rebase <branch>` --> Re-apply this branch's commits on top of `<branch>`.
	- `git rebase --abort <branch>` --> Cancel a rebase.

Branches are just **refs** stored in `.git/refs/heads/`. E.g.:

```
.git/refs/heads/main
.git/refs/heads/feature-x
```

---

# ✅ **2️⃣ `git checkout` — switch & restore**

## 📌 **What is it for?**

- **Switch branches** (`git switch` is the modern version)
    
- **Restore files** to how they were at some commit
    

## 📌 **How it works**

- Moving **HEAD** to point to a different branch or commit.
    
- Updating working directory to match.
    

## 📌 **Common usages**

|Command|What it does|
|---|---|
|`git checkout <branch>`|Switch to an existing branch|
|`git checkout -b <branch>`|Create and switch to new branch|
|`git checkout <commit>`|Detach HEAD — view past commit|
|`git checkout -- <file>`|Restore file to match last commit|

## 📌 **Examples**

```bash
git checkout dev           # switch to 'dev'
git checkout -b new-branch # create + switch
git checkout 4f5e6a7       # checkout commit in detached HEAD
git checkout -- index.js   # discard changes to index.js
```

## ⚡️ **Modern alternative**

- `git switch <branch>` → for switching branches only.
- `git restore <file>` → for restoring files.
    

These make intent clearer.

---

# ✅ **3️⃣ `git merge` — combine branches**

## 📌 **What is it for?**

- **Merge commits from one branch into another**
    
- Creates a **merge commit** if needed.
    

## 📌 **How it works**

- It compares the common ancestor, your branch, and the branch to merge.
    
- Applies the changes on top.
    
- If there are conflicts, you must resolve them.
    

## 📌 **Basic usage**

```bash
# On main, merge 'feature':
git checkout main
git merge feature
```

## 📌 **Merge types**

|Type|When it happens|
|---|---|
|**Fast-forward**|If your branch is exactly behind the other.|
|**3-way merge**|If both branches diverged. Creates a merge commit.|

## 📌 **Common options**

|Option|What it does|
|---|---|
|`--no-ff`|Force a merge commit even if fast-forward possible|
|`--abort`|Cancel a conflicted merge|

---

# ✅ **4️⃣ `git rebase` — replay commits**

## 📌 **What is it for?**

- **Move (reapply) commits from your branch onto another base**
    
- Creates a **cleaner, linear history**
    

## 📌 **How it works**

- Finds the base commit where your branch diverged.
    
- Re-applies your commits on top of the new base.
    
- Changes commit hashes (rewrites history).
    

## 📌 **Basic usage**

```bash
# Update your feature branch to latest main:
git checkout feature
git rebase main
```

This means:

> “Take the commits on `feature` and replay them on top of `main`.”

## 📌 **Interactive rebase**

```bash
git rebase -i HEAD~3
```

- Lets you pick, edit, squash, reorder commits before rewriting them.
    

---

# ✅ **Merging vs Rebasing**

| | `git merge` | `git rebase` |  
|---|---|  
| **Keeps history** | ✔️ | ❌ (rewrites) |  
| **Creates merge commits** | ✔️ | ❌ |  
| **Linear history** | ❌ | ✔️ |  
| **Safe for shared branches** | ✔️ | ⚠️ Don’t rebase pushed commits |

---

# ✅ **Practical flow**

|Goal|Recommended|
|---|---|
|Combine work locally, keep history clean|`git rebase`|
|Combine shared work, keep all history|`git merge`|

---

# ✅ **Summary table**

|Command|Common use|
|---|---|
|`git branch <name>`|Make new branch|
|`git branch -d <name>`|Delete branch|
|`git checkout <branch>`|Switch branch|
|`git checkout -b <branch>`|Create + switch|
|`git merge <branch>`|Merge into current|
|`git rebase <branch>`|Reapply commits on top of another branch|
|`git rebase -i HEAD~N`|Interactive rebase|

---

# ✅ **Golden rules**

✅ Use **merge** for shared branches — safe for team.  
✅ Use **rebase** for local clean-up.  
✅ Never rebase commits you’ve pushed (unless you _really_ know what you’re doing).

---

## 🚀 **Next up: want a detailed real-world example of merge vs rebase with conflicts & commands?**

Just say **“Show me merge vs rebase example!”** 💪✨