## Motivation

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

## Prior Art

I was aware of [Acme::Magic::Pony](https://metacpan.org/pod/Acme::Magic::Pony).  It already solves this problem, but it uses [cpan](https://metacpan.org/pod/cpan) for the installation.  [lib::xi](https://metacpan.org/pod/lib::xi) also solves this problem, but does this via `cpanm`.  (I was only made aware of `lib::xi` later).  I've lately been using [App::cpm](https://metacpan.org/pod/App::cpm) to install modules, so I wanted a solution that would also use [App::cpm](https://metacpan.org/pod/App::cpm).  I came up with something which I've been using for my own needs.  I'm now ready for the general public to tell me how bad of an idea it really is.  Drum roll please...

I give you [lazy](https://metacpan.org/pod/lazy).

[lazy](https://metacpan.org/pod/lazy) is really easy to use.  Let's say you have a script called `foo.pl` which may (or may not) include dependencies which you have not yet installed.  You can now invoke it this way:

`perl -Mlazy foo.pl`

This will attempt to install any missing dependencies in a place where they are globally available to you.  If all goes well you'll be installing dependencies and running your code all in one invocation of your script.

For example, if `foo.pl` contains:

```perl
use strict;
use warnings;
use feature qw( say );

use Acme::Urinal;

my $urinals = Acme::Urinal->new( [ 0 .. 4 ] );

for ( 0 .. 4 ) {
    my ( $index, $resource, $comfort_level ) = $urinals->pick_one;
    say $index;
}
```

then your output should look something like:

```bash
$ perl -Mlazy foo.pl
DONE install Acme-Urinal-1.0 (using prebuilt)
1 distribution installed.
1
3
2
0
4
```

(Note that the above script is giving you hints on which urinal to pick upon entering a public restroom which has, you know, urinals. Your mileage may vary.)

If you'd like to keep the new dependencies in a sandbox, you can do this:

`perl -Mlocal::lib=my_sandbox -Mlazy foo.pl`

This will install the missing modules to the `my_sandbox` folder and it will also use this folder to find them.   Your install might look something like:

```bash
$ tree my_sandbox/
my_sandbox/
├── bin
└── lib
    └── perl5
        ├── 5.26.1
        │   └── darwin-2level
        ├── Acme
        │   └── Urinal.pm
        └── darwin-2level
            └── auto
                └── Acme
                    └── Urinal

10 directories, 1 file
```

But wait, there's more!

If you're too lazy to specify a local directory, but you still want to use a local lib, [lazy](https://metacpan.org/pod/lazy) will use whatever your first default [local::lib](https://metacpan.org/pod/local::lib) install location is:

`perl -Mlocal::lib -Mlazy`

In my case it looks like:

```bash
$ tree ~/perl5/lib/perl5
/Users/olafalders/perl5/lib/perl5
├── 5.26.1
│   └── darwin-2level
├── Acme
│   └── Urinal.pm
└── darwin-2level
    └── auto
        └── Acme
            └── Urinal

7 directories, 1 file
```

There's still more!

If you want to pass some extra flags to `cpm` you can do this by passing them to `lazy`:

`perl -Mlazy=-v foo.pl`

This switches on `cpm`'s verbose reporting.  The first part of the output now looks like:

```bash
$ perl -Mlazy=-v foo.pl
63797 DONE resolve   (0.122sec) Acme::Urinal -> Acme-Urinal-1.0 (from MetaDB)
63797 DONE fetch     (0.008sec) Acme-Urinal-1.0 (using prebuilt)
63797 DONE install   (0.042sec) Acme-Urinal-1.0 (using prebuilt)
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

This whole thing got me thinking about a `vim` integration.  In my case I've got:

```vim
" Try to install missing Perl modules
nnoremap <leader>l :!perl -Mlazy -c %:p
```

This essentially means that in my editor, if I type `,l` `vim` takes the name of the file I'm currently working on ("Foo.pm") and runs `perl -Mlazy -c Foo.pm`. Since `lazy` will still be invoked when run via `-c` we don't even have to run code to install modules.  We can essentially just run a compile check and this sets the module installation chain of events into motion.  I can install missing modules without leaving my editor.

Now, this is where the pedants among you speak up and say "but -c *does* run code in `BEGIN` and `CHECK` blocks.  This is correct!  See more discussion at [https://stackoverflow.com/a/12908487/406224](https://stackoverflow.com/a/12908487/406224).  Essentially code *could* be run under `-c` and it's helpful to be aware of this, for security reasons.

## Wrapping Up

You get the idea.  I'd be tickled if you decided to give `lazy` a spin and then headed over to [Github](https://github.com/oalders/lazy) to tell me all the things that it doesn't do correctly.  :)

