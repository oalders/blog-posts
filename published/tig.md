## What is Tig?

If you work with `git` as your version control system, you've likely already resigned yourself to the fact that `git` is a complicated beast.  `git` is an amazing tool, but it can be cumbersome at times to navigate your `git` repositories.  That's where a tool like `tig` comes in.  From the `tig` man page:

> Tig is an ncurses-based text-mode interface for git(1). It functions mainly as a Git repository browser, but can also assist in staging changes for commit at chunk level and act as a pager for output from various Git commands.

What this basically means is that `tig` provides a text-based user interface which you can run in your terminal.  It makes it easy to browse your `git` logs, but it can do much more than just bounce you around from your last commit to a previous one.

*insert high level overview screenshot of UI here*

I'm going to give you a quick introduction to `tig` here.  Many of my examples have been poached directly from the excellent `tig` man page.  I highly recommend reading it for further information.

## Installation

* Fedora and RHEL: `sudo dnf install tig`
* Ubuntu and Debian: `sudo apt install tig`
* MacOS: `brew install tig`

See [the official installation instructions](https://jonas.github.io/tig/INSTALL.html) for even more options.

## Browse commits in your current branch

If you want to browse the latest commits in your branch, the command is simply:

`tig`

That's it.  This three character command will launch a browser which allows you to navigate the commits in your current branch.  You can think of it as a wrapper around `git log`.

To navigate the output, you can use the `up arrow` and `down arrow` keys to move from one commit to another.  Pressing `return` will open a vertical split with the contents of the chosen commit on the right hand side.  You can continue to browse up and down in your commit history on the left side, while your changes appear on the right.  Use `k` and `j` to navigate up and down by line on the right hand side.  Use `-` and `space bar` to page up and down on the right hand side. Use `q` to exit the right hand pane.

Searching on any `tig` output is simple as well.  Use `/` to search forward and `?` to search backward on both left and right panes.

*insert screenshot here*

That's enough to get you started navigating your commits.  There are far too many key bindings for me to cover here, but clicking `h` will display a help menu to help you discover all of your navigation and command options.  You can also use `\` and `?` to search the help menu. Use `q` to exit the help menu.

*insert screenshot here*
 
## Browse revisions for a single file

We saw above that `tig`, by default, is a wrapper around `git log`. Conveniently, `tig` accepts the same arguments which can be passed to `git log`.  For instance, to browse the commit history for a single file:

`tig README.md`

Compare this with the output of the `git` command which is being wrapped, to get a clearer view of how `tig` is enhancing the output.

`git log README.md`

To include the patches in the raw `git` output, you can add a `-p`:

`git log -p README.md`

If you want to narrow the commits down to a specific date range try something like this:

`tig --after="2017-01-01" --before="2018-05-16" -- README.md`

Again, you can compare this with the raw `git` version:

`git log --after="2017-01-01" --before="2018-05-16" -- README.md`


## Browse a git blame view of a file

`tig blame README.md`

This is essentially a wrapper around `git blame`.  As expected, it allows you to see who the last person was to edit a given line but it  also allows you to navigate to the commit which introduced the line.  This is somewhat like the `:Gblame` command which the `vim` plugin `vim-fugitive` provides.

## Browse your stash

If you're like me, you may have a pile of edits in your stash.  It's easy to lose track of them.  You can view the latest item in your stash via `git stash show -p stash@{0}`.  The second most recent item is found via `git stash show -p stash@{1}` and so on.  If can recall these commands whenever you need them you have a much sharper memory than I do. 

As with the `git` commands above, `tig` makes it easy to enhance your `git` output with a simple invocation: 

`tig stash`

Try issuing this command in a repository with a populated stash.  You'll be able to browse **and search** your stash items, giving you a quick overview of everything you saved for a rainy day.

## Browse your refs

*Add quick description of what refs in a git repo actually are*

`tig refs`

Browse all of your refs and drill down to specific commits. Use `q` to return to a previous menu.

*maybe include screenshot*

## Browse git-status

`tig status` is a wrapper around `git status`.

*include screenshot*

## Browse git-grep

`tig grep` allows you to navigate the output of `git grep`.  For example `tig grep -i foo lib/Bar` will navigate the output of a case-insensitive search for "foo" in the "lib/Bar" directory.

*maybe include screenshot*

## Tips

### Pipe output to tig via STDIN

If you are piping a list of commit IDs to tig, you'll need to use the `--stdin` flag in order to get readable output.

`git rev-list --author=olaf HEAD | tig show --stdin`

## Adding Custom Bindings

It's possible to customize `tig` with an rc file.  To do this, create a file in your home directory called `.tigrc`.  To demonstrate how to configure `tig` to your liking, we're going to add some helpful, custom key bindings.

Open `~/.tigrc` in your favourite editor and add:

```
# Apply the selected stash
bind stash a !?git stash apply %(stash)

# Drop the selected stash item
bind stash x !?git stash drop %(stash)
```

With the following bindings in place, now run `tig stash`.  You'll be able to browse your stash, just as noted above.  However, now you can press `a` to apply an item from the stash to your repository and `x` to drop an item from the stash.  Keep in mind that you'll need to perform these commands when browsing the stash list itself.  If you're browsing a stash item, enter `q` to exit that view and then press `a` or `x` to get the desired effect.

You can read more about `tig` key bindings at [https://github.com/jonas/tig/wiki/Bindings](https://github.com/jonas/tig/wiki/Bindings).

## Wrapping Up

I hope this has been a helpful demonstration of how you might use `tig` to enhance your daily workflow.  There are even more powerful things which `tig` can do (such as staging lines of code), but that's outside the scope of this introductory tutorial.  There's enough information here to make you dangerous, but there's still more to explore.

## TODO
* include screen shots of at least some of the functionality
* possibly create an accompanying docker image which includes
  * a git repository to run commands on
  * some staged and unstaged content to run commands on (or a script which can create this content on demand
  * a populated stash to run commands on

