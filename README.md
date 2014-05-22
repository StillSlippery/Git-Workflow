
# Git Workflow

This document provides information about a small-team development workflow 
using Git, GitHub, and the "pull request model". It begins with a conceptual 
overview to familiarize the reader with terminology. It then provides 
step-by-step instructions for setting up a repository and performing common 
tasks as they relate to this distributed development model.

## Overview

Git is a distributed development system. As such, each clone of a repository is 
itself a complete repository in its own right.

When a user commits a set of code, the commit occurs on the "local copy" of 
the repository. Then, when a user performs a push on their code, they do so to
a "remote repository" (often shortened to simply "remote").

### Roles

In small-team distributed development on GitHub, there are two general roles:

* __Developer__ responsible for creating features and fixing issues in a local 
copy of the Git repository, pushing these changes to their remote fork, and 
then issuing pull requests from their remote fork.
* __Integration Manager__ responsible for reviewing pull requests and merging 
accepted changes back into the canonical repository.

### Repositories

In the case of the pull request model in small-team development, there are 
three instances of the repository that a developer must understand:

* __Canonical Remote__ contains the official copy of the source code. This 
repository resides on GitHub and is managed by the integration manger. When a 
developer wishes to sync their local copy to the latest version, they pull from 
the canonical remote. However, under no normal circumstance should a developer 
ever push commits to the canonical repository. Instead, code makes its way 
to the canonical repository through a pull request from a remote fork.
* __Remote Fork__ is a clone of the repository to which a developer pushes 
their commits. This repository resides on GitHub, and each user is responsible 
for managing their own remote fork. Once a developer has committed their code 
to a remote fork and it is ready for primetime, they issue a pull request that 
the integration manager reviews and then either merges into the canonical 
remote or sends back for further changes.
* __Local Copy__ is a clone of the remote fork that lives on a developer's 
machine. When a developer makes changes, commits are made to this instance of 
the repository. Generally, a developer will pull from the canonical remote to 
get the latest official version of the code and then push to their remote fork.

### Development Workflow

Generally, a development workflow follows this style:

1. Developer creates a branch on their local repository for a feature.
2. Developer makes code changes and commits to this branch of the local copy.
3. Developer pushes the commits on this branch up to their remote fork and 
then returns to step 2 if work is still in progress or else moves to step 4.
4. When a feature is complete, the developer submits a pull request from their
remote fork.
5. The integration manager reviews this pull request. If it is insufficient to 
meet the intended needs, then a discussion begins in the pull request and the 
developer may commit additional changes to this branch on their local copy and 
push these changes to their remote fork, updating the pull request.
6. When the pull request is deemed sufficient to become part of the canonical 
version, the integration manager accepts and merges the pull request.

Variations may exist around certain edge cases, but this document will provide 
details primarily on how to handle this workflow.

## Getting Started

### Creating a new repo based on drupal-WebBlocks

A new repository can be created based on drupal-WebBlocks with the following steps:

1. Log in to GitHub
2. Press the "Create a new repo" icon (top right corner). Set owner to ```ucla```. Set repository name to ```drupal-[new-project]```. Set to Private.
3. On your local workspace, create a new directory for your project and initialize it with git.

        mkdir [new-project]
        cd [new-project]
        git init

4. Establish remotes for the base drupal-WebBlocks repository and newly-created upstream repository.

        git remote add base https://[ACCOUNT-NAME]@github.com/ucla/drupal-WebBlocks.git
        git remote add upstream https://[ACCOUNT-NAME]@github.com/ucla/drupal-[new-project].git

5. Pull the code from the base repo to your local repo, then push it back up to the newly created upstream repo.

        git pull base master
        git push upstream master

    Note: git push upstream master must be done by someone with write privilege on the ucla organization, unless the newly created repo already has other permissions assigned.

6. Go to https://github.com/ucla/drupal-[new-project] and press Fork.
7. Go back to the local workspace and establish a remote for your fork of the repository.

        git remote add origin https://[ACCOUNT-NAME]@github.com/[ACCOUNT-NAME]/drupal-[new-project].git

8. Push the code to your remote fork and set it as the default.

        git push -u origin master

### Creating a GitHub fork

A GitHub fork can be created with the following steps:

1. Log in to GitHub
2. Go to https://github.com/ucla/drupal-WebBlocks
3. Press the "Fork" button and fork to your local account

### Setting up a local repository

Once you have your fork set up, you need to clone it as a local repository on 
your computer.

```
git clone https://[ACCOUNT-NAME]@github.com/[ACCOUNT-NAME]/drupal-WebBlocks.git [DIRECTORY-NAME]
```

This will automatically set up a remote called `origin` that refers to the 
repository that you cloned and checks out the default branch (usually 
`master`).

Because this is a *fork* of the canonical repository, you need to make 
sure that you can also pull commits from that repository as well. As such, you 
should establish a second remote:

```
git remote add upstream https://[ACCOUNT-NAME]@github.com/ucla/drupal-WebBlocks.git
```

NOTE: You can use `https://github.com/..` if using Git 1.7.4 or above.

## Workflow Tasks

### Branching for a feature

Pull requests are made from a branch. As such, features should generally be 
developed on a branch, not directly on `master`.

Before creating a branch, start by making sure you are on the `master` branch 
and have all changes from the canonical repository:

```
git checkout master             # switch to the master branch
git pull origin master          # for good measure as this should already align with origin
git pull upstream master        # to fetch the latest set of changes from the canonical remote
git push origin master          # update the remote fork with the latest changes from the canonical remote
```

Creating a new branch is then a single command:

```
git checkout -b [BRANCH-NAME] master
```

This command says to create a new branch named `[BRANCH-NAME]` that starts out 
identical to the `master` branch.

You should then push this branch to the remote fork:

```
git push origin [BRANCH-NAME]
```

### Committing changes

Once you've got a branch, you can start making changes. Simply edit files as 
usual and test on your local copy.

When you're ready to designate a change to go into a commit, you need to add it:

```
git add [FILE-NAME]
```
This command will add files to a staging area to wait for commit.  Files in this staging area are not monitored for changes. If you change the file, you must run the command again to add the changes.

The `[FILE-NAME]` value can be a file or directory. Further, if you use `*` as 
the file name, then you will add all changed files in your local copy. Use this 
with caution if you have configuration files that are not in your `.gitignore` 
file. You can also specify multiple files/directories as a set of 
space-separated paths. All paths are local to your current working directory.

Before committing, you should ensure that the right files will be included in
the commit:

```
git status
```

This command will show you which files are added for commit, which have been 
changed but not yet added, and which are not yet under version control. This 
thus allows you to add only a subset of all the files that have changes, in 
order to make your commits coupled tightly to specific features even if you 
have a larger total set of changes.

When you've got all files added for a commit, issue the following:

```
git commit -m "[COMMIT-MESSAGE]"
```

The `[COMMIT-MESSAGE]` should have a first line that is 50 characters or less
in length. At most, it should be 72 characters. If your commit has additional 
details, enter an empty line and then additional lines, each no more than
72 characters in length. This is to ensure proper formatting when viewing
commits in GitHub and through the terminal.

A long commit message might be as follows:

```
git commit -m "[SHORT-DESCRIPTION]

[LONGER-DESCRIPTION...
...
...]"
```

Once you have issued a commit, it becomes part of the version history in your 
local copy for your current branch. It has not yet been pushed to the remote 
fork.

### Pushing commits to the remote fork

Once you've got one or more commits that you'd like to push to your remote 
fork, you should issue the following command:

```
git push origin [BRANCH-NAME]
```

Shorthand notation, if you've already pushed the branch at least once, is:

```
git push
```

Be aware that this shorthand notation will push all commits in branches that 
exist in your remote fork. There's no negative to pushing multiple branches at 
once through this shorthand, but using the longhand syntax ensures that the 
branch will be pushed if it doesn't already exist on the remote fork.

It is common during development on a branch to push multiple times. This 
ensures that your collaborators can see what you're working on and also that 
you've got a remote backup in case your local copy is somehow rendered 
inoperable.

### Issuing a pull request

Merge requests are a feature of GitHub. When you've completed work on a feature 
in a branch (or `master`, although it is not advised to develop on `master`), 
you can issue a pull request as follows:

1. Go to your remote fork on GitHub
2. Select your branch from the dropdown to the left of the "Files" tab
3. Press the "Pull Request" button
4. Ensure that the proper branches and repositories are selected
5. Enter details about the pull request and assign it to the integration manager

Outstanding pull requests can be found in the "Pull Requests" tab on the 
canonical remote. The integration manager can then review the changes 
(including the commits, files changed and an actual diff of the code) and 
either start a discussion in the pull request issue to prompt additional 
changes or, if everything looks good, merge the branch.

If more commits are made by the developer to the branch and pushed to the 
remote fork, then they will automatically be added to the pull request. This 
creates a positive feedback loop for incrementally improving a pull request 
until it is ready to be merged into the canonical remote as official code.

### Handing merge conflicts in pull requests

Although this is rare in small-team development, it may not be possible for 
GitHub to merge the change automatically. If this occurs, the merge has to be 
done manually.

One approach for doing this involves the integration manager cloning the 
canonical repository, adding a remote for the developer's remote fork, pulling 
the branch into the local copy of the canonical repository, handling merge 
conflicts manually, and then committing and pushing the working merge back to 
the canonical remote.

However, this is not the ideal approach. Instead, generally the integration 
manager should inform the issuer of the pull request that they need to update 
the branch to the latest version and handle the merge on their end.

To do this, a developer should start by updating the master branch of their 
local copy:

```
git checkout master             # switch to the master branch
git pull origin master          # for good measure as this should already align with origin
git pull upstream master        # to fetch the latest set of changes from the canonical remote
git push origin master          # update the remote fork with the latest changes from the canonical remote
```

This brings the `master` branch of the local copy up to the current version.
Now the developer just needs to merge it into their branch. The first bit of
this process is handled by Git:

```
git checkout [BRANCH-NAME]
git merge --no-ff master
```

If the `merge` command reports conflicts, then the developer needs to go into 
the files with conflicts (as reported), consolidate the two sets of changes, 
and then add all of those files and file a commit.

```
git add [CONFLICTED-AND-FIXED-FILES]
git commit -m "[COMMIT-MESSAGE-ABOUT-MERGE]"
```

As with any development, once the commit has occurred, it must then be pushed 
to the remote fork:

```
git push orgin [BRANCH-NAME]
```

Once this is done, as with all changes to a branch with an outstanding pull 
request, the changes will be added to the pull request. The developer should 
then post on the pull request that the branch has been updated and can now be 
merged cleanly.

## Useful Commands

### Working on multiple branches

It is possible to work on multiple features at once, each of them in their own 
branch. For a branch that already exists, you can switch to it with the 
`checkout` command:

```
git checkout [BRANCH-NAME]
```

This is the concept in play in the above sections when we switch to master to 
pull updates from the upstream.

### Merging branches

Sometimes, you may find that a feature on one branch is needed for development 
of a feature on another branch. The `merge` command shown earlier can do this:

```
git checkout [BRANCH-TO-MERGE-CHANGES-TO]
git merge --no-ff [BRANCH-TO-MERGE-CHANGES-FROM]
```

### Reviewing changes

The `diff` command is a powerful way to see how things have changed.

For files that you have not yet issued an `add` on:

```
git diff [FILE-NAME]
```

As with other Git commands, `[FILE-NAME]` may be a file, directory or a 
space-separated list. To get a diff of all files, simply neglect the 
`[FILE-NAME]` component:

```
git diff
```

The same rules exist for `[FILE-NAME]` in all below examples (namely that it's
optional and can be a file, directory or space-separated list).

If you have already used the `add` command but would like to see differences
between your last commit and the files that have not yet been committed (even 
if they have been added):

```
git diff HEAD [FILE-NAME]
```

You can also review differences between two commits:

```
git diff [OLDER-COMMIT-ID]..[NEWER-COMMIT-ID] [FILE-NAME]
```

### Working with other remotes

While the scheme described thus far involves a hierarchical scheme from
`local -> remote fork -> canonical remote`, Git is truly distributed in that 
you can actually pull changes into your local repository from other remotes.

To do this, start by adding a new remote:

```
git remote add [REMOTE-NAME] [REMOTE-LOCATION]
```

Once this has been added, you can pull onto your branch as:

```
git pull [REMOTE-NAME] [REMOTE-BRANCH-NAME]
```

This allows a developer to pull directly from another developer if the changes 
are not yet in a form to be accepted into the canonical remote even though they 
are enough for the developer to accomplish their own task.

This sort of a pull should be noted in a pull request, as it can create 
confusion is a partially complete feature pulled from one remote is part of a 
pull request from another remote.

### Hiding files from Git

You can inform Git that you do not wish it to consider a file/directory by
adding it to the `.gitignore` file at the base of your repository. 

GitHub provides a good guide that goes deeper on this issue:
https://help.github.com/articles/ignoring-files

## Other Commands

### Delete a branch


First, remove the branch locally

````
git branch -D [BRANCH-NAME]
````

Then, push the deleted branch.

````
git push origin [BRANCH-NAME] 
````

The colon denotes to delete it, rather than push it.
