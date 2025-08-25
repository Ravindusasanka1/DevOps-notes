## **GIT**
---

# 🌀 Git Basics Notes

## 🔹 What is Git?

* Git is a **distributed version control system (VCS)**.
* Tracks source code history and allows multiple developers to collaborate.
* Works locally and remotely (GitHub, GitLab, Bitbucket).

---

## 🔹 Git Workflow

1. **Working Directory** – Where you edit files.
2. **Staging Area (Index)** – Snapshot of changes before commit.
3. **Repository (Local Repo)** – Commits are stored here.
4. **Remote Repository** – Hosted repo (e.g., GitHub).

---

## 🔹 Common Git Commands

### 1. Setup & Config

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --list
```

### 2. Starting a Repo

```bash
git init               # Initialize new repo
git clone <url>        # Clone existing repo
```

### 3. Tracking Changes

```bash
git status             # Show repo status
git add <file>         # Stage file
git add .              # Stage all changes
git reset <file>       # Unstage file
```

### 4. Commit Changes

```bash
git commit -m "Message"        # Commit staged changes
git commit -am "Message"       # Stage & commit tracked files
```

### 5. Working with Remotes

```bash
git remote -v                  # Show remotes
git remote add origin <url>    # Add remote
git push origin main           # Push changes
git pull origin main           # Pull latest changes
git fetch origin               # Fetch without merging
```

### 6. Branching

```bash
git branch                     # List branches
git branch <name>              # Create branch
git checkout <name>            # Switch branch
git switch <name>              # Modern switch
git merge <branch>             # Merge branch
git branch -d <name>           # Delete branch
```

### 7. Logs & History

```bash
git log                        # View commit history
git log --oneline --graph      # Pretty log
git diff                       # Show changes
git show <commit>              # Show details of commit
```

### 8. Undoing Things

```bash
git checkout -- <file>         # Discard changes
git reset --soft HEAD~1        # Undo last commit, keep changes staged
git reset --hard HEAD~1        # Undo last commit, discard changes
```

---

## 🔹 Typical Git Workflow Example

```bash
git clone https://github.com/user/repo.git
cd repo
git checkout -b feature-branch
# make changes
git add .
git commit -m "Added new feature"
git push origin feature-branch
```

---

## 🔹 Best Practices

* Write **clear commit messages**.
* Use **branches** for new features/bug fixes.
* Keep `main` (or `master`) branch **stable**.
* Pull before pushing to avoid conflicts.
* `.gitignore` → Ignore unnecessary files (logs, build artifacts, secrets).

---
## **GIT-remote repeositories**



## 🔹 Local Repository Setup

```bash
# 1. Initialize Git inside your project
git init

# 2. Add files to staging area
git add .

# 3. Commit changes
git commit -m "Initial Commit"
```

---

## 🔹 Connecting to Remote Repository

```bash
# Add a remote (give it a name, e.g., github)
git remote add github https://github.com/username/my-application.git

# Push local branch (master/main) to remote
git push -u github master
```

---

## 🔹 Everyday Git Commands

```bash
# See current repo status
git status

# See commit history
git log --oneline

# Add modified files
git add filename.py

# Commit changes
git commit -m "Updated backend logic"

# Push changes to remote
git push

# Pull latest changes from remote
git pull
```

---

## 🔹 Cloning an Existing Repository

```bash
git clone https://github.com/username/my-application.git
```

---

## 🔹 Branching

```bash
# Create a new branch
git branch feature-x

# Switch to a branch
git checkout feature-x

# Or create & switch in one command
git checkout -b feature-x

# Merge a branch into master/main
git checkout master
git merge feature-x
```

---

📌 Your workflow looks like this:

* **Local repo** → (`git add`, `git commit`) → history saved on your laptop
* **Remote repo (GitHub)** → connected with `git remote add`
* Sync with → `git push` (upload) / `git pull` (download)

---

