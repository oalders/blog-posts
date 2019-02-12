# About the Various PANs

When it comes to **CPAN**, Perl has a lot of related acronyms, many of which can be hard to understand.  Let's take a moment to discuss some of them now.  This discussion will focus mostly on the PANs.  Let's start with the most popular PAN in Perl:

## CPAN

The Comprehensive Perl Archive Network.  **CPAN** is a repository (or collection) of files which various authors have made available.  There is one canonical **CPAN** repository and all other **CPAN**s act as mirrors of either the canonical **CPAN** or of an additional mirror.  This means that **CPAN** is distributed geographically.  When you install from **CPAN**, you are, in many cases, actually installing from one of the **CPAN** mirrors.  Choosing one which is geographically closer to you may result in faster download speeds, however this is not always necessary.  For instance, if you choose [cpan.metacpan.org](https://cpan.metacpan.org) as your mirror, [Fastly](https://www.fastly.com/) will handle figuring out the fastest way to get the file to you.

## PAUSE

**PAUSE** is the "**P**erl **A**uthor **U**pload **SE**rver".  This isn't strictly one of the **PAN**s, but it does belong in a discussion of the various moving parts associated with **CPAN**.  **PAUSE** is the administrative layer which exists over **CPAN**.  **PAUSE** controls the processes of assigning author names to users, accepting **CPAN** uploads and deciding (via [02packages.txt](https://cpan.metacpan.org/modules/02packages.details.txt)) which files are authorized (ie official) and which are not.

## MetaCPAN

**MetaCPAN** is layer on top of **CPAN**.  Initially it was conceived of as a web API which would parse **CPAN** module metadata and serve up information about the various modules.  Hence the `meta` in **MetaCPAN**.  The **MetaCPAN** API is the core of **MetaCPAN**'s functionality.

Following the publishing of the API, a web front end was created. This search engine, now known simply as [metacpan.org](https://metacpan.org), relies exclusively on the **MetaCPAN** API to serve up its results.  So, while most people may think of the search site as **MetaCPAN** itself, it's really just one part of the project.

## BackPAN

**BackPAN** is an aggregation of what currently is and what at one point was on **CPAN**.  For various reasons, modules can be deleted from **CPAN** before they have outlived their usefulness to the general public.  If you're looking for a module which can no longer be found on **CPAN**, you can try looking on a **BackPAN**.  There are various **BackPAN**s and, unlike **CPAN** itself, there is no one canonical **BackPAN**.  This leads to a confusing situation where not every **BackPAN** may have exactly the same files, so this bears keeping in mind.

If you use [backpan.metacpan.org](https://backpan.metacpan.org) or [cpan.metacpan.org](https://cpan.metacpan.org) as the mirror which your **CPAN** install from, you are actually using a **BackPAN**, so in general you should be able to find what you need there. [backpan.metacpan.org](https://backpan.metacpan.org) is an alias to [cpan.metacpan.org](https://cpan.metacpan.org), so just use whichever name you prefer.

## MiniCPAN

Sometimes you want a copy of **CPAN** for when you're on the go, or just not able to get fast Internet access or for various other reasons.  For such occasions, you can create a **MiniCPAN**, which is essentially a copy of all of the latest modules on **CPAN**.  Since the older versions of modules are not included in a fresh **MiniCPAN**, it can be much more compact than a full **CPAN** mirror.  I use a **MiniCPAN** when I'm in airplane mode -- literally when I'm on an airplane -- because there are often times when I'm working on something while in the air and I need to install a Perl module *right now*!  See [CPAN::Mini](https://metacpan.org/pod/CPAN::Mini) for more information.

## DarkPAN

Many of us have heard of the **DarkPAN**, but nailing down exactly what it is can be difficult, so I asked on [Twitter](https://twitter.com/olafalders/status/1034113626960011264).  Based on my non-scientific poll, most people felt that **DarkPAN** was a blanket term for Perl code which does not exist in the **CPAN**.  A smaller group of people thought of the **DarkPAN** as a **MiniCPAN** which contains Perl code not in the **CPAN**.

To quote [TUX](https://metacpan.org/author/HMBRAND), a **DarkPAN** is:

> the modules and scripts used in the wild (including non-OpenSourced company stuff) that is not publicly available (through CPAN). It is the part of perl that we cannot index, cannot analyze and cannot test when we upgrade perl.

## GrayPAN

Along with my Twitter poll, [I also solicited thoughts on Reddit](https://www.reddit.com/r/perl/comments/9aqipz/what_is_the_darkpan/), which brought up the **GrayPAN**.  For this part, I'm just going to quote [KENTNL](https://metacpan.org/author/KENTNL) who says a **GrayPAN**

> is publicly accessible, but published outside the cpan infrastructure, resulting in a codebase that is factually public, but functionally non existent from the perspective of CPAN, as things can't really depend on it, and subsequently doesn't get subjected to multi Arch testing with every release, and critical breakage is likely to go unnoticed until too late.

> ...

>For example: GNU Autotools/Autoconf uses Perl substantially, and is widely used in OpenSource, but due to not being on CPAN, it doesn't attract being tested as routine against developmental versions of Perl.

>This means that when Perl makes a change that breaks Auto*, it won't be until Perl ships ( or is nearing shipping ) that people will know Autoconf is broken, which will mean there's little to no scope for avoiding breaking it.

>Greypan software is extra useful here, for another reason: It demonstrates that the argument about "stuff on darkpan could break", is not merely a strawman, but a reality.

>NB: Autotools being broken by perl changes actually happened, and late-cycle changes had to be made to Perl to avoid breakage, the "oh, consumers can fix their code" argument just isn't helpful when the consumer is both so widespread, and important, and yet, potentially have a stagnant release process.

>Hence, it's a darkpan that doesn't immediately appear "dark", but functions with most of the downsides of a darkpan

I think that's a better summary than I could provide.

# The Lower Case PANs

After much discussion about **CPAN** and friends.  What about `cpan`, `cpanp`, `cpanm` and even `cpm`?  These all have one thing in common.  They are utilities for installing modules from **CPAN**.

## cpan

Lower case `cpan` is the name of (probably) the oldest **CPAN** installer client which is still in use.  [Read the docs](https://metacpan.org/pod/cpan).

## cpanp

`cpanp` is an iteration on the functionality which was brought to us by the `cpan` client.  It was initially easier to set up than `cpan` and had more options. [Read the docs](https://metacpan.org/pod/distribution/CPANPLUS/bin/cpanp).

## cpanm

`cpanm` is the `cpanminus` command line **CPAN** installer.  The name is a play on **CPANPLUS**.  Written by [MIYAGAWA](https://metacpan.org/author/MIYAGAWA), this command line interface did away with much of the configuration involved in using both `cpan` and `cpanp`.  It offered a faster and friendlier way for people to get up and running when they wanted to install **CPAN** modules. [Read the docs](https://metacpan.org/pod/cpanm).

## cpm

`cpm` is an even faster version of `cpanm`.  It has more dependencies, but it can install modules in parallel, making it potentially much faster at installation than any of the other **CPAN** installers.  [Read the docs](https://metacpan.org/pod/App::cpm).

# In Conclusion

If you found any of this confusing, you are not alone.  To truly understand all of these tools (and even their names) it helps to understand the historical context in which they came to exist.  There's a lot of developer and community effort which has gone into the development of these tools and resources.  I'm very grateful for it as these tools save me countless hours of my own time every week. Hopefully this little walk down **CPAN**'s memory lane has been helpful to you.  In future, if a friend or colleague is confused as to the meaning of any of these terms, you'll be well placed to bore them to tears with the details.
