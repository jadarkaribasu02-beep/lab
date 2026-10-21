# Git Lab Experiments — Commands, Errors, and Solutions

A beginner-friendly reference for completing Git lab exercises from Windows PowerShell. This README records the commands used, the errors encountered, why they happened, and how to fix them.

> **Important:** These notes describe one repository that uses `master` as its target branch. Your repository may use `main` instead. Check your actual branch names before running commands.

## Table of Contents

- [Before You Start](#before-you-start)
- [Experiment 6: Merge a Feature Branch with a Custom Message](#experiment-6-merge-a-feature-branch-with-a-custom-message)
- [Problems Encountered and Solutions](#problems-encountered-and-solutions)
- [How to Verify the Merge](#how-to-verify-the-merge)
- [Push the Result to GitHub](#push-the-result-to-github)
- [Quick Reference](#quick-reference)

## Before You Start

Open PowerShell and navigate to your **local repository folder**. For example:

```powershell
cd C:\Users\YOUR_USERNAME\OneDrive\Desktop\lab
```

Replace the example path with the actual path on your computer.

Check that you are inside a Git repository:

```powershell
git status
```

If Git reports `fatal: not a git repository`, you are probably in the wrong folder. Run `dir` to see the folders available, then use `cd FOLDER_NAME` to enter your repository.

If the repository is only on GitHub and not on your computer, clone it first. Copy the HTTPS URL from GitHub's **Code** button:

```powershell
cd C:\Users\YOUR_USERNAME\OneDrive\Desktop
git clone YOUR_REPOSITORY_URL
cd REPOSITORY_FOLDER
```

Replace the placeholders with your actual username, repository URL, and repository folder name.

## Experiment 6: Merge a Feature Branch with a Custom Message

### Task

Merge `feature-branch` into the target branch and provide a custom merge commit message.

The lab sheet uses `main`, but the repository used for this experiment had `master` as its target branch. Always check the actual branch names first.

### 1. Check local and remote branches

```powershell
git branch
git branch -a
```

- `git branch` lists local branches.
- `git branch -a` lists local and remote-tracking branches.

Example: the feature branch may appear as `remotes/origin/feature-branch`. That means the remote-tracking branch exists, but a local branch named `feature-branch` may not exist yet.

### 2. Create a local feature branch from the remote branch (if needed)

If `origin/feature-branch` exists but local `feature-branch` does not, run:

```powershell
git fetch origin
git switch -c feature-branch --track origin/feature-branch
```

If your Git version does not support `git switch`, use:

```powershell
git checkout -b feature-branch origin/feature-branch
```

If `feature-branch` already exists locally, do not create it again.

### 3. Switch to the target branch

If your target branch is `main`:

```powershell
git switch main
```

If your target branch is `master`:

```powershell
git switch master
```

Older Git versions can use `git checkout BRANCH_NAME` instead.

### 4. Merge with a custom commit message

For a repository using `main`:

```powershell
git merge feature-branch -m "Merge feature branch into main for collaboration"
```

For the repository in this example, which uses `master`:

```powershell
git merge feature-branch -m "Merge feature branch into master for collaboration"
```

A successful merge may show a message such as:

```text
Merge made by the 'ort' strategy.
```

Git may instead report that the branch is already up to date, or it may stop for conflicts. Read the message before continuing.

## Problems Encountered and Solutions

### Problem 1: `cd lab` says the path does not exist

**Error:**

```text
cd : Cannot find path 'C:\Users\YOUR_USERNAME\lab' because it does not exist.
```

**Cause:** PowerShell searched for `lab` inside the current directory, but the folder was not there.

**Solution:**

```powershell
pwd
dir
```

Use `dir` to find the folder and `cd` to enter the correct location. Remember that a GitHub repository does not automatically mean a local folder exists at your current path.

### Problem 2: `fatal: not a git repository`

**Error:**

```text
fatal: not a git repository (or any of the parent directories): .git
```

**Cause:** The current directory is not inside a local Git repository.

**Solution:** Navigate to the repository folder and check again:

```powershell
cd PATH_TO_YOUR_REPOSITORY
git status
```

If the repository has not been downloaded, clone it from GitHub first.

### Problem 3: `git checkout main` says the pathspec did not match

**Error:**

```text
error: pathspec 'main' did not match any file(s) known to git
```

**Cause:** There was no local branch named `main`. In this experiment, the target branch was named `master`.

**Solution:**

```powershell
git branch -a
git switch master
```

Use `main` only if that branch actually exists. Do not rename branches just to match an example unless your instructor requires it.

### Problem 4: `feature-branch` cannot be merged

**Error:**

```text
merge: feature-branch - not something we can merge
```

Git may suggest:

```text
origin/feature-branch
```

**Cause:** The feature branch existed on the remote, but there was no local branch with that name.

**Solution:**

```powershell
git fetch origin
git checkout -b feature-branch origin/feature-branch
```

If the local branch already exists, switch to it instead of creating it again.

### Problem 5: Typing `y` does not fix the merge

**Error:**

```text
y : The term 'y' is not recognized...
```

**Cause:** PowerShell interpreted `y` as a command. Git was not asking a yes/no question at that point.

**Solution:** Read the error and run the correct Git command. Do not type a response unless a command explicitly asks for one.

### Problem 6: Typo in the remote branch name

**Incorrect command:**

```powershell
git merge rigin/feature-branch
```

**Cause:** `origin` was misspelled as `rigin`.

**Solution:** Check the exact branch name and spell it correctly:

```powershell
git branch -a
git merge origin/feature-branch
```

Normally, create a local branch first as shown above, then merge the local `feature-branch`.

### Problem 7: `fatal: refusing to merge unrelated histories`

**Error:**

```text
fatal: refusing to merge unrelated histories
```

**Cause:** Git could not find a common ancestor between the two histories. This can happen when branches were created independently, such as by initializing separate repositories.

**First, stop and inspect the branches:**

```powershell
git log --oneline --graph --decorate --all
git remote -v
git branch -a
```

Make sure you are merging the intended repositories and branches. If the histories are genuinely separate and you intentionally need to combine them, Git supports:

```powershell
git merge feature-branch -m "Merge feature branch into master for collaboration" --allow-unrelated-histories
```

Use the correct target branch name in the commit message. **Do not use `--allow-unrelated-histories` automatically**; it bypasses Git's protection against merging histories with no shared ancestor and may produce conflicts. Resolve any conflicts carefully before completing the merge.

## How to Verify the Merge

After the merge succeeds, run:

```powershell
git status
git log -1
git log --oneline --graph --decorate --all
```

Check that:

1. `git log -1` shows your custom merge commit message.
2. The graph shows the feature branch's history connected to the target branch.
3. `git status` reports a clean working tree, unless you have other uncommitted work.

You can also inspect the most recent commit's parents:

```powershell
git show --no-patch --pretty=raw HEAD
```

A merge commit normally has two parent commit IDs.

## Push the Result to GitHub

After verifying the merge, push the target branch to the remote.

For `main`:

```powershell
git push origin main
```

For `master`:

```powershell
git push origin master
```

Then refresh the GitHub repository page and check the latest commit. If the push is rejected, read the error before trying force-push commands; avoid force-pushing unless you understand the impact and have permission.

## Quick Reference

| Task | Command |
|---|---|
| Show current directory | `pwd` |
| List files and folders | `dir` |
| Check working tree | `git status` |
| List local branches | `git branch` |
| List local and remote branches | `git branch -a` |
| Fetch remote updates | `git fetch origin` |
| Switch branches | `git switch BRANCH_NAME` |
| Create a local branch from remote | `git checkout -b feature-branch origin/feature-branch` |
| Merge with a custom message | `git merge feature-branch -m "Your custom message"` |
| View commit history | `git log --oneline --graph --all` |
| Push `main` | `git push origin main` |
| Push `master` | `git push origin master` |

## Final Notes

- Check the repository path and branch names before running commands.
- `main` and `master` are both possible branch names; they are not interchangeable unless both branches exist.
- A successful local merge does not automatically push the merge to GitHub.
- Keep this README as a reference, but use your own understanding when explaining the experiment to your instructor.
