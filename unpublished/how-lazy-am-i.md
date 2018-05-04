Occasionally I find myself running some random Perl script or dealing with some code from a colleague that doesn't have proper dependency management (yet).  It's a bit painful to

* run the script
* wait for it to die on a failed dependency
* install the missing dependency
* re-run the script
* wait for it to die
* install the missing dependency
* rinse
* lather
* repeat

I was aware of [Acme::Magic::Pony](https://metacpan.org/pod/Acme::Magic::Pony).  It already solves this problem, but it uses [cpan](https://metacpan.org/pod/cpan) for the installation.  I've lately been using [App::cpm](https://metacpan.org/pod/App::cpm), so I wanted a solution that would also use [App::cpm](https://metacpan.org/pod/App::cpm).  I came up with something which I've been using for my own needs.  I'm now ready for the general public to tell me how bad of an idea it really is.  Drum roll please...

I give you [lazy](https://metacpan.org/pod/lazy).

[lazy](https://metacpan.org/pod/lazy) is really easy to use.  Let's say you have a script called `foo.pl` which may (or may not) include dependencies which you have not yet installed.  You can now invoke it this way:

`perl -Mlazy foo.pl`

This will attempt to install any missing dependencies in a place where they are globally available to you.  If all goes well you'll be installing dependencies and running your code all in one invocation of your script.

If you'd like to keep the new dependencies in a sandbox, you can do this:

`perl -Mlocal::lib=my_sandbox -Mlazy foo.pl`

This will install the missing modules to the `my_sandbox` folder and it will also use this folder to find them.   But wait, there's more!

If you're too lazy to specify a local directory, but you still want to use a local lib, [lazy](https://metacpan.org/pod/lazy) will use whatever your first default [local::lib](https://metacpan.org/pod/local::lib) install location is:

`perl -Mlocal::lib -Mlazy`

And there's still more!

If you want to pass some extra flags to `cpm` you can do this by passing them to `lazy`:

`perl -Mlazy=-v foo.pl`

This switches on `cpm`'s verbose reporting.  Now, if you also want to remove the colour from this reporting, that's easy enough:

`perl -Mlazy=-v,--no-color foo.pl`

Maybe you want to install man pages too:

`perl -Mlazy=-v,--no-color,--man-pages foo.pl`

Check `cpm --help` for a more exhaustive list of available options.

Anything you can do via command line switches, you can also do directly in your code.

```
use local::lib qw( my_sandbox );
use lazy qw( -v --no-color );
```

You get the idea.  I'd be tickled if you decided to give `lazy` a spin and then headed over to [Github](https://github.com/oalders/lazy) to tell me all the things that it doesn't do correctly.  :)

