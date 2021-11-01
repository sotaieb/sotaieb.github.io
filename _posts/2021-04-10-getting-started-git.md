---
title: "Getting started Git"
categories:
  - Git
tags:
  - Git
---

```sh
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/username/username.github.io.git
git push -u origin main
```

## log

```sh
git log --oneline --graph
```

## reset/checkout/revert/clean

Every branche has two pointers:

- HEAD pointer
- Branch pointer

Initially, the HEAD pointer usually points to the main pointer.
Checking out a file does not move the HEAD pointer.

git checkout: Moves to a specific commit, the repo in a "detached HEAD", This means you are no longer working on any branch.

git reset: Moves both the HEAD pointer and the branch pointer to a specific commit and modify the state of the three trees.

git clean command operates on untracked files.

```sh
# create a new commit with the inverse of the last commit
git revert HEAD

# Changes the commit history tree
git reset --soft HEAD

# Changes the commit history tree and the staging index tree
git reset --mixed HEAD
git reset path/to/file

# Changes commit history tree and the staging index tree and the working directory tree
git reset --hard HEAD

# showing which files are going to be removed (-n), including directory (-d), including ignorid file (-x)
git clean -n -d -x

# execute clean
git clean -dfx
```

## Checkout with tags

```sh
git fetch --all --tags
## listing tags
git tag

$ git checkout -b newbranchname tagname
```

## Pull --merge vs --rebase

```sh
# your local changes are reapplied on top of the remote changes.
git pull --rebase
# your local changes are merged with the remote changes.
git pull --merge
```

## Forcing a pull to overwrite local changes

```sh
git reset -- hard
git pull

##
git reset HEAD path/to/file
git checkout HEAD^ path/to/file
```

## Keeping local and remote changes

```sh
## using commit
git commit
git pull
## using stash
git stash
git pull --rebase origin master ## or git pull
git stash pop
## when conflict
git stash apply
```

## Git checkouts fail with "Filename too long error"

```sh
 New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem"`
   -Name "LongPathsEnabled" -Value 1 -PropertyType DWORD -Force

git config --system core.longpaths true
```
