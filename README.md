# Git Tutorial
A document that explains the basic commands to properly use Git.

```shell
$ git --version
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
author SouheilMaatoug <souheil.maatoug@gmail.com> 1681647630 +0200
committer SouheilMaatoug <souheil.maatoug@gmail.com> 1681647630 +0200
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
Author: SouheilMaatoug <souheil.maatoug@gmail.com>
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
Author: SouheilMaatoug <souheil.maatoug@gmail.com>
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
