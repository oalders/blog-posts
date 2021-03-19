# Context Switching in Git

Those of us who spend a lot of time working with `git` will eventually need to do some form of context switching. Sometimes this adds very little overhead to our workflows, but at other times it can be a real pain. Let's discuss the pros and cons of some common strategies for dealing with context switching.

## The Problem:

Imagine you are working in branch called `feature-X`. You have just discovered that you need to solve an unrelated problem. This cannot be done in `feature-X`. You will need to do this work in a new branch: `feature-Y`.

## Solution #1 (stash + branch)

Probably the most common workflow to tackle this issue looks something like this:

* *Halt work on branch feature-X*
* `git stash`
* `git checkout -b feature-Y origin/main`
* *hack hack hack...*
* `git checkout feature-X` or `git switch -`
* `git stash pop`
* *Resume work on feature-X*

### Pros:

The nice thing about this approach is that this is a fairly easy workflow for simple changes. It can work quite well, especially for small repositories.

### Cons:

When using this workflow you can only have one workspace at any given time. Also, depending on the state of your repository, working with the stash can be non-trivial. 

## Solution #2 (WIP commit + branch)

A variation on the first solution looks quite similar, but it uses a WIP (Work in Progress) commit rather than the stash. When you're ready to switch back, rather than popping the stash, `git reset HEAD~1` unrolls your WIP commit and you're free to continue on much as you did in the earlier scenario, without having touched the stash. 

* *Halt work on branch feature-X*
* `git add -u` (adds only modified and deleted files)
* `git commit -m "WIP"`
* `git checkout -b feature-Y origin/master`
* *hack hack hack...*
* `git checkout feature-X` or `git switch -`
* `git reset HEAD~1`


### Pros:

This is still an easy workflow for simple changes and also good for small repositories. You don't have to work with the stash.

### Cons:

You can only have one workspace at any given time. Also, WIP commits can sneak into your final product if you're or your code reviewer are not vigilant. 

I should note that when using this workflow you *never* want to add a `--hard` to `git reset`. If you do this accidentally, you should be able to restore your commit using `git reflog`, but it's less heartstopping to avoid this scenario entirely.


## Solution #3 (new repository clone)

Rather than creating a new branch, you make a new clone of the repository for each new feature branch.

### Pros:

Now you can work in multiple workspaces simultaneously. You don't need `git stash` or even WIP commits.

### Cons:

Depending on the size of your repository, this can use a lot of disk space. (Shallow clones can help with this scenario, but they may not always be a good fit.) Additionally, your repository clones will be agnostic about each other. Since they can't track each other, it's now up to you to track where our clones live. If you need to set up `git` hooks, that will need to be done for each new clone.


## Solution #4 (git worktree)

In order to use this solution, you may need to learn about `git add worktree`. Don't feel badly if you're not familiar with worktrees in Git. Many of us get by for years in blissful ignorance of this concept.

### What is a worktree?

Let's think of the worktree as the files in the repository which belong to your project. Essentially it's a kind of workspace. You may not realize that you're already using worktrees. When using Git, you get your first worktree for free.

```sh
$ mkdir /tmp/foo && cd /tmp/foo
$ git init
$ git worktree list
/tmp  0000000 [master]
```

As you can see, the worktree exists even before the first commit. Now, let's add a new worktree to an existing project.

### Adding a worktree

```sh
$ git clone https://github.com/oalders/http-browserdetect.git
$ cd http-browserdetect/
$ git worktree list
/Users/olaf/http-browserdetect  90772ae [master]

$ git worktree add ~/trees/oalders/feature-X -b oalders/feature-X origin/master
$ git worktree add ~/trees/oalders/feature-Y -b oalders/feature-Y e9df3c555e96b3f1

$ git worktree list
/Users/olaf/http-browserdetect       90772ae [master]
/Users/olaf/trees/oalders/feature-X  90772ae [oalders/feature-X]
/Users/olaf/trees/oalders/feature-Y  e9df3c5 [oalders/feature-Y]
```

We can see from above that to add a new worktree we need to provide:

1. a location on disk
1. a branch name
1. something to branch from

Like most other Git commands, we need to be inside a repository when issuing this command. Once the worktrees are created, we have isolated work environments. The Git repository tracks where these worktrees live on disk. If Git hooks have already been set up in the parent repository, they will also be available to us in the worktrees.

Let's not overlook the fact that each worktree only uses a fraction of the disk space used by the parent repository. In this case the worktree requires about one third of the disk space of the original. This can scale very well. Once your repositories are measured in the Gigabytes, you'll really come to appreciate these savings.


```sh
$ du -sh /Users/olaf/http-browserdetect
2.9M

$ du -sh /Users/olaf/trees/oalders/feature-X
1.0M
```

### Pros:

You can now work in multiple workspaces simultaneously. You don't need the stash. `git` tracks all of your worktrees. You don't need to set up Git hooks. This is also faster than `git clone` and can also save on network traffic, since you can do this in airplane mode. You also have more efficient use of disk space without needing to resort to a shallow clone.

### Cons:

This is yet another thing to remember. However, if you can get into the habit of using this feature it can reward you handsomely.

## A few more tips

When you need to clean up our worktrees, you have a couple of options. The preferable way is to let Git remove the worktree:

`git worktree remove /Users/olaf/trees/oalders/feature-X`

If you prefer a scorched earth approach, `rm -rf` is also your friend.

`rm -rf /Users/olaf/trees/oalders/feature-X`

However, if you do this, you may want to clean up remaining files via `git worktree prune`. Or you skip the `prune` now and this will happen on its own at some point in the future via `git gc`.

## Notable notes

If you're ready to get started with `git worktree`, here are a few things to keep in mind.

* Removing a worktree does not delete the branch
* You can switch branches within a worktree
* You cannot check out the same branch in multiple worktrees
* Like many other `git` commands, `git worktree` commands need to be run from inside a repository
* You can have many worktrees at once
* Create your worktrees from the same local checkout, or they will be agnostic about each other


## git rev-parse

One final note is that when using `git worktree`, your concept of where the root of the repository lives may depend on context. Fortunately, `git rev-parse` allows us to distinguish between the two.

* To find the root of the parent repository
  * `git rev-parse --git-common-dir`

* To find the root of the repository we're in
  * `git rev-parse --show-toplevel`

As in many things, TIMTOWDI (There's More Than One Way To Do It). What's important is that you find a workflow that suits your needs. What your needs are may vary depending on the problem at hand. Maybe now you'll occasionally find yourself reaching for `git worktree` as a handy tool in your revision control tool belt.