Recently I found a great little [Twitter command line tool](https://github.com/sferik/t) called `t`.  It does a lot of useful things, including building and editing Twitter lists.  For example, with the following commands we can:

* create a Twitter list called "my-list-of-people"
* add the [@metacpan](https://twitter.com/metacpan) account to the list
* display the accounts which are now in the list we've just created

```bash
t list create my-list-of-people
t list add my-list-of-people @metacpan
t list members my-list-of-people
```

I thought it would be fun to create a Twitter list of CPAN authors using some of the data in the MetaCPAN API.  Authors can [update their profiles on MetaCPAN](https://metacpan.org/account/profile) to add one or more Twitter accounts.  This data can then be found in the `author` type of the MetaCPAN API, in the `profile` key.  To find out how many Twitter usernames of CPAN authors we can get, we'll create a script that looks something like this:

```perl
#!/usr/bin/env perl

use strict;
use warnings;
use feature qw( say );

use MetaCPAN::Client;

my $mc = MetaCPAN::Client->new( version => 'v1' );

# Search for authors with a listed Twitter account
my $search = $mc->author( { 'profile.name' => 'twitter' } );

my @handles;
while ( my $author = $search->next ) {

    # grep matching author profiles to extract only the Twitter id
    my @profiles = grep { $_->{name} eq 'twitter' } @{ $author->profile };

    foreach my $profile (@profiles) {
        my $id = $profile->{id};

        # not every handle returned by the API is prefixed by "@", so
        # we'll add it when it's missing
        $id = '@' . $id unless $id =~ m{\A@};
        push @handles, $id;
    }
}

# print a case-insensitive alpha-sorted list for humans to enjoy
say $_ for sort { "\L$a" cmp "\L$b" } @handles;
```

At this point you should be able to run this script, which prints the Twitter account names we are looking for.  Now comes the Twitter integration.  We could do this with a Perl module, but the point of this exercise is to use `t`.

The first thing you need to do is install [t, the Twitter command line tool](https://github.com/sferik/t).  Twitter OAuth is a bit cumbersome, so you'll also need to create your own OAuth app.  The client will guide you through this process.  If you run into any problems getting started once you've entered your OAuth keys, check [issue #402](https://github.com/sferik/t/issues/402) and specifically [this comment](https://github.com/sferik/t/issues/402#issuecomment-466909077).

Now all that's left is to create a list, run the script and pipe the output of the script to the new list.

```bash
t list create cpan-authors
perl twitter.pl | xargs t list add cpan-authors
t list cpan-authors members
```

That's it.  Your list has now been created and you can curate it to your heart's content.  As part of this exercise, I created an authors list for [my own Twitter account](https://twitter.com/olafalders).  You can [see my cpan-authors list in action](https://twitter.com/olafalders/lists/cpan-authors).
