# Git Tutorial
A document that explains the basic commands to properly use Git.

```shell
git --version
git version 2.34.1
```
## Table of contents

## 1. Git and GitHub
Talk about version control, the difference between Git and GitHub, its use, its benefices..
Use it from scratch / git CLI GUI / use it with and IDE (pycharm / VSCode for example)

## 2. Git's basic internals
Git objects (blob, tree, commit, tag)
Hierarchy of the folder
difference between working directory, staging area and commits
branches
SHA1
the folder .git/
the index


## 3. First steps
In this part, we will talk about the basic commands and the first steps in using Git.
1. Git init (to create a .git folder) else the directory is not considered as a repository.
2. Git config (user name and user email) / show the file .git/config
3. create a file / add content / git status
4. git add / git status
5. git commit / git status
6. create branch / git branch / git checkout
7. man git command


## 4. Intermediate steps
In this part, we will go further in details and see more complicated commands with more complicated situations.
1. .gitignore
1. branching / checkout commits / HEAD
2. parents and child commits
3. merge branches / solve conflicts 
4. alter commits / git commit --amend / git revert / git git reset (soft/mixed/hard) / git rebase -i (squaosh / rename /reorder)
5. finding commits (git blame)
6. stash and reflog
7. git diff


## 5. Remote repositories
In this part, we will talk about remote repositories / how to create a connection between repositories
1. what is a remote repository
2. connection / SSH / HTTPS
3. Hosting a repository in GitHub

## 6. Team work
1. workflows branches
2. methods / concepts
3. pull requests
4. CI/CD
5. hooks
6. commit messages and branch names

include images / snapshots

## 7. References
- O'reilly book : Version Control with Git, 3rd edition (Prem Kumar Ponthorai, Jon Loeliger)
- official git manual
