# Git from zero

An introductory guide to the Git version control system and its
integration with the GitHub platform. A reference document based on real-world
cases solved during actual work sessions.

## 1. What is Git

Git is a distributed version control system.

Its main functions are:

- save changes in a project
- go back to previous versions
- work with branches
- upload projects to GitHub
- integrate local changes with remote changes

## 2. Core concepts

### 2.1 Normal folder vs Git repository

A normal folder has no change history.

A Git folder contains a hidden subfolder called `.git`.

If running:

```bash
git status
```

shows the following error:

```bash
fatal: not a git repository
```

it means that folder has not been initialized with Git yet.

### 2.2 States of a file in Git

Git works with three main states:

- modified file
- staged file
- file saved in a commit

Basic workflow:

1. A file is edited.
2. Git detects the change.
3. The file is added with `git add`.
4. The change is saved in history with `git commit`.

## 3. Basic commands

### 3.1 View the current status

```bash
git status
```

This command shows:

- modified files
- files ready for commit
- the active branch
- whether the current branch is ahead of or behind the remote

### 3.2 Initialize a repository

```bash
git init
```

This command creates the `.git` folder.

Then it is recommended to rename the main branch to `main`:

```bash
git branch -m main
```

### 3.3 Add files to the staging area

Add a specific file:

```bash
git add scan.py
```

Add all changes:

```bash
git add .
```

### 3.4 Create a commit

```bash
git commit -m "Update scan.py logic"
```

A commit is a snapshot of the project at a specific moment.

### 3.5 View commit history

```bash
git log
```

Shows the list of commits with their hash, author, and date.

Compact version with one line per commit:

```bash
git log --oneline
```

Graphical version:

```bash
git log --graph --oneline
```

### 3.6 View the changes in a file

Before making a commit it is recommended to review the changes made:

```bash
git diff
```

Shows the differences between the file on disk and the last saved version.

To see the diff of a specific file:

```bash
git diff archivo.py
```

### 3.7 View the configured remote

```bash
git remote -v
```

Shows whether the local repository is connected to GitHub and to which URL.

### 3.8 Connect a local repository with GitHub

Over HTTPS:

```bash
git remote add origin https://github.com/USER/REPO.git
```

Over SSH:

```bash
git remote add origin git@github.com:USER/REPO.git
```

### 3.9 Upload changes to GitHub

First push:

```bash
git push -u origin main
```

Later pushes:

```bash
git push
```

### 3.10 Fetch changes from the remote

```bash
git pull --rebase origin main
```

This command:

- fetches the changes from the remote
- places the local commits on top
- avoids unnecessary merges in most cases

### 3.11 Clone an existing repository

```bash
git clone git@github.com:USER/REPO.git
```

Downloads a copy of the GitHub repository with its full history.

Over HTTPS:

```bash
git clone https://github.com/USER/REPO.git
```

### 3.12 Work with branches

View the active branch and existing branches:

```bash
git branch
```

Create a new branch:

```bash
git branch branch_name
```

Switch to another branch:

```bash
git checkout branch_name
```

Or with the modern syntax:

```bash
git switch branch_name
```

Create and switch to a branch in one step:

```bash
git checkout -b branch_name
```

## 4. Recommended workflow

This is the recommended workflow for a normal project:

```bash
git status
git add .
git commit -m "Clear description of the change"
git pull --rebase origin main
git push -u origin main
```

Explanation of each step:

1. `git status`
   Allows reviewing the changes made.

2. `git add .`
   Stages the changes for the commit.

3. `git commit -m "..."`
   Saves the changes in local history.

4. `git pull --rebase origin main`
   Integrates the most recent changes from GitHub before uploading.

5. `git push -u origin main`
   Sends the work to the remote repository.

## 5. Common errors and their solutions

### 5.1 Error: not a git repository

Example:

```bash
fatal: not a git repository
```

Cause:

- the command was run inside a normal folder
- that folder did not contain `.git`

Solution:

```bash
git init
git branch -m main
```

### 5.2 Error: remote add origin

If the GitHub repository already exists but the local repo is not connected:

```bash
git remote add origin https://github.com/oramirez13/PComb-Parser.git
```

If `origin` already exists and needs to be changed:

```bash
git remote set-url origin git@github.com:oramirez13/PComb-Parser.git
```

### 5.3 Error: password authentication is not supported

Example:

```bash
remote: Invalid username or token. Password authentication is not supported
```

Cause:

- the normal GitHub password was used over HTTPS

Solution:

- use a Personal Access Token over HTTPS
- or use SSH, which is the recommended option for frequent work

### 5.4 Error: permission denied to deploy key

Example:

```bash
Permission to repo.git denied to deploy key
```

Cause:

- GitHub was using an SSH key registered as a deploy key
- not as a personal key of the account

Solution applied:

1. generate a new SSH key
2. add it to the personal GitHub account
3. load it with `ssh-agent`
4. configure `~/.ssh/config`

### 5.5 Verify that SSH works

Command:

```bash
ssh -T git@github.com
```

If the result is similar to:

```bash
Hi oramirez13! You've successfully authenticated...
```

the SSH authentication is configured correctly.

### 5.6 Error: fetch first

Example:

```bash
! [rejected] main -> main (fetch first)
```

Cause:

- the remote repository had commits that the local repo did not have

Solution:

```bash
git pull --rebase origin main
git push -u origin main
```

### 5.7 Error: cannot pull with rebase: You have unstaged changes

Example:

```bash
error: cannot pull with rebase: You have unstaged changes
```

Cause:

- files were modified
- neither `git add` nor `git commit` were run
- Git could not merge unsaved changes with a rebase

Solution:

```bash
git add .
git commit -m "Save local changes"
git pull --rebase origin main
git push
```

### 5.8 Error: Everything up-to-date

Example:

```bash
Everything up-to-date
```

This message does not always mean the change was uploaded.

It can mean:

- there were no new commits
- the changes were still uncommitted

That is why the status should always be checked:

```bash
git status
```

### 5.9 Conflicts in rebase

Example:

```bash
CONFLICT (add/add): Merge conflict in README.md
CONFLICT (add/add): Merge conflict in scan.py
```

Cause:

- the remote contained a file
- the local repository had another file with the same name
- Git could not decide which one to keep

Git marks the conflict as follows:

```text
<<<<<<< HEAD
remote content
=======
local content
>>>>>>> your_commit
```

Solution:

1. open the file
2. decide which content is kept
3. remove the `<<<<<<<`, `=======`, `>>>>>>>` markers
4. save the file
5. mark it as resolved:

```bash
git add README.md scan.py
git rebase --continue
```

### 5.10 Editor error during rebase

Example:

```bash
error: Terminal is dumb, but EDITOR unset
```

Cause:

- Git tried to open an editor to continue the rebase
- the environment had no editor configured

Solution:

```bash
GIT_EDITOR=true git rebase --continue
```

### 5.11 Error caused by pushing in the middle of a rebase

Real case:

```bash
on ... rebase-i ...
git push -u origin main
```

and then:

```bash
! [rejected] main -> main (non-fast-forward)
```

Cause:

- the rebase had not finished yet
- the local branch was still being integrated
- Git does not allow uploading an incomplete or outdated history against the remote

Important rule:

- if the prompt shows something like `rebase-i`
- or Git indicates that conflicts must be resolved and `git rebase --continue` must be run
- then `git push` must not be run yet

The rebase must finish first.

Correct order:

1. open the files in conflict
2. choose the correct content
3. remove the conflict markers
4. save the files
5. run `git add`
6. run `git rebase --continue`
7. only at the end run `git push`

Example:

```bash
git add README.md cerysall.py .gitignore
GIT_EDITOR=true git rebase --continue
git push -u origin main
```

If the conflict appears in several files, the process is the same for all of them.

### 5.12 Practical rule: never push with the rebase still open

Summary:

- `git pull --rebase` can open conflicts
- while conflicts are not resolved, the rebase remains open
- while the rebase remains open, `git push` must not be run

First:

```bash
git add .
git rebase --continue
```

Then:

```bash
git push
```

### 5.13 Error: histories do not share a common ancestor

Real case during a security remediation:

- a repository showed `ahead 54 / behind 54` in `git status`
- `git merge-base HEAD origin/main` returned nothing
- `git log` on both sides had the same commit messages and the same content

Cause:

- the history had been rewritten with `git-filter-repo` (for example, to
  sanitize author emails or remove files from the whole history)
- rewriting changes every commit hash because each hash depends on the
  author, committer, date and parents
- the local clone still had the old history, while GitHub had the rewritten one
- both histories contained the same files, but no commit was shared

How to detect it:

```bash
git merge-base HEAD origin/main
git diff --quiet HEAD origin/main && echo "Trees are identical"
```

How to solve it (adopt the rewritten history):

1. make sure the working tree is clean (`git status`)
2. verify that the file trees are identical (`git diff --quiet HEAD origin/main`)
3. align the local clone with the rewritten GitHub history:

```bash
git reset --hard origin/main
```

4. verify the result:

```bash
git status
git merge-base HEAD origin/main
```

Notes:

- before resetting, save the old HEAD hash (`git rev-parse HEAD`) in case it is needed later
- never force-push the old history over the rewritten one just to reuse the old hashes

## 6. SSH from scratch

### 6.1 Generate a new key

```bash
ssh-keygen -t ed25519 -C "oramirez@gmail.com" -f ~/.ssh/id_ed25519_github
```

### 6.2 Start the SSH agent

```bash
eval "$(ssh-agent -s)"
```

### 6.3 Load the key

```bash
ssh-add ~/.ssh/id_ed25519_github
```

### 6.4 List the loaded keys

```bash
ssh-add -l
```

### 6.5 View the public key

```bash
cat ~/.ssh/id_ed25519_github.pub
```

### 6.6 Add it to GitHub

Path:

`GitHub -> Settings -> SSH and GPG keys -> New SSH key`

Type:

- `Authentication Key`

### 6.7 Configure `~/.ssh/config`

Recommended content:

```sshconfig
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_github
  IdentitiesOnly yes
```

## 7. HTTPS vs SSH

### 7.1 HTTPS

Advantages:

- simple to understand at the beginning
- works immediately after storing a token in `~/.git-credentials`
- reliable fallback when SSH keys are not working

Disadvantages:

- requires a token
- can be annoying when making many pushes

### 7.2 SSH

Advantages:

- better for frequent work
- once configured, usually more comfortable

Disadvantages:

- the initial setup requires more work
- can fail with `Permission denied (publickey)` when the key is not added
  to the GitHub account, when it is registered as a deploy key instead of a
  personal key, or when `~/.ssh/config` is not configured

Real case: on this machine several repositories had `git@github.com:` remotes
that stopped working with `Permission denied (publickey)`. The practical fix
applied was to switch those remotes to HTTPS and let the token in
`~/.git-credentials` handle authentication:

```bash
git remote set-url origin https://github.com/USER/REPO.git
```

Conclusion from real experience:

- SSH is comfortable once fully configured
- HTTPS with a token is the most reliable option and works everywhere
- if SSH fails, HTTPS is a quick and safe fallback

## 8. How to prepare a project for GitHub

Before uploading a project the following should be reviewed:

### 8.1 Files that should be included

- source code
- `README.md`
- `requirements.txt`
- `.gitignore`
- images only if they add value to the project

### 8.2 Files that should not be included

- `venv/`
- `.venv/`
- `__pycache__/`
- `*.pyc`
- logs
- reports generated by execution
- personal editor configurations that do not add value to the project

## 9. Complete example from scratch

Consider a folder with a new Python project.

### Step 1. Enter the folder

```bash
cd /path/to/project
```

### Step 2. Initialize Git

```bash
git init
git branch -m main
```

### Step 3. Create `.gitignore`

Example:

```gitignore
.venv/
venv/
__pycache__/
*.pyc
```

### Step 4. Review files

```bash
git status
```

### Step 5. Make the first commit

```bash
git add .
git commit -m "First commit of the project"
```

### Step 6. Connect GitHub

```bash
git remote add origin git@github.com:USER/REPO.git
```

### Step 7. Upload

```bash
git push -u origin main
```

### Step 8. If GitHub already had content

```bash
git pull --rebase origin main
git push -u origin main
```

## 10. Essential commands

```bash
git status
git add .
git commit -m "message"
git remote -v
git pull --rebase origin main
git push
```

## 11. Best practices

- Always run `git status` before `pull` or `push`.
- Never `push` without having made a `commit`.
- If Git reports unsaved changes, first run `add` and `commit`.
- If Git reports `fetch first`, first run `git pull --rebase origin main`.
- In case of conflicts: open the file, choose the correct content, save, run `git add` and then `git rebase --continue`.
- While in the middle of a rebase, do not `push` until it is finished.
- Do not upload `venv`, `__pycache__`, logs or generated reports.
- Use clear and short commit messages.

## 12. Recommended commit messages

Examples:

- `Create project README`
- `Fix IP validation in scan.py`
- `Update main menu logic`
- `Remove unnecessary files from the repository`
- `Prepare project for GitHub`

## 13. Recommended workflow

Every time a change is made in a project:

```bash
git status
git add .
git commit -m "Description of the change"
git pull --rebase origin main
git push
```

If the project does not have Git yet:

```bash
git init
git branch -m main
git remote add origin git@github.com:USER/REPO.git
git add .
git commit -m "First commit"
git push -u origin main
```

## 14. Summary

By the end of this guide, the student is able to:

- initialize repositories
- connect projects with GitHub
- use SSH or HTTPS with a token correctly
- resolve authentication errors
- understand why a `push` fails
- integrate remote changes with `pull --rebase`
- resolve conflicts in real files
- prepare projects for GitHub
- recognize and solve the case of histories that do not share a common ancestor
  (rewritten history with `git-filter-repo`)

As a next step it is recommended to write a complementary guide of common
errors (`GIT_COMMON_ERRORS.md`) as a quick reference material.
