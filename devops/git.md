# Git — Fundamentals and Practical Reference

**Level:** Beginner  
**Last reviewed:** 2026-11-19  
**Purpose:** Personal technical reference for version control, collaboration, and DevOps workflows.

---

## 1. TL;DR

Git is a distributed version control system used to track changes in files and coordinate development.

Key concepts:

- **Repository:** A project tracked by Git.
- **Commit:** A snapshot of changes.
- **Branch:** An independent line of development.
- **Staging area:** A place to prepare changes before committing.
- **Remote:** A repository hosted elsewhere.
- **Merge:** Combines changes from different branches.
- **Clone:** Creates a local copy of a remote repository.
- **Push:** Uploads local commits to a remote repository.
- **Pull:** Fetches and integrates remote changes.

Typical workflow:

1. Modify files.
2. Inspect changes.
3. Stage selected files.
4. Commit changes.
5. Push commits to a remote repository.

---

## 2. What is version control?

Version control is a system for recording and managing changes to files over time.

It helps developers:

- Track modifications.
- Restore earlier versions.
- Identify when and why something changed.
- Work on separate features without affecting the main development branch.
- Collaborate with other developers.
- Review changes before integrating them.

### Types of version control

| Type                        | Description                                    |
| --------------------------- | ---------------------------------------------- |
| Local version control       | Tracks changes on one computer                 |
| Centralized version control | Uses a central server for version history      |
| Distributed version control | Each clone contains its own repository history |

Git is a distributed version control system.

---

## 3. Git vs GitHub

These are different technologies.

| Git                                  | GitHub                             |
| ------------------------------------ | ---------------------------------- |
| Version control system               | Hosting and collaboration platform |
| Runs locally or on servers           | Provides hosted repositories       |
| Tracks file changes                  | Hosts Git repositories             |
| Works without an internet connection | Usually accessed over a network    |
| Open-source software                 | Web-based service                  |

Other Git hosting platforms include GitLab and Bitbucket.

**Important:** Git does not require GitHub. A Git repository can exist entirely on a local computer.

---

## 4. Basic Git architecture

Git manages changes through three main areas:

1. Working directory
2. Staging area (index)
3. Repository (committed history)

```text
Working Directory
       |
       | git add
       v
Staging Area
       |
       | git commit
       v
Local Repository
       |
       | git push
       v
Remote Repository
```

### Working directory

The files and folders you currently edit.

### Staging area

The area where you select which changes should be included in the next commit.

### Local repository

Stores commits and project history on your computer.

### Remote repository

A repository hosted on another computer or service, such as GitHub.

---

## 5. Installing and configuring Git

Check whether Git is installed:

```bash
git --version
```

Configure the author identity used for commits:

```bash
git config --global user.name "Example Name"
git config --global user.email "example@example.com"
```

These are placeholders, not real credentials.

Check configuration:

```bash
git config --list
```

Check a specific setting:

```bash
git config user.name
git config user.email
```

Configuration scopes:

| Scope      | Description                        |
| ---------- | ---------------------------------- |
| `--system` | Applies to all users on the system |
| `--global` | Applies to the current user        |
| `--local`  | Applies to the current repository  |

Local configuration overrides global configuration.

**Privacy note:** Commit author names and email addresses can be visible in public repository history. Consider using a GitHub-provided noreply email address if you do not want to expose your personal email.

---

## 6. Creating repositories

### Initialize a repository

```bash
git init
```

Creates a new Git repository in the current directory.

### Clone an existing repository

```bash
git clone REPOSITORY_URL
```

Creates a local copy of a remote repository.

Example:

```bash
git clone https://github.com/OWNER/REPOSITORY.git
```

Replace `OWNER` and `REPOSITORY` with the appropriate values.

### Check repository status

```bash
git status
```

Shows:

- Current branch.
- Modified files.
- Staged changes.
- Untracked files.
- Whether the local branch is ahead of or behind its upstream branch.

Run this frequently.

---

## 7. Basic Git workflow

### 7.1 Modify files

Edit files in your working directory.

### 7.2 Inspect changes

```bash
git status
git diff
```

- `git status`: Shows the state of the working directory and staging area.
- `git diff`: Shows unstaged changes.

To inspect staged changes:

```bash
git diff --staged
```

### 7.3 Stage changes

Stage one file:

```bash
git add FILE_NAME
```

Stage all changes in the current directory:

```bash
git add .
```

Staging selects changes for the next commit.

### 7.4 Commit changes

```bash
git commit -m "Describe the changes"
```

Creates a commit containing the staged changes.

A good commit message should describe what changed.

Examples:

```text
Add Docker fundamentals memo
Fix SQL join example
Update Linux command reference
```

### 7.5 Push changes

```bash
git push
```

Uploads local commits to the configured remote repository.

### 7.6 Typical workflow

```bash
git status
git diff
git add FILE_NAME
git diff --staged
git commit -m "Describe the changes"
git push
```

---

## 8. Understanding commits

A commit is a snapshot of staged changes in the repository history.

Each commit contains information such as:

- Unique commit identifier (hash).
- Author information.
- Commit message.
- Timestamp.
- References to parent commits.
- A snapshot of the tracked project contents.

### Inspect commit history

```bash
git log
git log --oneline
git log --graph --oneline --all
```

### Inspect a specific commit

```bash
git show COMMIT_HASH
```

### Good commit practices

- Make commits focused on one logical change.
- Use descriptive messages.
- Avoid committing generated files unnecessarily.
- Review changes before committing.
- Do not create artificial commits just to increase activity.

---

## 9. Branches

A branch is an independent line of development.

Branches allow developers to work on features or fixes without directly changing the main branch.

### List branches

```bash
git branch
```

### Create a branch

```bash
git branch feature-name
```

### Switch branches

```bash
git switch feature-name
```

### Create and switch in one command

```bash
git switch -c feature-name
```

### Delete a branch

```bash
git branch -d feature-name
```

Use `-D` only when you intentionally want to force-delete an unmerged branch.

### Example workflow

```bash
git switch -c feature/add-login

# Make changes

git add .
git commit -m "Add login functionality"
git push -u origin feature/add-login
```

The `-u` option sets the upstream branch, making future pushes and pulls simpler.

---

## 10. Merging branches

Merging integrates changes from one branch into another.

Example:

```bash
git switch main
git merge feature-name
```

This merges `feature-name` into `main`.

### Merge conflicts

A merge conflict occurs when Git cannot automatically combine changes.

Typical causes:

- Different changes to the same lines.
- One branch deletes a file modified by another.
- Incompatible changes to overlapping sections.

General resolution process:

1. Run `git status` to identify conflicted files.
2. Open the affected files.
3. Review the conflicting sections.
4. Edit the files to produce the intended result.
5. Stage the resolved files.
6. Complete the merge commit if Git requires it.

Conflict markers look like:

```text
<<<<<<< HEAD
Current branch changes
=======
Incoming branch changes
>>>>>>> feature-name
```

Remove the markers after resolving the conflict.

Do not blindly accept one side without reviewing the changes.

---

## 11. Working with remote repositories

A remote is a reference to another Git repository.

The default remote created by many cloning workflows is named `origin`.

### List remotes

```bash
git remote -v
```

### Add a remote

```bash
git remote add origin REPOSITORY_URL
```

### Change a remote URL

```bash
git remote set-url origin REPOSITORY_URL
```

### Fetch remote changes

```bash
git fetch
```

Downloads remote updates without integrating them into the current branch.

### Pull remote changes

```bash
git pull
```

Fetches and integrates changes from the upstream branch.

### Push changes

```bash
git push
```

Uploads local commits to the remote branch.

### Fetch vs Pull

| Fetch                                              | Pull                                    |
| -------------------------------------------------- | --------------------------------------- |
| Downloads remote updates                           | Downloads and integrates remote updates |
| Does not change the current branch's working files | Can update the current branch           |
| Useful for inspecting remote changes first         | Useful for updating the current branch  |

---

## 12. Undoing changes

Undo commands can affect uncommitted work or repository history. Understand their effects before using them.

### Discard unstaged changes in a file

```bash
git restore FILE_NAME
```

Discards unstaged modifications to that file.

**Warning:** Those modifications may be difficult or impossible to recover.

### Unstage a file

```bash
git restore --staged FILE_NAME
```

Removes a file from the staging area without discarding its working-directory changes.

### Create a new commit that reverses an earlier commit

```bash
git revert COMMIT_HASH
```

Creates a new commit that reverses the changes introduced by the specified commit.

This is generally safer than rewriting shared history.

### Reset

```bash
git reset --soft HEAD~1
```

Moves the current branch back one commit while keeping the changes staged.

```bash
git reset --mixed HEAD~1
```

Moves the current branch back one commit while keeping the changes unstaged.

```bash
git reset --hard HEAD~1
```

Moves the current branch back one commit and discards changes in the working directory and staging area that are affected by the reset.

**Warning:** `--hard` can permanently discard uncommitted work. Avoid using it unless you fully understand the consequences.

---

## 13. The .gitignore file

`.gitignore` specifies intentionally untracked files that Git should ignore.

Typical examples:

```gitignore
# Environment variables and secrets
.env
.env.*
!.env.example

# Dependencies
node_modules/
venv/
.venv/

# Python cache
__pycache__/
*.pyc

# Operating system files
.DS_Store
Thumbs.db

# Editor settings
.vscode/
.idea/

# Build output
dist/
build/
```

The examples above are general-purpose. Adjust them according to the project.

### Important rules

- `.gitignore` does not automatically untrack files already committed.
- Never assume `.gitignore` is a substitute for reviewing staged files.
- Avoid ignoring files required to build or run the project.
- Do not commit secrets just because the repository is private.

If a file is already tracked, adding it to `.gitignore` does not remove it from Git history.

---

## 14. Git and public repository security

A public repository can be viewed and cloned by anyone.

Before pushing changes, inspect the files being committed.

### Never commit

- Passwords.
- API keys.
- Access tokens.
- Private SSH keys.
- Cloud credentials.
- Database credentials.
- Personal documents.
- Private configuration files.
- Sensitive logs or exported data.

### Review staged files

```bash
git status
git diff --staged
```

Check which files are included in the next commit.

### Check tracked files

```bash
git ls-files
```

Lists files tracked by Git.

### Check whether a file is ignored

```bash
git check-ignore -v FILE_NAME
```

Shows which ignore rule applies to a file.

### If a secret is committed

Simply deleting the file in a later commit is not enough. The secret may remain in Git history.

Take these steps:

1. Revoke or rotate the exposed credential immediately.
2. Assess what systems or accounts it could access.
3. Remove the secret from the repository and history when appropriate.
4. Review access logs if available.
5. Prevent recurrence with secret scanning and safer configuration.

Never paste real credentials into public documentation or commit messages.

---

## 15. GitHub authentication

GitHub supports authentication methods for Git operations, including HTTPS and SSH.

### HTTPS

Repositories can be accessed through HTTPS URLs:

```text
https://github.com/OWNER/REPOSITORY.git
```

GitHub does not accept ordinary account passwords for Git authentication over HTTPS.

Authentication can be handled through GitHub CLI or a personal access token.

### GitHub CLI

GitHub CLI (`gh`) is a command-line tool for interacting with GitHub.

Check installation:

```bash
gh --version
```

Authenticate:

```bash
gh auth login
```

Check authentication status:

```bash
gh auth status
```

GitHub CLI is separate from Git itself.

### SSH

SSH allows authentication using a key pair.

- Private key: Kept securely on your own machine.
- Public key: Added to your GitHub account.

Never share or upload your private SSH key.

---

## 16. Useful Git commands

| Command                   | Purpose                           |
| ------------------------- | --------------------------------- |
| `git --version`           | Check Git version                 |
| `git init`                | Initialize a repository           |
| `git clone URL`           | Clone a repository                |
| `git status`              | Check repository state            |
| `git add FILE`            | Stage a file                      |
| `git commit -m "MESSAGE"` | Create a commit                   |
| `git log --oneline`       | Show compact commit history       |
| `git diff`                | Show unstaged changes             |
| `git diff --staged`       | Show staged changes               |
| `git branch`              | List local branches               |
| `git switch BRANCH`       | Switch branches                   |
| `git merge BRANCH`        | Merge a branch                    |
| `git fetch`               | Fetch remote changes              |
| `git pull`                | Fetch and integrate changes       |
| `git push`                | Push commits                      |
| `git remote -v`           | List remote repositories          |
| `git restore FILE`        | Restore unstaged file changes     |
| `git revert HASH`         | Reverse a commit                  |
| `git stash`               | Temporarily store working changes |
| `git tag`                 | Manage version tags               |
| `git show HASH`           | Inspect a commit                  |
| `git ls-files`            | List tracked files                |

---

## 17. Practical learning exercises

- [ ] Check the installed Git version.
- [ ] Initialize a local repository.
- [ ] Create a Markdown file and track it.
- [ ] Stage and commit a change.
- [ ] Inspect commit history.
- [ ] Modify a file and inspect `git diff`.
- [ ] Unstage a file using `git restore --staged`.
- [ ] Create and switch to a new branch.
- [ ] Make a commit on the new branch.
- [ ] Merge the branch into the main branch.
- [ ] Create a `.gitignore` file.
- [ ] Clone a public repository.
- [ ] Add and inspect a remote.
- [ ] Fetch and pull remote changes.
- [ ] Push a local commit to a remote repository.
- [ ] Practice resolving a merge conflict.

---

## 18. Important distinctions to remember

| Concept                       | Meaning                                             |
| ----------------------------- | --------------------------------------------------- |
| Git vs GitHub                 | Version control system vs hosting platform          |
| `git add` vs `git commit`     | Stage changes vs record a commit                    |
| `git commit` vs `git push`    | Save locally vs upload to remote                    |
| `git fetch` vs `git pull`     | Download updates vs download and integrate          |
| `git restore` vs `git revert` | Restore file content vs reverse a commit            |
| Branch vs repository          | Development line vs project history                 |
| Merge vs rebase               | Combine histories vs replay commits on another base |
| Public vs private repository  | Publicly accessible vs access-controlled            |

---

## 19. Official resources

- [Git Official Website](https://git-scm.com/)
- [Git Documentation](https://git-scm.com/doc)
- [Git Reference](https://git-scm.com/docs)
- [Pro Git Book](https://git-scm.com/book/en/v2)
- [GitHub Docs](https://docs.github.com/)
- [GitHub CLI Manual](https://cli.github.com/manual)
- [GitHub Authentication](https://docs.github.com/en/authentication)

---

## 20. Personal learning notes

Use this section for technical observations and questions. Do not include account credentials, private information, or machine-specific secrets.

### Things I understand

-

### Things I need to practice

-

### Questions

- How does Git store snapshots internally?
- What is the difference between merge and rebase?
- How does Git detect changes between commits?
- What is the difference between `HEAD`, a branch, and a commit?
- How do Git hooks work?
- How are Git workflows integrated into CI/CD pipelines?
- How can Git history be used for auditing and security investigations?

### Related topics

- [ ] Git branching strategies
- [ ] Pull requests and code reviews
- [ ] GitHub Actions
- [ ] Git hooks
- [ ] Git internals and object storage
- [ ] Conventional Commits
- [ ] CI/CD fundamentals
- [ ] Secret scanning
- [ ] Software supply-chain security
