# About the Various PANs

When it comes to **CPAN**, Perl has a lot of related acronyms, many of which can be hard to understand.  Let's take a moment to discuss some of them now. 

## CPAN

The Comprehensive Perl Archive Network.  **CPAN** is a repository (or collection) of files which various authors have made available.  There is one canonical **CPAN** repository and all other **CPAN**s act as mirrors of either the canonical **CPAN** or of an additional mirror.  This means that **CPAN** is distributed geographically.  When you install from **CPAN**, you are, in many cases, actually installing from one of the **CPAN** mirrors.  Choosing one which is geographically closer to you, may result in faster download speeds.

## PAUSE

**PAUSE** is the "Perl Author Upload Server".  This isn't strictly one of the **PAN**s, but it does belong in a discussion of the various moving parts associated with **CPAN**.  **PAUSE** is the administrative layer which exists over **CPAN**.  **PAUSE** controls the processes of assigning author names to users, accepting **CPAN** uploads and deciding (via 02packages.txt) which files are authorized (ie official) and which are not.

## MetaCPAN

**MetaCPAN** is layer on top of **CPAN**.  Initially it was conceived of as a web API which would parse **CPAN** module metadata and serve up information about the various modules.  Hence the `meta` in **MetaCPAN**.  The **MetaCPAN** API is the core of **MetaCPAN**'s functionality.  [metacpan.org](https://metacpan.org), the search engine, relies exclusively on the **MetaCPAN** API to serve up its results.  So, while most people may think of the search site as **MetaCPAN** itself, it's really just one part of the project.

## BackPAN

**BackPAN** is an aggregation of what currently is and what at one point was on **CPAN**.  For various reasons, modules can be deleted from **CPAN** before they have outlived their usefulness to the general public.  If you're looking for a module which can no longer be found on **CPAN**, you can try looking on a **BackPAN**.  There are various **BackPAN**s and, unlike **CPAN** itself, there is no one canonical **BackPAN**.  This leads to a confusing situation where not every **BackPAN** may have exactly the same files, so this bears keeping in mind.

If you use **MetaCPAN**'s **CPAN** repository as the mirror which your **CPAN** install from, you are actually using a **BackPAN**, so in general you should be able to find what you need there. 

## DarkPAN

[go to reddit thread for definitions of these]

## GrayPAN

## cpan

Lower case **cpan** is the name of (probably) the oldest **CPAN** installer client which is still in use.

## cpanplus

**cpanplus** is an iteration on the functionality which was brought to us by the **cpan** client.  It was initially easier to set up than **cpan** and had more options.

## cpanm

**cpanm** is the **cpanminus** command line **CPAN** installer.  The name is a play on **cpanplus**.  Written by MIYAGAWA, this command line interface did away with much of the configuration involved in using both **cpan** and **cpanplus**.  It offered a faster and friendlier way for people to get up and running when they wanted to install **CPAN** modules

## cpm

**cpm** is an even faster version of **cpanm**.  It has more dependencies, but it can install modules in parallel, making it potentially much faster at installation than any of the other **CPAN** installers.