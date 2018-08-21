Occasionally I find myself running some random Perl script from a Github gist or dealing with some code from a colleague that doesn't have proper dependency management (yet).  It's a bit painful to

* run the script
* wait for it to die on a failed dependency
* install the missing dependency
* re-run the script
* wait for it to die
* install the missing dependency
* rinse
* lather
* repeat

## Being lazy

I was aware of [Acme::Magic::Pony](https://metacpan.org/pod/Acme::Magic::Pony).  It already solves this problem, but it uses [cpan](https://metacpan.org/pod/cpan) for the installation.  [lib::xi](https://metacpan.org/pod/lib::xi) also solves this problem, but does this via [cpanm](https://metacpan.org/pod/cpanm).  (I was only made aware of [lib::xi](https://metacpan.org/pod/lib::xi) later).  I've lately been using [App::cpm](https://metacpan.org/pod/App::cpm) to install modules, so I wanted a solution that would also use [App::cpm](https://metacpan.org/pod/App::cpm).  I came up with something which I've been using for my own needs.  I'm now ready for the general public to tell me how bad of an idea it really is.  Drum roll please...

I give you [lazy](https://metacpan.org/pod/lazy).

Let's take an example script, called `foo.pl`:

```perl
use strict;
use warnings;
use say;

use Open::This qw( to_editor_args );

say join ' ', to_editor_args('LWP::UserAgent::new()');
```

If you are missing an installed module, your output might look something like:

```bash
$ perl foo.pl
Can't locate Open/This.pm in @INC (you may need to install the Open::This module) (@INC contains: /Users/olafalders/.plenv/versions/5.26.1/lib/perl5/site_perl/5.26.1/darwin-2level /Users/olafalders/.plenv/versions/5.26.1/lib/perl5/site_perl/5.26.1 /Users/olafalders/.plenv/versions/5.26.1/lib/perl5/5.26.1/darwin-2level /Users/olafalders/.plenv/versions/5.26.1/lib/perl5/5.26.1) at foo.pl line 3.
BEGIN failed--compilation aborted at foo.pl line 3.
```

Let's try re-invoking this script, but this time with [lazy](https://metacpan.org/pod/lazy):


`perl -Mlazy foo.pl`

This will attempt to install any missing dependencies in a place where they are globally available to you.  If all goes well you'll be installing dependencies and running your code all in one invocation of your script.  Does it work?  Let's test it.


```bash
$ perl -Mlazy foo.pl
DONE install Open-This-0.000009 (using prebuilt)
1 distribution installed.
+20 /Users/olafalders/.plenv/versions/5.26.1/lib/perl5/site_perl/5.26.1/LWP/UserAgent.pm
```

It *did* work and it was pretty painless.  `lazy` noticed that a module ([Open::This](https://metacpan.org/pod/Open::This)) was missing, so it installed it, loaded it and then allowed the script successfully to run to completion.

(Note that the above script is giving you the arguments which you could pass to an editor, like `vim` to open up [LWP::UserAgent](https://metacpan.org/pod/LWP::UserAgent) at the line where `sub new` is defined.  I hope to get to [Open::This](https://metacpan.org/pod/Open::This) in a subsequent blog post.)

That's enough information to make you dangerous.  At this point you can stop reading and get on with your life.  If you'd like to see some more advanced use cases, read on.

## Using an arbitrary local::lib

If you'd like to keep the new dependencies in a sandbox, you can do this:

`perl -Mlocal::lib=my_sandbox -Mlazy foo.pl`

This will install the missing modules to the `my_sandbox` folder and it will also use this folder to find them.   Your install might look something like:

```bash
$ perl -Mlocal::lib=my_sandbox -Mlazy=-v foo.pl

6615 DONE resolve   (0.155sec) Open::This -> Open-This-0.000009 (from MetaDB)
6615 DONE fetch     (0.040sec) Open-This-0.000009 (using prebuilt)
6615 DONE resolve   (0.028sec) Try::Tiny -> Try-Tiny-0.30 (from MetaDB)
6615 DONE fetch     (0.087sec) Try-Tiny-0.30 (using prebuilt)
6617 DONE resolve   (0.142sec) Path::Tiny -> Path-Tiny-0.108 (from MetaDB)
6616 DONE resolve   (0.143sec) Module::Runtime -> Module-Runtime-0.016 (from MetaDB)
6616 DONE fetch     (0.029sec) Module-Runtime-0.016 (using prebuilt)
6615 DONE install   (0.053sec) Try-Tiny-0.30 (using prebuilt)
6617 DONE fetch     (0.043sec) Path-Tiny-0.108 (using prebuilt)
6616 DONE resolve   (0.029sec) Module::Build -> Module-Build-0.4224 (from MetaDB)
6616 DONE fetch     (0.040sec) Module-Build-0.4224 (using prebuilt)
6615 DONE install   (0.054sec) Path-Tiny-0.108 (using prebuilt)
6616 DONE install   (0.250sec) Module-Build-0.4224 (using prebuilt)
6615 DONE install   (0.033sec) Module-Runtime-0.016 (using prebuilt)
6615 DONE install   (0.070sec) Open-This-0.000009 (using prebuilt)
5 distributions installed.

+20 /Users/olafalders/.plenv/versions/5.26.1/lib/perl5/site_perl/5.26.1/LWP/UserAgent.pm

$ tree my_sandbox/
my_sandbox/
├── bin
│   ├── config_data
│   └── ot
└── lib
    └── perl5
        ├── 5.26.1
        │   └── darwin-2level
        ├── Module
        │   ├── Build
        │   │   ├── API.pod
        │   │   ├── Authoring.pod
        │   │   ├── Base.pm
        │   │   ├── Bundling.pod
        │   │   ├── Compat.pm
        │   │   ├── Config.pm
        │   │   ├── ConfigData.pm
        │   │   ├── Cookbook.pm
        │   │   ├── Dumper.pm
        │   │   ├── Notes.pm
        │   │   ├── PPMMaker.pm
        │   │   ├── Platform
        │   │   │   ├── Default.pm
        │   │   │   ├── MacOS.pm
        │   │   │   ├── Unix.pm
        │   │   │   ├── VMS.pm
        │   │   │   ├── VOS.pm
        │   │   │   ├── Windows.pm
        │   │   │   ├── aix.pm
        │   │   │   ├── cygwin.pm
        │   │   │   ├── darwin.pm
        │   │   │   └── os2.pm
        │   │   └── PodParser.pm
        │   ├── Build.pm
        │   └── Runtime.pm
        ├── Open
        │   └── This.pm
        ├── Path
        │   └── Tiny.pm
        ├── Try
        │   └── Tiny.pm
        └── darwin-2level
            └── auto
                ├── Module
                │   ├── Build
                │   └── Runtime
                ├── Open
                │   └── This
                ├── Path
                │   └── Tiny
                └── Try
                    └── Tiny

22 directories, 29 files

6 directories, 0 files
```

But wait, there's more!

## Using a default local::lib

If you're too lazy to specify a local directory, but you still want to use a local lib, [lazy](https://metacpan.org/pod/lazy) will use whatever your first default [local::lib](https://metacpan.org/pod/local::lib) install location is:

`perl -Mlocal::lib -Mlazy foo.pl`

In my case it looks like:

```bash
$ tree ~/perl5/lib/perl5
/Users/olafalders/perl5/lib/perl5
├── 5.26.1
│   └── darwin-2level
├── Module
│   ├── Build
│   │   ├── API.pod
│   │   ├── Authoring.pod
│   │   ├── Base.pm
│   │   ├── Bundling.pod
│   │   ├── Compat.pm
│   │   ├── Config.pm
│   │   ├── ConfigData.pm
│   │   ├── Cookbook.pm
│   │   ├── Dumper.pm
│   │   ├── Notes.pm
│   │   ├── PPMMaker.pm
│   │   ├── Platform
│   │   │   ├── Default.pm
│   │   │   ├── MacOS.pm
│   │   │   ├── Unix.pm
│   │   │   ├── VMS.pm
│   │   │   ├── VOS.pm
│   │   │   ├── Windows.pm
│   │   │   ├── aix.pm
│   │   │   ├── cygwin.pm
│   │   │   ├── darwin.pm
│   │   │   └── os2.pm
│   │   └── PodParser.pm
│   ├── Build.pm
│   └── Runtime.pm
├── Open
│   └── This.pm
├── Path
│   └── Tiny.pm
├── Try
│   └── Tiny.pm
└── darwin-2level
    └── auto
        ├── Module
        │   ├── Build
        │   └── Runtime
        ├── Open
        │   └── This
        ├── Path
        │   └── Tiny
        └── Try
            └── Tiny

19 directories, 27 files
```

But wait, there's more!

## Passing args to cpm

If you want to pass some extra flags to `cpm` you can do this by passing them to `lazy`:

`perl -Mlazy=-v foo.pl`

This switches on `cpm`'s verbose reporting.  The first part of the output now looks like:

```bash
$ perl -Mlazy=-v foo.pl
10476 DONE resolve   (0.159sec) Open::This -> Open-This-0.000009 (from MetaDB)
10476 DONE fetch     (0.041sec) Open-This-0.000009 (using prebuilt)
10476 DONE install   (0.053sec) Open-This-0.000009 (using prebuilt)
1 distribution installed.
+20 /Users/olafalders/.plenv/versions/5.26.1/lib/perl5/site_perl/5.26.1/LWP/UserAgent.pm
```

Now, if you also want to remove the colour from this reporting, that's easy enough:

`perl -Mlazy=-v,--no-color foo.pl`

Maybe you want to install man pages too:

`perl -Mlazy=-v,--no-color,--man-pages foo.pl`

Check `cpm --help` for a more exhaustive list of available options.

Anything you can do via command line switches, you can also do directly in your code.

```
use local::lib qw( my_sandbox );
use lazy qw( -v --no-color );
```

## In Your Editor

This whole thing got me thinking about a `vim` integration.  In my case I've added the following to my `.vimrc`:

`nnoremap <leader>l :!perl -Mlazy -c %:p`

This essentially means that in my editor, if I type `,l` then `vim` helpfully takes the name of the file I'm currently working on (`Foo.pm`) and runs `perl -Mlazy -c Foo.pm`. Since `lazy` will still be invoked when run via `-c` we don't even have to run code to install modules.  We can essentially just run a compile check and this sets the module installation chain of events into motion.  I can install missing modules without leaving my editor.

Now, this is where the pedants among you speak up and say "but -c *does* run code in `BEGIN` and `CHECK` blocks".  See more discussion at [https://stackoverflow.com/a/12908487/406224](https://stackoverflow.com/a/12908487/406224).  Essentially code *could* be run under `-c` and it's helpful to be aware of this, for security reasons.

## In Your Linter

As an [https://github.com/w0rp/ale](Ale) + `vim` user, I've been using [SKAJI's](https://metacpan.org/author/SKAJI) [syntax-check-perl](https://github.com/skaji/syntax-check-perl/) plugin for [https://github.com/w0rp/ale](Ale).  Since this linter runs your code via `-c`, adding a `use lazy;` to your code will install your CPAN modules on demand, every time your linter runs.  So, it's possible for your modules to be installed asynchronously as you happily go about writing your code.  Just be sure to add a `use lazy;` to the code you are writing.  This statement must be *before* any modules which you would like to be auto-installed.

## Wrapping Up

You get the idea.  I'd be tickled if you decided to give `lazy` a spin and then headed over to [Github](https://github.com/oalders/lazy) to tell me all the things that it doesn't do correctly.  :)
