# Git Tutorial
Version control comes as an essential brick for software development. It is the tool 
that manages changes and developments and allows collaboration on the IT project by implementing the concepts of agile 
methods in DevOps. The most known Version Control System is **Git**.

In this tutorial, we will explore what Git is and how it works. We will also
talk about some DevOps concepts methods for an IT development project.


## Table of contents
1. Git and GitHub
2. Git's Basic commands
3. Git's internals
4. Intermediate steps
5. Remote repositories
6. Collaboration
7. References

```shell
$ git --version
git version 2.34.1
```


## 1. Git and GitHub
Git is an open-source, version control tool. GitHub is a web-based hosting service for git.

In other words, Git is a software for version control (to keep track of changes, to collaborate on the same project ...) while 
GitHub is a cloud service provider for hosting Git repositories and providing additional services (task automations, project management, ...).


The main idea of a VCS consists of the following:
A shared project hosted on GitHub (called remote repository) and multiple developers contributing to it. 
Each developer has a local copy of the project (local repository), makes changes (e.g. modifying code) and sends them to 
the remote project. Although, this type of collaboration can only be done by following some management rules, or it will 
turn into a mess.

![gitconcept](/images/git_repositories.png)

## 2. Basic commands
In this part, we will talk about the basic commands and the first steps in using Git.

1. **Configuration**: 
   1. User: (user identity to be used by Git in the metadata)
    ````shell
    $ git config user.name "jon doe"
    $ git config user.email "jon.doe@example.com"
    ````
    2. Editor: (default text editor when Git asks you to edit a message: nano for example)
    ````shell
    $ export GIT_EDITOR=nano
    ````
2. **Creating a repository**
````shell
$ git init
````
In a folder, run the above command to turn it into a Git repository. the ``git init`` command creates a hidden directory 
called *.git* where all revision information are stored.
use the option ``--b main`` in case you want to change the default name of the initial branch to *main*.

3. **Adding a file**

Once our new empty repository is created, we can now add files and revise them. 
````shell
$ echo "Hello Git!" > file1
````
![gitconcept](/images/git_modifpng.png)
The ``file1`` is now created in our working directory, but it is still untracked i.e. not yet tracked by Git.
To do that we will use the `git add` command and run `git status` to have the status of Git after the modification:
````shell
$ git add file1
````
![gitconcept](/images/gitadd.png)
the new file is now tracked by Git, but now, we need to validate this change and save a new version of the project (containing this new file):
So let's commit our first changes.
````shell
$ git commit -m "add a new file"
````
![gitconcept](/images/gitcommit.png)

After that, one can use `git push` to update the remote repository.  
The mechanism works as follows:

![git diagram](/images/git_diagram.png)


4. **View history**

you can view your commits' history using `git log`:
````shell
$ git log
commit 2658a45cc547f3d29b4683e8cebd0d52f1d929cb (HEAD -> main)
Author: Jon Doe <jon.doe@example.com>
Date:   Sun Apr 16 14:20:30 2023 +0200

    second commit

commit 812db24fe117bc451557936861d74339319106e5
Author: Jon Doe <jon.doe@example.com>
Date:   Sun Apr 16 14:09:20 2023 +0200

    add a new file
````

without any options, this command will output a sequence of individual commits' information. 
that contains the **commit ID (SHA1)**, the commit author, the date of the commit and its message.

Note (HEAD -> main) in the first commit is to say that this is the tip of the branch main.
you can use the option ```--oneline``` or ``--graph``.

To have more details about the commit ID, you can use the ```git show``` that shows objects.
for example:
````shell
$ git show 2658a45cc547f3d29b4683e8cebd0d52f1d929cb
commit 2658a45cc547f3d29b4683e8cebd0d52f1d929cb (HEAD -> main)
Author: Jon Doe <jon.doe@example.com>
Date:   Sun Apr 16 14:20:30 2023 +0200

    second commit
````

to view commit differences. you can use the ``git diff`` command which show changes
between commits, commit and working tree, etc
````shell
$ git diff HEAD^ HEAD
diff --git a/file1 b/file1
index 106287c..42eada1 100644
--- a/file1
+++ b/file1
@@ -1 +1,2 @@
 Hello Git!
+new line
````

- removing and renaming files in your repository:
``git rm`` (to delete staged file) and ``git mv`` to rename staged file.
Note that you need to commit this change but not to add the file.


## 2. Git's basic internals
Git objects (blob, tree, commit, tag)
Hierarchy of the folder
difference between working directory, staging area and commits
branches
SHA1
the folder .git/
the index
### Git object store
-blobs: binary long object: to refer to any file containing any data.
a blob contains a file's data but does not contain any metadata about it or even its name.
- trees: A tree object represents a directory information. It records blob identifiers, pathnames and some metadata for all files in the directory.
It can reference another subtree and thus build a complete hierarchy of files and subdirectories.
In simple terms, a tree records the contents of a single level in the directory hierarchy.
It lists files and subtrees by including their name and an identifier for the Git object they represent.
- Commits: A commit object holds metadata for each change introduced into the repository,
including the author, committer, commit date, and log message.
Each commit points to a tree object that captures, in one complete snapshot, the state of the repository at the time of the commit.
- Tags: a tag object assigns a human-readable name to a specific object, usually a commit.

#### Index
the index stores binary data and is private to your repository.
the content of the index is temporary and describes the structure of the entire repository
at a specific moment in time. It represents a cached representation of the all
the blob objects that reflects the current state of the project.
The index keeps records of staged changes (after git add)


#### SHA1
Each object in the object store is associated with a unique name produced by applying
SHA1 to the content of the object, yielding a SHA1 hash value.
sha1 values are 160-bit values that are represented as a 40-digit hexadecimal number such as 
`2658a45cc547f3d29b4683e8cebd0d52f1d929cb`
sha1 always computes the same ID/hash for identical content.

Git records each pathname and index itby a hash value. This information is stored
in the Git object store as the *tree* object

#### To resume
when a file is created and staged in the index directory by ``git add``
Git internally created a blob object. creates its SHA1 and enters it into
the object store as file named after its hash. git adds / after the first two bits (a mechanism to improve filesystem)

you can verify its content using:
````shell
$ git cat-file -p hash
````

Once the blob is saved in the object store. let's look how it is associated
with a filename. the pathname is stored in a tree object. the index contains the pathnames.

````shell
$ git ls-files -s
100644 42eada1675223b525cd3b0d3ccad745b18d7b7f0 0       file
````
the tree object can be produced by ``git write-tree``
you can see the association of the filename and the hash of the blob.

now that the blob and the tree are in the object store, we can create a commit
````shell
$ git cat-file -p 2658a45
tree a2645b856788b36c90dfb5ed5861f6c9dff03eee
parent 812db24fe117bc451557936861d74339319106e5
author Jon Doe <jon.doe@example.com> 1681647630 +0200
committer Jon Doe <jon.doe@example.com> 1681647630 +0200
````

the commit object contains your name and the time you made the commit,
the commit object refer to a tree object.
the name of a tree object that actually identifies the associated files
the author and date of change
the commiter
the commit message
. commit object

finally tag objects are simple references to a particular commit object.

````shell
$ git tag -a V0.1 commit-hash
````
entre editor and enter text. then ``git rev-parse V0.1`` to get the corresponding hash.
then ``git cat-file -p tag-hash``

to see more about versioning: https://semver.org/


---

configuration file:
- *.git/config*: configuration file manipulated by default or --local
- *~/.config*: user-specific configuration settings with --global option



## 3. First steps
In this part, we will talk about the basic commands and the first steps in using Git.
1. Minimum git configuration: the very first step before being able to commit any changes is the configuration
of the identity of the user which will be used by Git in the metadata.
````shell
$ git config user.name "jon doe"
$ git config user.email "jon.doe@example.com"
````
you can also configure the editor because sometimes you will be asked to edit commit messages in a text editor.
````shell
$ export GIT_EDITOR=nano
````
to choose ``nano`` for example.

2. Git init (to create a .git folder) else the directory is not considered as a repository.
To convert a directory to a Git repository:
````shell
$ git init
````
the ``git init`` command creates a hidden directory called *.git* where all revision information are stored.
use the option ``--b main`` in case you want to change the default name of the initial branch.

3. create a file / add content / git status: 
Once our new empty repository, we can now add files and revise them. 
````shell
$ echo "Hello Git!" > file1
````
The ``file1`` is now added to our working directory, but it is still untracked i.e. changes' metadata still not tracked by Git.
To do that :
````shell
$ git add file1
````
````shell
$ git status
````
if you choose to make modification before committing, you will see that there is one version (snapshot) of ``file1`` in
the staging area (the first added line) and another version not staged (the file with the new line). ``git status`` will give the details
and you can choose to do want you want. you can commit the staging version, then add the second version and have a second commit, or 
discard changes using ``git restore file1`` or add it and have a single commit.

Now let's commit our first changes.
````shell
$ git commit -m "initial commit"
````

the mechanism is as follows:
include image from working directory -> (git add) -> index/staging directory -> git commit -> local history (.git/)

you can also view your commits' history:
````shell
$ git log
commit 2658a45cc547f3d29b4683e8cebd0d52f1d929cb (HEAD -> main)
Author: Jon Doe <jon.doe@example.com>
Date:   Sun Apr 16 14:20:30 2023 +0200

    second commit

commit 812db24fe117bc451557936861d74339319106e5
Author: Jon Doe <jon.doe@example.com>
Date:   Sun Apr 16 14:09:20 2023 +0200

    create initial commit
````
without any options, this command will output a sequence of indivual commits' information. 
that contains the commit ID (SHA1)
the commit author, the date of the commit and its message.
Note (HEAD -> MAIN) in the first commit to say that this is the header of this branch.
you can use the option ```--oneline``` or ``--graph``

to have more details about the commit ID, you can use the ```git show``` that shows objects.
for example:
````shell
$ git show 2658a45cc547f3d29b4683e8cebd0d52f1d929cb
commit 2658a45cc547f3d29b4683e8cebd0d52f1d929cb (HEAD -> main)
Author: Jon Doe <jon.doe@example.com>
Date:   Sun Apr 16 14:20:30 2023 +0200

    second commit
````

to view commit differences. you can use the ``git diff`` command which show changes
between commits, commit and working tree, etc
````shell
$ git diff HEAD^ HEAD
diff --git a/file1 b/file1
index 106287c..42eada1 100644
--- a/file1
+++ b/file1
@@ -1 +1,2 @@
 Hello Git!
+new line
````

- removing and renaming files in your repository:
``git rm`` (to delete staged file) and ``git mv`` to rename staged file.
Note that you need to commit this change but not to add the file.


#### Branches
a branch allows the user to launch a separate line of development within the project.
this allows development to progress in multiple directions simultaneously.
each commit you create will be applied to only one of the branches, the one which is marked active.

The branch name will always refer to the most recent commit on the branch, which called the *HEAD*.
a branch name a simple pointer to a specific commit.

when you create a new branch, it is always based upon an existing commit within the repository.
it can be the HEAD commit or a different commit that you reference explicitly using its hash value.

The basic form of the command is as follows:
````shell
$ git branch branchname start-commit
````
if you don't specify a ``start-commit``, the default point will be the HEAD on the currently active branch.

You need then to switch to the new branch in order to make changes in it.
you can list branches using ``git branch``


#### viewing branches and their commits
the ``git show-branch`` provides more details than ``git branch``
its output is divided into two parts, separated by a line of dashes.

the section above the separator lists the following:
- the name of branches in square brackets, one per line
- each branch name prefixed with special characters: (*) to denote the current branch and (!) for other branches.
- each branch name with its commit message from the most recent commit

the section below the separator lists each commit together with its branch name:
- stating which commits are present in each listed branch
- prefixed with special characters: (+) the commit is present in the listed branch, (*) indicates that the commit is present in the
current branch, and (-) denotes that the commit is a merge commit on the branch

```git checkout branchname```

if you have uncommitted changes, Git refuses to switch branch and ask you to commit changes first.


Note that ``git checkout`` works also with files to restore their states.
for example, git checkout file, when it's deleted or modified but not yet staged.

``git restore`` do the same but what are the differences?

you can check the history of a file using :
````shell
$ git log --follow -p -- filename
````

create and checkout a new branch
````shell
$ git checkout -b branchname
````

#### DETACHED HEAD
you can checkout to any commit in the log history. you will be in a state known as detached head mode.
because the branch and the head point to different commits.
you are free to add new commits as experimental changes in your development project.

those commits are also known as unreachable commits. In other words, there is no permanent
reference to the commits other than the head. when you switch to another named branch, the commits will disappear.
if you decide to keep those commits, you must first create a new branch

deleting branch
````shell
$ git branch -d branchname
````
a warning message stating that branchname is not fully merged means that there are changes that are not included in the main branch.
you are loosing some developments. Git is keeping you from accidentally losing content from the branch to be deleted.
to force you can use -D option.

in case the content of the wanted to be deleted branch is present in another branch, you can check that out and then procede to the deletion safely.


## Commits
every commit represents a single atomic changeset with respect to the previous state.
A commit snapshot represents the state of the total set of modified files ans directories.
a changeset between two snapshots represetns a complete transformation from one tree state to another.


#### viewing old commits
``git log`` output will include every associated commit and its log messages reachable from the specified starting point (by default HEAD)

it is possible also to give a range, for example ``git log HEAD~3 HEAD~5``
 
- git log graphs:
#TODO add ranges chapter 4

## Merging branches
Merging is combining two or more different lines of development.
It unifies two or more commit histories of branches. All the branches to be merged must be in the same repository.
when modifications in one branch do not conflict with modifications found in another branch, Git computes a merge result
and creates a new commit that represents the new, unified state.

``git merge``: your current branch is always the target branch, and changes from other branches are merged into the current branch.

1. Preparing for the merge:
As a general rule, your git life will be much easier if you start each merge with a clean working directory and index.

#TODO find some graphs

2. merge with a conflict


## git diff
explain some output variables of the git diff command.

git diff as unix command

- git diff: show the difference between your working directory and the index. It tells what can be staged for your next commit.
- git diff --cached commit: show the difference between the staged changes in the index and the current commit. It tells the
difference between what's in your index and what's permanently stored in the repository.
- git diff commit: summarizes the differences between you working directory and the given commit.
- git diff commit1 commit2: displays the differences between the two commits.
#TODO more on git diffs with examples

## Altering commits
there are several schools of thought. One philosophy might be termed *realistic history*: every commit is retained and nothing is altered.

on variant of this known as a *fine-grained* realistic history, where you commit every change as soon as possible.
another option is *didactic* realistic history, where you take your time and commit only your best work at convenient and suitable moments.

Given the opportunity to adjust the history -- possible cleaning up a bad intermediate design decision or
rearranging commits into more logical flow -- you can create a more 'idealistic' history.

### git revert
```git revert commit``` applies the inverse of the given commit. generally this command is used to introduce a new commit thet
reverses the effects of a given commit.
the revert doesn't alter the existing history within a repository. Instead, it adds a new commit to the history.
it is considered as a safe command (ultimately for shared repositories)

### git commit --amend
change the HEAD commit
the commit has the same content but some aspects requires adjustment or tidying.
a frequent use of git commit --amend is to fix typos right after a commit. however this is not the only use.
can amend any file or files and can add or delete a file as part of the commit.
give an example

### git reset
the git reset changes your repository and working directory to a known state.
the git reset adjusts the HEAD ref to a given commit and, by default, updates the index to match that commit.
git reset can also modify your working directory to mirror the revision by a given commit.

git reset is considered as a destructive command because it can overwrite and destroy changes.

the git reset has three main options --soft, --mixed and --hard.

- git reset --soft commit:
the soft option changes the head ref to point to a given commit. the contents of your index and working directory are left unchanged.
It will move the commits above in the staging area.
example: suppose you make a commit, then you add a file and realize that you need to combine this staged change with the previous commit.
go back and do it correctly:
commited files are moved to staged are. you can then combine the files in one commit or commit them in another order.
``git reset --soft HEAD^`` return to the previous commit.


- git reset --mixed commit:
it changes the head to point to the given commit. your content are also modified to align the tree structure.
It will move the commits above in the working directory.
``git reset --mixed HEAD^`` return to the previous commit.

commited files return to working directory. In that case you can add one by one or choose in which order to add and commit them.

- git reset --hard commit:
it changes the head ref to point to the given commit. The content of the index are also modified to agree with the tree structure.
your working directory contents are changed to reflect the state of the tree represented by the given commit.
modifications are then lost and new files added but not tracked will stay untouched.
hard reset comes back to the chosen commit and deletes commits above.


### git rebase
it is used to alter where a sequence of commits is based.
this command requires at least the name of the other branch onto which your commits will be relocated. By default, the commits
from the current branch that are not already on the branch are rebased.

a common use for git rebase is to keep a series of commits that you are developing up to date with respect to another branch.


### git rebase -i
for reordering, editing, removing, squashing multiple commits into one, and splitting one commit into several commits.


##TODO
stash and reflog
then remote repositories

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
