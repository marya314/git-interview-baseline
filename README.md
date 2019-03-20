# Git Collaboration

## Overview

This lesson will show you how to use git in a collaborative setting. We'll cover the most common git commands and the situations in which you will use them.

## Objectives

1. Create a new branch for your repository with `git branch` or `git checkout -b`.
2. Checkout branches with `git checkout`.
3. Make commits within a branch.
4. Merge local branches with `git merge`.
5. Update branches from remotes with `git fetch`.
6. Merge updated remote branches with `git merge`.
7. Update and merge remote branches with `git pull`.

## Why Collaborate Using Git?

A key to collaborating with git is to keep discrete and individual lines of work isolated from each other. Consider the following scenario:

You start work on a big feature, make a few commits, but don't entirely finish the feature. Your git log might look like:

```
512bec5 Still broken, working on new-feature (aviflombaum, 2 hours ago)
62d840 Almost done with new-feature (aviflombaum, 1 day ago)
fbee832 Started new-feature (aviflombaum, 2 days ago)
```

Our feature, in its current state, is not production-ready. If we deployed the latest version of our code, our users would see a half-finished, currently broken new-feature (no good).

But wait! We notice a big bug that is currently breaking the application for all users. The bug is an easy fix, and deploying one simple change can make everything work again. But you can't make that small commit on top of your current work, as you'd be pushing your unfinished new-feature as well as the bug fix. Here is what that log would look like:

```
r4212d1 Fix to application breaking bug (aviflombaum, just now)
512bec5 Still broken, working on new-feature (aviflombaum, 2 hours ago)
62d840 Almost done with new-feature (aviflombaum, 1 day ago)
fbee832 Started new-feature (aviflombaum, 2 days ago)
```

See, we can't push all those commits.

If we had isolated the new-feature into its own copy of our code, we could have deployed the bug fix separately, then went back to work on  new-feature until it's complete. We can do exactly this using a feature in git called branches.

## Making a branch with `git branch`

Let's first do some setup by making a repository to experiment with the collaborative features of git. You don't have to follow along, you'll be able to understand the concepts from the reading but if you'd like, you can copy and paste these commands locally.

From our home directory we're going to make a new directory for our mission-critical-application.

```
~ $ mkdir mission-critical-application
~ $ cd mission-critical-application
mission-critical-application $ git init
mission-critical-application $ touch application.rb
mission-critical-application $ git add application.rb
mission-critical-application $ git commit -m "First working version of application.rb"
```

1. We made a new directory with `mkdir mission-critical-application`.
2. We moved into that directory with `cd mission-critical-application`.
3. We turned that directory into a git repository with `git init`.
4. We created our application `touch application.rb`.
5. We programmed an entire working first version in `application.rb` (*not reflected in the CLI commands above, but we did, and it was awesome, great job*).
6. We added our `application.rb` to git with `git add application.rb`.
7. We committed the first working version of our application with `git commit -m "First working version of application.rb"`.
8. You deploy your application to production and people start using it (*also not reflected in the CLI commands above, but we did, and it too was awesome, great job*).

With our application online and customers rolling in, we notice a bug and quickly add a fix in the form of a file, `first-bug-fix.rb` (*this is just an example*).

```
mission-critical-application $ touch first-bug-fix.rb
mission-critical-application $ git add first-bug-fix.rb
mission-critical-application $ git commit -m "First bug fix"
```

Right now our git log could be visualized as a timeline composed of two commits.

![First Two Commits](https://dl.dropboxusercontent.com/s/ikorf1qvvp4tay0/2015-11-02%20at%2011.15%20AM.png)

### About `master` branch

Notice that these commits are occurring in a linear sequence of events, almost like a timeline? We call this timeline a branch. Whenever you are working on commits in git, you are adding them on a timeline of code called a branch. The branch you are on by default at the start of any repository, your main timeline (the main branch) is called master.

![Master Branch](https://dl.dropboxusercontent.com/s/v75as2cf6xr8n8a/2015-11-02%20at%2011.17%20AM.png)

`git status` will always tell you what branch you are on.

```
mission-critical-application $ git status
On branch master
nothing to commit, working directory clean
```

The `master` git branch is our default branch.

**Protip: A best practice when using git is to make sure that the `master` branch is always clean with working code. We don't put broken code in master so that we can always deploy master.**

### Starting a new feature with `git branch new-feature`

To keep master clean, we'll start a new feature in an isolated feature branch. Our timeline will look as follows:

![Feature Branch](https://dl.dropboxusercontent.com/s/d61r0fxyriaf5oj/2015-11-02%20at%2011.52%20AM.png)

After commit 2, we will branch out of master and create a new timeline for commits and events specifically related to the new feature. The master timeline remains unchanged and clean.

To make a new branch simply type: `git branch <branch name>`. In the case of a branch relating to a new feature, we'd name the branch `new-feature` like so:

```
mission-critical-application $ git branch new-feature
```

To see a list of our branches we can type: `git branch -a`

```
mission-critical-application $ git branch -a
* master
  new-feature
```

The `*` in front of the branch `master` indicates that `master` is currently our working branch and that we also have a branch called `new-feature`. Note that we only created the new branch, but did not switch to it. If we made a commit right now, that commit would still be applied to our `master` branch.

## Switching branches with `git checkout`

We need to checkout or switch into our `new-feature` branch so that git knows that all commits we will be making apply to only that branch. We can move between branches with `git checkout <branch name>`.

```
mission-critical-application $ git status
On branch master
nothing to commit, working directory clean
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
mission-critical-application $ git status
On branch new-feature
nothing to commit, working directory clean
```

We started on `master` and then checked out our `new-feature` branch with `git checkout new-feature`, thereby moving into that timeline.

**Protip: You can create and checkout a new branch in one command using: `git checkout -b new-branch-name`. That will both create the branch `new-branch-name` and move into it by checking it out.**

Let's make a commit in this `new-feature` and get the feature started by making a new file, `new-feature-file` to represent the code for the new feature.

```
mission-critical-application $ touch new-feature-file
mission-critical-application $ git add new-feature-file
mission-critical-application $ git commit -m "Started new feature"
[new-feature 332a618] Started new feature
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature-file
```

You can see the commit we made was made in the context of the `new-feature` branch.

Right as we got started on that feature though, we get another bug report and have to move back into master to fix the bug and then deploy master. How do we move from `new-feature` branch back to `master`? What will our code look like when we move back to `master`, will we see the remnants of the `new-feature` branch and code represented by the `new-feature-file`?

### Moving back to `master` with `git checkout master`

You can always move between branches with `git checkout`. Since we are currently on `new-feature`, we can move back to master with `git checkout master`.

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

And we could move back to `new-feature` with `git checkout new-feature`

```
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
```

And back again with:

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

![Switching between branches](https://dl.dropboxusercontent.com/s/qzajqsd9f6njauc/2015-11-02%20at%2012.12%20PM.png)

### Working in Two Branches

From master, one thing you'll notice is that the code you wrote on `new-feature`, namely the file, `new-feature-file`, is not present in the current directory.

```
mission-critical-application $ ls
application.rb first-bug-fix.rb
```

The master branch *only has the code from the most recent commit relative to the master timeline or branch*. The code from our `new-feature` is tucked away in that branch, waiting patiently in isolation from the rest of our code in `master`.

Once you're on master you are free to make a commit to fix the bug, which we'll represent with a new file, `second-bug-fix.rb`.

```
mission-critical-application $ touch second-bug-fix.rb
mission-critical-application $ git add second-bug-fix.rb
mission-critical-application $ git commit -m "Second bug fix"
```

Let's look at our timeline now.

![Commit on Master](https://dl.dropboxusercontent.com/s/9ipgkog7yv8hrok/2015-11-02%20at%2012.18%20PM.png)

We were able to update the master branch with the bug fix without touching any of the code in new-feature. The `new-feature` branch is now 1 commit behind master because `new-feature`  was created only with the commits at the moment when the branch was created. You could describe `master` as being 1 commit ahead of the `new-feature` branch.

Let's go back into `new-feature`, complete the feature and commit it, then look at the timeline. Remember how to move from `master` back to `new-feature`?

```
mission-critical-application $ git status
On branch master
nothing to commit, working directory clean
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
```

Let's rename `new-feature-file` to `new-feature` to signify the code we wrote to complete the feature and commit this change. We can rename a file with `mv <original filename> <new filename>` BASH command.

```
mission-critical-application $ mv new-feature-file new-feature
mission-critical-application $ git add new-feature
mission-critical-application $ git commit -m "Finished feature"
[new-feature bfe50fc] Finished feature
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature
```

Let's look at our timeline now.

![Completed Feature Branch](https://dl.dropboxusercontent.com/s/xtoehu7tv5zim6v/2015-11-02%20at%2012.31%20PM.png)

The final step of our `new-feature` work sprint is to figure out how to merge that timeline into the master timeline.

## Merging branches with `git merge`

We now have two separate branches with their own work and commits. Our goal is to bring the timeline of commits that occurred on the `new-feature` branch into  `master` so that at the end of the operation, our `master` timeline looks like:

![Merged Timeline](https://dl.dropboxusercontent.com/s/bf0cktf3ag549z2/2015-11-02%20at%201.15%20PM.png)

Think of it as putting history back together again.

By merging the branches, `master` will inherit all of the commits from the `new-feature` branch as though those events occurred on the `master` timeline.

When we merge a branch with `git merge`, it's important to be currently working on your target branch, or the branch you want to move into. The first step for our `new-feature` merge is to checkout `master` because that is where we want the commits to end up.

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

Once on your target branch, type: `git merge <branch name>` where `<branch name>` is the branch we want to merge. In this case, it is `git merge new-feature`.

```
mission-critical-application $ git merge new-feature
Updating e5830af..bfe50fc
Fast-forward
 new-feature      | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature
```

Now the branches have been merged and if you `ls`, you'll see the `new-feature` file from the `new-feature` branch in your current working directory that is checked out to master. Note that this merged two local branches on our machine. To learn how to work with remote branches, let's continue on.

## Working with remote branches with `git fetch`, `git merge`, and `git pull`

Your local branches can attach to and "track" remote branches that live on the internet, generally on GitHub, that your team members might contribute to and you can download locally. `git fetch`, `git merge`, and `git pull` are all ways to view and incorporate changes from remote branches.

**Protip: 99% of the time, you will be using `git pull`, which is a combination of `git fetch` and `git merge`. However, it's important to understand what each of these commands are doing, and how `git pull` works.**

### Local and Remote Branches in git

An important concept when collaborating in git are local and remote branches. Local branches live only on your machine - they have not been pushed up to a remote branch and only you can see them.

Remotes are branches that exist in a remote location (usually GitHub). This allows other developers to access the branch, pull down the latest commits, and add their own work and commits.

When you make a new branch using `git checkout -b new-feature`, as we've done previously, it creates a *local* branch on your machine called new-feature. You can then push the newly created new-feature branch to the remote repository (called origin here) using:

````
git push -u origin new-feature
````

Now the new branch is available in the remote repository.

### Using `git fetch`

Perhaps your coworkers or other developers have contributed to the remote repository. You want to bring your copy of the remote repository up to date, but you don't want to integrate anything just yet. Perhaps the new commits may break the feature you are building.

In this case, you can use `git fetch`.

```
mission-critical-application $ git fetch
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 3), reused 3 (delta 2), pack-reused 0
Unpacking objects: 100% (4/4), done.
From github.com:aviflombaum/mission-critical-application
   bfe50fc..0ae1da2  master     -> origin/master
 * [new branch]      remote-feature-branch -> origin/remote-feature-branch
```

The last lines of output are really important, let's take a closer look:

```
From github.com:aviflombaum/mission-critical-application
   bfe50fc..0ae1da2  master     -> origin/master
 * [new branch]      remote-feature-branch -> origin/remote-feature-branch
```

The first line, `From github.com:aviflombaum/mission-critical-application` is informing us which remote our `git fetch` updated from, namely, the remote repository located at: https://github.com/aviflombaum/mission-critical-application

When we `fetch` with git, we are asking to copy all changes on the remote to our local git repository, but not actually integrate any. The next line, `bfe50fc..0ae1da2  master     -> origin/master` is telling us that a new commit was found in `origin/master`. `origin/master` means the GitHub version of `master`. Even though git fetched a new commit from `origin/master`, it did not merge it into the local master.

### Using `git merge`

Our remote copy on GitHub has a file, `remote-bug-fix`, presumably some code that another developer pushed up to our remote version of the `master` branch to fix a bug. We now want to integrate that code into our local copy.

From within your local master branch, type: `git merge origin/master`, referring to the branch's full path, `remote/branch`, or `origin/master`.

```
mission-critical-application $ git merge origin/master
Updating bfe50fc..0ae1da2
Fast-forward
 remote-bug-fix | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 remote-bug-fix
mission-critical-application $ ls
 application.rb		new-feature-file    new-feature		
 remote-bug-fix
```

The commits fetched via `git fetch` are now merged from the `origin/master` branch into our local `master` branch. And now `ls` reveals that the file present on the remote, `remote-bug-fix` is integrated into our local copy of `master` as well.

`git fetch` is a pretty low-level git command we don't use that much because it always requires two steps, first `git fetch` and then `git merge` to actually integrate those changes into your working branch. Generally, if you are in `master` you want to immediately `fetch` and `merge` any changes to the remote master.

### Combining `git fetch` with `git merge` by using `git pull`

`git pull` is literally the combination of both `git fetch` and `git merge`.

When you `git pull` the following things will occur:

1. You will `git fetch` all remote changes, including those on the current branch, existing branches, and new branches.
2. Any changes that are on a remote branch which is being tracked by your local branch, that is to say, if you are on `master` and there is a change to `origin/master`, those changes will be automatically merged.

## Conclusion

Git is complex, and collaborating with people in this matter is just hard - there's no easy way to allow 100s of people to all work on the same code base. These workflows are just being introduced to you.  You'll have lots of time to practice them and memorize what each command does.

![XKCD Git](http://imgs.xkcd.com/comics/git.png)

<a href='https://learn.co/lessons/git-collaboration-readme' data-visibility='hidden'>View this lesson on Learn.co</a>

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/git-collaboration-readme'>Git Collaboration</a> on Learn.co and start learning to code for free.</p>

<p class='util--hide'>View <a href='https://learn.co/lessons/git-collaboration-readme'>Git Collaboration</a> on Learn.co and start learning to code for free.</p>
