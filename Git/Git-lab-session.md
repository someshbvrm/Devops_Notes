
***

# 🧪 Git Hands-On Lab: Branching, Merging, & Collaboration

This guide walks you through a practical Git workflow, from initializing a repository to collaborating via forks and pull requests.

---

## 1. 🚀 Project Setup & Initial Commit

```bash
# Initialize a new Git repository in your current directory
git init

# Check available Git commands (get help)
git --help

# Create your first file
echo "# My Project" > README.md

# Add the file to the staging area
git add README.md

# Check the status of your working directory
git status
# Output: Changes to be committed: new file: README.md

# Commit the staged changes to the repository with a descriptive message
git commit -m "Add initial README.md file"

# Verify the working tree is clean
git status
# Output: nothing to commit, working tree clean
```

---

## 2. 🌿 Branching Workflow

```bash
# Check existing branches. The asterisk (*) shows your current branch.
git branch
# Output: * main

# Create a new branch called 'develop'
git branch develop

# List branches again to confirm creation
git branch
# Output:
#   develop
# * main

# Switch from the 'main' branch to the 'develop' branch
git checkout develop
# Output: Switched to branch 'develop'

# Verify the switch
git branch
# Output:
# * develop
#   main

# Create a new 'login' feature branch FROM the 'develop' branch and switch to it immediately
git checkout -b login develop

# Create a file for the login feature
touch login.jsp

# Add and commit the new feature
git add login.jsp
git commit -m "Add login.jsp page for user authentication"

# View the commit history to see your new commit
git log --oneline --graph
# Output will show the new commit on the 'login' branch
```

---

## 3. 🔀 Merging Branches

```bash
# Switch back to the 'develop' branch to integrate the new feature
git checkout develop

# Merge the 'login' branch into the current branch ('develop')
git merge login -m "Merge login feature into develop"

# Now, to get the feature into production, merge 'develop' into 'main'
git checkout main
git merge develop -m "Release login feature to production"

# Your workflow should be: feature -> develop -> main
```

---

## 4. 🧹 Cleaning Up Branches

```bash
# Delete the 'login' branch now that it has been successfully merged
git branch -d login

# Create another feature branch
git checkout -b register develop
touch register.jsp
git add register.jsp
git commit -m "Add registration page"

# Imagine you decide NOT to merge this feature...
git checkout develop

# Delete the 'register' branch WITHOUT merging (force delete)
git branch -D register
# The -D flag is required for unmerged branches; -d is for merged ones.
```

---

## 5. 🤝 Collaboration with Remote Repositories (GitHub)

### **Cloning & Remotes**
```bash
# Download a complete copy of a remote repository to your local machine
git clone https://github.com/username/repository-name.git
cd repository-name

# View the remote connections (usually 'origin' points to the cloned repo)
git remote -v
# Output:
# origin  https://github.com/username/repository-name.git (fetch)
# origin  https://github.com/username/repository-name.git (push)
```

### **The Standard Open-Source Workflow**
1.  **🍴 Fork** the main project on GitHub (click the "Fork" button). This creates a copy in *your* GitHub account.
2.  **💻 Clone your fork** locally:
    ```bash
    git clone https://github.com/YOUR-USERNAME/repository-name.git
    ```
3.  **➕ Add the original repo** as an `upstream` remote to sync future changes:
    ```bash
    git remote add upstream https://github.com/ORIGINAL-OWNER/repository-name.git
    ```
4.  **🌿 Create a feature branch** and do your work:
    ```bash
    git checkout -b my-new-feature
    # ... make changes ...
    git add .
    git commit -m "Implemented a cool new feature"
    ```
5.  **📤 Push your branch** to your fork on GitHub:
    ```bash
    git push origin my-new-feature
    # Pushes 'my-new-feature' branch to your fork (origin)
    ```
6.  **🔁 On GitHub**, open a **Pull Request (PR)** from `your-username:my-new-feature` to `original-repo:main`.
7.  The project maintainer reviews your PR and **merges** it if approved.

### **Pushing to a Specific Remote Branch**
```bash
# Push your current local branch to a specific remote branch (e.g., 'cloudsoft')
git push origin cloudsoft
```

---

## 6. 🔑 Authentication: Personal Access Tokens (PAT)

Since 2021, GitHub **no longer accepts your account password** for command-line authentication. You must use a **Personal Access Token (PAT)**.

### **How to Generate a PAT:**
1.  Go to your GitHub **Settings**.
2.  Navigate to **Developer settings** > **Personal access tokens** > **Tokens (classic)**.
3.  Click **Generate new token** > **Generate new token (classic)**.
4.  Give it a descriptive **Note** (e.g., "My Laptop CLI").
5.  Set an **Expiration** (e.g., 30 days for security).
6.  Select **scopes**. For basic repo access, check the **`repo`** checkbox.
7.  Click **Generate token**.
8.  **🔐 Copy the token immediately!** You won't see it again.

### **Using Your PAT:**
- The next time you run `git push` or `git pull`, you will be prompted for your username and password.
- **Username**: Your GitHub username.
- **Password**: **Paste the Personal Access Token** you generated.

---

## ✅ **Lab Summary & Best Practices**

- **`git init`**: Start a new repo.
- **`git add & commit`**: Snapshot changes frequently with clear messages.
- **`git branch & checkout`**: Use branches for every new feature or bugfix.
- **`git merge`**: Integrate branches. Merge features into `develop`, and `develop` into `main`.
- **`git push origin <branch>`**: Share your work by pushing a branch to the remote.
- **Fork -> Clone -> Branch -> Push -> Pull Request**: This is the golden workflow for contributing to projects.
- **Use PATs, not passwords**, for authentication with GitHub. Keep your tokens secure!
- **Write clear commit messages** in the imperative mood ("Add feature", "Fix bug").
- **Never force push (`git push -f`) to a shared branch.** It rewrites history and causes problems for collaborators.