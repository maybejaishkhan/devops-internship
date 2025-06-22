## 2. Git Project
To start a git project we do `git init`. This creates a `.git` folder in the current folder. 

> This project is a local git project but we can also clone anyone else's existing repo from a remote provider like [Github]() or [Gitlab]() using the `git clone <url>` command.
### 2.1. Project Areas
Every git project has 3 (and also a 4th) areas and it works by managing things between these areas:
1. **Working Directory** --> Where our files/folders live in a git project. Completely untracked.
2. **Staging Area** --> Where the files we select to be committed live.
3. **Local Repository** --> Where all the commits are stores.

**Remote Repository** --> Basically a repository that is somewhere else.

Creating a git project and getting it to GitHub looks like this:
```shell
# Create a repo on GitHub and Copy it's URL.

mkdir project
cd project
git init
git add .
git commit -m "first commit" 

# This renames the branch from "master" to "main". Required.
git branch -M main

git remote add origin <url>
git push -u origin main
```



🔥 **Perfect — let’s deep-dive into `git init` in absolute detail: how it works, what it does inside the `.git` folder, options, best practices, and practical gotchas.**

---

## ✅ **📌 1️⃣ What is `git init`?**

**`git init`** creates a new **empty Git repository** in your current directory.

This turns an ordinary folder into a **Git version-controlled project**.

---

## ✅ **📌 2️⃣ What happens when you run it?**

👉 **Git does 3 main things:**

1️⃣ Creates a hidden `.git` folder at the root of your directory.

2️⃣ Initializes default internal structures:

* Object database (`.git/objects/`)
* Staging area (index file: `.git/index`)
* Configuration (`.git/config`)
* References (`.git/refs/heads/` for branches)
* HEAD pointer (`.git/HEAD`)

3️⃣ The working directory itself remains **unchanged**. No files are auto-tracked — you choose what to track.

---

## ✅ **📌 3️⃣ Folder anatomy after `git init`**

Run:

```bash
git init
```

Now inspect:

```bash
tree -a .git  # or use `ls -a .git` if no tree
```

You’ll see something like:

```
.git/
├── HEAD
├── config
├── description
├── hooks/
├── info/
├── objects/
├── refs/
```

**Breakdown:**

| Item               | What it does                                                |
| ------------------ | ----------------------------------------------------------- |
| `.git/HEAD`        | Points to current branch (`refs/heads/main`)                |
| `.git/config`      | Repo-specific config (user.name, remotes, etc.)             |
| `.git/description` | Used by Gitweb, mostly legacy                               |
| `.git/hooks/`      | Client-side scripts (pre-commit, pre-push, etc.)            |
| `.git/info/`       | Extra info, e.g. exclude file for local ignores             |
| `.git/objects/`    | All commits, trees, blobs stored here (content-addressable) |
| `.git/refs/`       | Contains refs: branches, tags                               |

---

## ✅ **📌 4️⃣ When to use `git init`**

👉 Use it when:

* You have a project folder **not yet under version control**.
* You want to start tracking it **from scratch**.
* You want to create an **empty local repo** and push it later to a remote like GitHub.

---

## ✅ **📌 5️⃣ `git init` vs `git clone`**

| Command           | What it does                                                      |
| ----------------- | ----------------------------------------------------------------- |
| `git init`        | Starts a new repo from scratch.                                   |
| `git clone <url>` | Copies an existing remote repo + configures remote automatically. |

👉 **Tip:** If you already have a remote, `git clone` is easier — `git init` is for when you’re starting from zero.

---

## ✅ **📌 6️⃣ Common variations**

### ✅ Initialize in a specific folder

```bash
git init myproject
# Creates myproject/ and initializes repo inside it.
```

### ✅ Re-initialize an existing repo

```bash
git init --force
```

* Forces re-creation of `.git` if it exists.

---

## ✅ \*\*📌 7️⃣ Initialize a **bare** repository

```bash
git init --bare myproject.git
```

👉 **What is bare?**

* No working directory: just the `.git` contents.
* Used on servers as **central shared repos**.
* Everyone clones/pushes/pulls to/from it.

**Structure:**

```
myproject.git/
├── HEAD
├── config
├── objects/
├── refs/
```

No actual project files.

---

## ✅ **📌 8️⃣ Practical example: Local to remote**

```bash
mkdir myproject
cd myproject
git init

# Add a file
echo "# My Project" > README.md
git add .
git commit -m "Initial commit"

# Create an empty repo on GitHub
# Add remote:
git remote add origin git@github.com:username/myproject.git

# Push your first branch
git push -u origin main
```

---

## ✅ **📌 9️⃣ How to undo `git init`**

If you ran `git init` by mistake, or want to remove Git version control:

```bash
rm -rf .git
```

👉 This deletes **all commits, branches, and history** — your files stay untouched!

---

## ✅ **📌 10️⃣ Pro tips**

✅ `git init` does NOT auto-create a `.gitignore` — you must add that yourself.

✅ You can check whether a folder is a Git repo:

```bash
git rev-parse --is-inside-work-tree
# Outputs 'true' if inside a working tree.
```

✅ Git tracks from the `.git` folder: moving/deleting `.git` breaks your repo.

---

## ✅ **📌 TL;DR**

| Command              | What it does                            |
| -------------------- | --------------------------------------- |
| `git init`           | Create new local repo in current folder |
| `git init myproject` | Make new folder + initialize there      |
| `git init --bare`    | Server-style repo, no working directory |
| Undo                 | `rm -rf .git`                           |

---

## 🚀 **Ready for #2?**

If you want, I’ll break down `git add` next in the same level of detail — just say **“Next: git add!”** 🚀✨
