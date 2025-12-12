# Git: From First Commit to Advanced Workflows

---

## 1. Why Version Control? And Why Git?

### 1.1 Life without version control

Without version control, teams usually:

* Save files as `project-final-v2-FINAL-FINAL.zip`
* Overwrite each other’s changes
* Lose work when laptops die or files get corrupted
* Have no clear history of *what* changed and *why*

Common pain:

* Hard to experiment safely
* Hard to go back when something breaks
* Hard to see who changed what and when
* Hard to collaborate across time zones / teams

### 1.2 What version control gives you

A Version Control System (VCS) solves these problems:

* **History**
  Every change (commit) is tracked with an author, timestamp, and message.

* **Safety**
  You can revert to any previous commit or branch.

* **Collaboration**
  Multiple people can work on the same codebase without constantly conflicting.

* **Experimentation**
  Branches allow you to try ideas without breaking the main code.

* **Traceability**
  You can answer: “When did this bug appear?” and “Which change caused it?”

### 1.3 Why Git specifically?

Git is:

* **Distributed**
  Everyone has a full clone of the repository, including full history. You can work offline.

* **Fast**
  Operations like branching, merging, and viewing history are optimized for speed.

* **Flexible**
  Multiple workflows: centralized, feature-branch, fork & pull request, GitFlow, trunk-based.

* **Standard**
  Most modern tooling, CI/CD, and platforms (GitHub, GitLab, Bitbucket, Azure DevOps) assume Git.

---

## 2. Git Basics

### 2.1 Key concepts

* **Repository (repo)**:
  A directory tracked by Git. Contains your code and a `.git` folder with the history / metadata.

* **Commit**:
  A snapshot of your project at a point in time, with a message explaining the change.

* **Branch**:
  A moveable pointer to a series of commits. Lets you isolate work (features, fixes, experiments).

* **Remote**:
  A copy of the repository hosted on a server (GitHub, GitLab, etc.), used for collaboration.

* **Working directory / staging area / repository**:

  * **Working directory**: your actual files on disk.
  * **Staging area (index)**: the “shopping cart” of changes you plan to commit.
  * **Repository (.git)**: committed history.

### 2.2 Git installation & first-time setup

After installing Git:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Check config:

```bash
git config --list
```

---

## 3. Repos, Commits, Branches, Remotes

### 3.1 Creating or cloning a repo

* Create a new repo:

```bash
mkdir my-project
cd my-project
git init
```

* Clone an existing repo:

```bash
git clone https://github.com/user/repo.git
cd repo
```

### 3.2 The file lifecycle in Git

Typical workflow:

1. Edit files in your working directory.
2. Stage changes with `git add`.
3. Commit staged changes with `git commit`.

Commands:

```bash
git status           # See which files are modified, staged, untracked
git add file1.js     # Stage a file
git add .            # Stage all changes (careful!)
git commit -m "Describe what you changed"
```

### 3.3 Viewing history

```bash
git log              # Detailed history
git log --oneline    # One-line summary of each commit
git log --graph --oneline --all
```

### 3.4 Branches

* View branches:

```bash
git branch           # List local branches
git branch -r        # List remote branches
```

* Create & switch branches:

```bash
git branch feature/login         # Create a branch
git switch feature/login         # Switch to it (or: git checkout feature/login)
# Shortcut: create + switch
git switch -c feature/login
```

* Merge a branch (simple case):

```bash
git switch main
git merge feature/login
```

### 3.5 Remotes

* Check configured remotes:

```bash
git remote -v
```

* Add a remote:

```bash
git remote add origin https://github.com/user/repo.git
```

* Push a branch to remote:

```bash
git push -u origin main       # First push (sets upstream)
git push                      # Next pushes
```

* Pull latest changes:

```bash
git pull                      # Fetch + merge from upstream branch
```

---

## 4. Collaboration Workflows

### 4.1 Centralized workflow

* Everyone pushes directly to `main` (or `master`).
* Simple but risky: easy to break main.

Used in small teams or quick prototypes.

### 4.2 Feature branch workflow

* `main` is always **stable**.
* Each feature / bug fix gets its own branch:

  * `feature/login-page`
  * `bugfix/issue-123`

Typical flow:

1. Update your local repo:

   ```bash
   git switch main
   git pull
   ```

2. Create a feature branch:

   ```bash
   git switch -c feature/login-page
   ```

3. Commit work as you go.

4. Push branch:

   ```bash
   git push -u origin feature/login-page
   ```

5. Open a Pull Request (PR) / Merge Request (MR) on the platform.

6. After review & CI pass, merge to `main`.

### 4.3 Fork & Pull Request (open source workflow)

Common for open source projects:

1. Fork the repository (your own copy under your account).
2. Clone your fork.
3. Create a branch in your fork, commit changes.
4. Push to your fork.
5. Open a Pull Request from your fork’s branch into the original repo’s `main`.

### 4.4 GitFlow (release-oriented)

Branches:

* `main` – production ready
* `develop` – integration branch
* `feature/*`, `release/*`, `hotfix/*`

More complex, useful for projects with formal releases and multiple environments.

### 4.5 Trunk-based development

* One main branch (“trunk”), often called `main`.
* Feature branches are short-lived, merged quickly.
* Heavy use of feature flags to hide incomplete features in production.

---

## 5. Advanced Git

This is where Git becomes a power tool instead of just “save + sync”.

### 5.1 Rebase

**Purpose:** rewrite commit history to make it linear, clean, and easier to understand.

#### 5.1.1 Basic rebase

Scenario: you have `feature/login` based on an older `main`, and `main` has new commits.

```bash
git switch feature/login
git fetch origin
git rebase origin/main
```

This “replays” your commits on top of the latest `main`, creating a clean linear history.

Then push (may need force):

```bash
git push --force-with-lease
```

> Rule of thumb: **never rebase public/shared history** unless your team agrees and knows what they’re doing.

#### 5.1.2 Interactive rebase

Use interactive rebase to:

* Reorder commits
* Squash multiple small commits into one
* Edit commit messages

```bash
git rebase -i HEAD~4
```

This opens the last 4 commits in your editor. You can:

* `pick` – keep commit
* `reword` – change commit message
* `squash` / `fixup` – combine commits

### 5.2 Stash

**Purpose:** temporarily store uncommitted changes without committing them.

Useful when:

* You need to switch branches quickly but don’t want to commit work in progress.

Commands:

```bash
git status                   # See changes
git stash                    # Stash tracked changes
git stash -u                 # Stash tracked + untracked
git stash list               # See stashes
git stash apply              # Apply most recent stash, keep it in list
git stash pop                # Apply and remove from stash list
git stash drop stash@{1}     # Delete a particular stash
```

### 5.3 Reset

**Purpose:** move a branch pointer and optionally modify staging area and working directory.

Three main modes:

1. **Soft reset** – keep changes staged

   ```bash
   git reset --soft HEAD~1
   ```

   * Moves `HEAD` back one commit.
   * All changes become staged (ready to recommit).

2. **Mixed reset** (default) – keep changes in working directory, unstage them

   ```bash
   git reset HEAD~1
   # or explicitly
   git reset --mixed HEAD~1
   ```

3. **Hard reset** – **dangerous**, discards changes

   ```bash
   git reset --hard HEAD~1
   ```

   * Moves `HEAD` and updates working directory to that commit.
   * Any local changes after that commit are lost (unless recoverable via `reflog`).

> Good practice: if unsure, use `--soft` or `--mixed`, and always check `git status` before `--hard`.

### 5.4 Reflog

**Purpose:** “Time machine” for your branch pointers.

Even if you “lose” commits via reset or rebase, Git keeps a log of where `HEAD` and branches have pointed.

```bash
git reflog
```

You’ll see entries like:

```text
abc1234 HEAD@{0}: reset: moving to HEAD~1
def5678 HEAD@{1}: commit: Add login validation
...
```

You can recover a lost commit:

```bash
git reset --hard HEAD@{1}
# or
git switch -c rescue-branch abc1234
```

### 5.5 Bisect

**Purpose:** find the commit that introduced a bug using binary search.

Workflow:

1. Mark a known **good** commit (where the bug didn’t exist).
2. Mark a **bad** commit (where the bug exists).
3. Git checks commits in between; you test and mark them good/bad until the offending commit is found.

Commands:

```bash
git bisect start
git bisect bad              # current commit is bad
git bisect good v1.0.0      # tag/commit where everything worked
# Git moves you to a middle commit
# Run tests or reproduce the bug
git bisect good             # or: git bisect bad
# Repeat until Git prints the first bad commit
git bisect reset            # return to your original branch
```

### 5.6 Other useful advanced commands

* **Cherry-pick** – apply a specific commit from another branch:

  ```bash
  git cherry-pick <commit-hash>
  ```

* **Tagging** – mark specific commits as releases:

  ```bash
  git tag v1.0.0
  git push origin v1.0.0
  ```

* **Clean** – remove untracked files:

  ```bash
  git clean -n    # dry run
  git clean -f    # actually delete
  ```

---

## 6. Best Practices & Resources

### 6.1 Commit message best practices

* Use **present tense, imperative mood**:

  * “Add login form validation”
  * “Fix null reference in user service”
* Keep the subject line short (~50 chars); add detail in the body if needed.
* One logical change per commit:

  * Avoid commits that mix refactoring, formatting, and features.

Commit example:

```text
Add validation to login form

- Validate email format on client and server
- Prevent multiple submissions while waiting for server
- Add unit tests for invalid credentials
```

### 6.2 Branching practices

* Use descriptive names:

  * `feature/user-profile`
  * `bugfix/login-redirect-loop`
  * `chore/update-dependencies`
* Keep branches **short-lived**:

  * Frequently sync with `main` (rebase or merge).
  * Avoid long-running branches with huge merge conflicts.

### 6.3 Collaboration & code review

* Use Pull Requests / Merge Requests:

  * Ensure at least one peer review.
  * Require CI to pass before merging.
* Discuss design in PRs; keep feedback constructive.
* Don’t push directly to `main` in team projects (unless it’s an agreed part of your workflow).

### 6.4 Keep your repo clean

* Use a `.gitignore` to avoid committing:

  * Build artifacts
  * Logs
  * Secrets and local config files
* Never commit secrets (passwords, tokens, private keys).

  * Use environment variables, secret managers, or config files that are not tracked by Git.

### 6.5 Learning resources

You can add links in your own version of this doc; here are categories you can fill in:

* **Official docs**

  * Git documentation (`git help <command>`, `man git-<command>`).
  * Online Pro Git book (free).
* **Interactive tutorials**

  * Websites that simulate Git in the browser (for branching, rebasing, etc.).
* **GUI tools** (for people who prefer visual tools)

  * GitHub Desktop, GitKraken, SourceTree, VS Code built-in Git support.
* **Practice**

  * Create a demo repo just for experiments: rebasing, stashing, resetting, bisecting.
  * Break things on purpose, then try to recover using `reflog`, `reset`, etc.

---
