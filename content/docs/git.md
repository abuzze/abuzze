---
title: "Git Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---

# GITHUB CHEATSHEET

Creating a new local repo
```
git init
git add .
git commit -m "Initial commit"
```
Show commit history
```
git log
```
Reverting everything to the last committed state
```
git reset
git checkout .
```
Hard delete unpublished commits
```
# This will destroy any local modifications.
# Don't do it if you have uncommitted work you want to keep.
git reset --hard 0d1d7fc32
# or switch to the last commit
git reset --hard HEAD
```
Checking unstaged changes
```
git status
```
Pulling from master to local repository
```
git init (only first time)
git remote add upstream <master repository url> # (only first time)
git pull upstream master <master repository url>
```
Pushing from local repository to personal fork. This is
however only necessary when you plan on doing a pull
request to the master
```
git push origin master
```
Committing changes to the local repository:
```
git commit -m <commit message>
```
Committing changes to the local repository, without adding new files (only modified files:
```
git commit -a -m <commit message>
```
### Creating a new repository
 - Go to your own github page @ https://github.com/<github username>
 - Click New repository
 - Define a <repository name>
 - Provide a description
 - Choose public (or private if you wish to pay the required fee of $7.00/month)
 - Choose wheter or not you wish to create the new repository with a README file
 - Click Create repository
 - Open your prefered terminal and execute the following
 ```
git config --global user.name "Your name"
git config --global user.email your@email.domain
git config --global core.editor "subl -w"
mkdir <repository name>
cd <repository name>
git init
 ```
  <place files to be versioned into current directory>
```
git commit -m 'First addition of all files'
git remote add origin git@github.com:<github username>/<repository name>.git
git push -u origin master
```
### Pushing a new repository into github
```
cd <existing repository name>
git remote add origin git@github.com:<github username>/<repository name>.git
git push -u origin master
```
### Creating a new branch and switching to it right away
```
git checkout -b <new branchname> <branchname to branch from>
```
### Show the current branch (marked with *)
```
git branch
```
### create a new branch and change to it
```
git checkout -b <branchname>
```
### Changing to another branch
```
git checkout <branchname>
```
### merging another branch to the current branch
```
git merge <another branchname>
```
### deleting a branch
```
git branch -d <branchname>
```
### deleting a branch that wasn't merged to master
```
git branch -D <branchname>
```
### Reverting a file to its last committed state
```
git checkout -- <file>ra
```

### Renaming a file via git, so it doesn't count as a new file
```
git mv oldfilename newfilename
```
### Deleting a file via git
```
git rm filename
```
### untracking a file that was already commited and tracked, but is now in the .gitignore
```
git update-index --assume-unchanged <file>
```
### retracking that file, please remove from .gitignore before you do this
```
git update-index --no-assume-unchanged <file>
```