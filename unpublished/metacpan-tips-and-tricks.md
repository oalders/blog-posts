# PopClip

* https://github.com/metacpan/metacpan-popclip

# Alfred Workflows

* https://github.com/2shortplanks/alfred-metacpan
* https://github.com/handlename/alfred-metacpan-workflow
  * https://github.com/handlename/go-metacpan

# Keyboard Shortcuts

Type `shift` + `?` on most MetaCPAN pages.  The shortcuts provided will
generally work on [module pages](https://metacpan.org/pod/Open::This) and
[distribution pages](https://metacpan.org/release/Open-This)

* `gi` - takes you to a module/distribution's issue tracker
* `gr` - takes you to a module/distributions's repository
* `ga` - takes you to the author page of a module/distribution's latest releaser
* `gc` - takes you to a module/distributions changelog
* `gd` - takes you to the distribution page for a module
* `gs` - takes you to the source page of a module
* `gp` - takes you to a module/distribution's repository pull requests page
* `gb` - takes you to the file browser for a module/distribution
* `s` - brings the focus of the cursor the search bar

# Finding releases which do not appear in searches

Currently MetaCPAN only returns results for the last authorized and indexed
uploads of a module.  This makes other uploads, like `TRIAL` releases, hard to
find.  There are a couple of ways to find hard to find releases.  One way is to
go to the release page or module page for a distribution.  Clicking on "Jump to
version" may find the version you are looking for, if the person who uploaded
the module has permissions for it.

If, however, you're looking for an upload by someone who does not have release
permissions for a module, you won't find it here.  If you know the name of the
author you are looking for, go to that author's page.  In the sidebar you'll
find a "All releases by this author" link.  It'll look something like
(https://metacpan.org/author/OALDERS/releases).  Follow that link and you'll
find everything which this author has uploaded.  Chances are you'll be able to
find your hard to find module(s) via this search.

# Finding someone who upload a new version of a module

You've fixed a bug in a module.  Now you want to know who can patch the code
and upload a new release.  Your first instinct is to contact the last person to
release this module, but for various reasons, this may no longer be the best
person to contact about this.  How do find someone who can help you?

On the left hand menu for modules and distributions, click on the "Permissions"
link.  It will take you to a page like this:
(https://metacpan.org/permission/distribution/libwww-perl).  Note that for this
release, there are 4 different authors who can upload a new release.  If you
look closely, you'll actually see 5 authors listed, but only 4 of them have
permissions to upload *all* of the modules in this distribution.  So, by
contacting one of them, you'll have the best success.  (In this case the module
is still being maintained, you can actually open an issue on the relevant
Github repository rather than needing to concact any one person directly, but
you get the idea).
