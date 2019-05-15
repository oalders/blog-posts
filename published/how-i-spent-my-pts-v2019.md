# PTS

The Perl Toolchain Summit (PTS) is an [annual event](http://blogs.perl.org/users/neilb/2019/03/announcing-the-perl-toolchain-summit-2019.html), held in Europe, where work on improving the Perl toolchain takes place. 

I was fortunate to be able to attend PTS again this year.  I'd like to thank my employer, [MaxMind](https://maxmind.com), for sending me to PTS and for once again financially sponsoring this event.  PTS has become something which I really look forward to.  It's a block of time to work, undistracted, on MetaCPAN.  It's also a chance to see some friends whom I only see once or twice per year.  Having the MetaCPAN core developers in one time zone and in one room allows us to enjoy an accelerated feedback loop and get things done much, much faster than might otherwise have been the case.

This year we gathered in the UK, outside of London, to work out of Bisham Abbey, while staying in the adjacent National Sports Centre, which is part of the same complex.

![](http://olafalders.com/static/images/pts-2019/bisham-abbey.jpg)

# The Sponsors

This event simply wouldn't take place without the financial backing of our wonderful sponsors.  I'd like to thank the following donors for making this event a reality:

* [Booking.com](https://www.booking.com)
* [cPanel](https://cpanel.com)
* [MaxMind](https://www.maxmind.com/en/home)
* [FastMail](https://www.fastmail.com)
* [ZipRecruiter](https://www.ziprecruiter.com)
* [Cogendo](https://cogendo.com)
* [Elastic](https://www.elastic.co)
* [OpenCage Data](https://opencagedata.com)
* [Perl Services](https://www.perl-services.de)
* [Zoopla](https://www.zoopla.co.uk)
* [Archer Education](https://www.archeredu.com)
* [OpusVL](http://opusvl.com)
* [Oetiker+Partner](https://www.oetiker.ch)
* [SureVoIP](https://www.surevoip.co.uk)
* [YEF](http://www.yapceurope.org)

# The Attendees

These past few years, as the MetaCPAN contingent has grown, we've set up our own table at PTS.  This year we had [Leo Lapworth](https://metacpan.org/author/LLAP), [Mickey Nasriachi](https://metacpan.org/author/MICKEY), [Graham Knop](https://metacpan.org/author/HAARG) and [Shawn Sorichetti](https://metacpan.org/author/SSORICHE) and me.  [Doug Bell](https://metacpan.org/author/PREACTION) sat with us as well, while hacking on CPAN Testers.  Sadly, [Joel Berger](https://metacpan.org/author/JBERGER) was not able to attend this year or we would have forced him to sit with us as well.  [Shawn Sorichetti](https://metacpan.org/author/SSORICHE) was a new addition to our PTS table.  He had joined us at meta::hack and helped us get a lot done there, so it was great to have him hacking with us again.  He has a lot of experience with Docker and Ansible, [which we took advantage of](https://ssoriche.com/posts/2019/04/returning-from-pts-2019/).

![The MetaCPAN table. Photo by Wendy: https://www.flickr.com/photos/wendyga/](http://olafalders.com/static/images/pts-2019/table.jpg)

*Left to right: Mickey Nasriachi, Olaf Alders, Leo Lapworth and Shawn Sorichetti.  Missing from this photo is Graham Knop. Photo by Wendy: https://www.flickr.com/photos/wendyga/*

# The Goals

This year we took the first couple of hours of the hackathon to gather together as a group and hammer out a plan for the 4 day event.  We decided to address some pain points in our stack.  Firstly, our Vagrant environment, although it works, is quite unwieldy and generally not enjoyable to work with.  Secondly, our Puppet deployments are a bit confusing.  Puppet has been a great tool up to this point and a lot of time has gone into its configuration, but we're ready to move on to the next tool.

The result of our conversation was a commitment to move to Docker for both local work environments and also for live deployments.  As part of the shift to Docker we decided to start moving our automation to Ansible.  We're all quite aware that neither Docker nor Ansible are going to solve all of our issues, but we felt that on the whole it would be a net positive for all of us and for the project.

# Dockerizing the first site

We chose to start our Dockerization with [Github meets CPAN](https://gh.metacpan.org).  This was a good candidate because it's not a mission critical web service and it has enough moving parts (Mojo + MongoDB + an hourly cron job) that we'd get an idea of what might be involved in porting the other services.  This site is now deployed and in production using Docker.  This work included:

* setting up a `Dockerfile` for the site
* setting up a `docker-compose.yml` for the site
* setting up a MetaCPAN organization on DockerHub
* creating a base Docker image with a pre-installed Perl and some common Perl modules that multiple sites will be able to use
* configuring Travis to upload new images to Dockerhub after successful merges to master

Various other things happened as part of this, like setting up Ansible, setting up [keybase.io](https://keybase.io) for shared secrets and making a plan for which production server(s) to convert to serving Docker containers.

# Dockerizing more sites

As part of the above process, we spent some time on expanding the Docker configuration of [metacpan-web](https://github.com/metacpan/metacpan-web) and [metacpan-api](https://github.com/metacpan/metacpan-api).  These had previously been set up by some helpful folks outside of the core team, but internally we had not been using them in anger.  This gave us a chance to see how they really perform and to tweak them for our needs.  I was able to hack on [metacpan-web](https://github.com/metacpan/metacpan-web) and [metacpan-api](https://github.com/metacpan/metacpan-api) using Docker during PTS and I'm quite happy with the result.

# Making new uploads discoverable faster

Moving on from Docker, I was also able to speed up the amount of time it takes to view new modules on MetaCPAN.  When you upload a new release to CPAN there's a lag time before it shows up as the latest, indexed module.  This is due to several things.

* We need to rsync the module from PAUSE
* We need to rsync an updated 02packages file from PAUSE
* In some cases, we need to rsync an updated 06perms file from PAUSE

The module sync is quite fast, but 02packages and 06perms are a bit trickier, since they are created/updated by cron scripts on the PAUSE servers.  For example, as I found out at PTS, the 06packages file is updated every 12 minutes, with the exception of a 24 minute gap once per hour.  Because of the timing of your module being set as latest relies on all of the various files syncing first, we've traditionally just run a cron hourly to set the latest flag.  Now that we're using a Minion queue, we're able to set up specific queue jobs to run at set intervals after each module has been uploaded.  With this new change, if the stars align, you may find your module is discoverable within just a few minutes.  In other cases, you will still need to wait longer, but hopefully not as long as it has taken in the past.  In the worst case you'll have to wait just as long as you previously did, but hopefully this is rarely true.  This code was merged at PTS.  Hopefully you're already noticing a difference.

# Deleting unused code

Mickey and Graham spent some time auditing the codebase and they identified some code (mostly utility scripts) which people weren't generally aware of.  Since none of us were using these scripts, they will be deleted.

# Reworking the indexer

Mickey spent a lot of time getting into the guts of the indexer.  This code is non-trivial and quite difficult to refactor.  He's working to simplify how it works and was able to start breaking this into smaller chunks for deployment.  The change was too invasive to deploy at once, but this is a very important step for us to be able to upgrade to a newer version of Elasticsearch at some point.

# Fixing PAUSE account connection

For a good while there has been a problem with our previous mail sender in getting messages delivered to @cpan.org email addresses.  This is how we verify PAUSE authors on [metacpan.org](https://metacpan.org).  [FastMail](https://fastmail.com) solved this by becoming an email sponsor for us.  (I should add that FastMail is a PTS sponsor as well.)

Shortly before PTS we were able to merge the code to switch mail delivery to FastMail.  I'm happy to report that authors are now once again able to connect their PAUSE accounts to their MetaCPAN profiles.  There are still some related issues to be worked out, but it's better than it once was.

# Ripping out the guts of our OAuth

An issue that has plagued us for years is that some users have had issues linking services (like Twitter and GitHub) to their accounts on [metacpan.org](https://metacpan.org).  The issues have been really hard to reproduce and we just haven't solved them.  Since I finally had a block of uninterrupted time, I took a deep dive into our OAuth setup and I'm now in the process of simplifying how this works.  The first step is that I'm converting our OAuth flow to use [Mojolicious::Plugin::Web::Auth](https://metacpan.org/pod/Mojolicious::Plugin::Web::Auth) rather than the custom code we currently have in place for this.  This allows us to handle the same OAuth flows but with much less code.  I had looked into setting up [Auth0](https://auth0.com) for this, but our problems are with our internal flows rather than with the OAuth flows themselves.  The internal issues would need to be, fixed regardless.  It was easier for me to swap out our current code with a tiny bit of Mojo configuration than to look into setting up a third party integration and potentially having to conform to their requirements etc.  I'm not ruling out a future third party integration, but this is a logical first step in fixing this issue.

As far as deployment goes, we're going to deploy these new Mojo OAuth flows while leaving the existing flows in place, since they can co-exist and are agnostic about each other.  Once we're satisfied that the new system is working, we'll flip the switch.  This seemed like the most disruptive and least risky way of approaching the changes.  Some code was prepared for deployment at PTS, but I'll need to keep working at this in order to finish it up.

# Goodbye Facebook

A side effect of the OAuth work is we have removed Facebook authentication.  It has been broken for many months and nobody has opened a ticket that I'm aware of.

# Goodbye OpenID

As we discussed simplifying our authentication options, we decided that we'll be removing OpenID.  It doesn't appear to be heavily used and this would reduce the complexity of our code.

# Spoiler Alert

[INGY](https://metacpan.org/author/INGY) is doing some really cool stuff with tab completion and CPAN module installation.  I believe he'll have it all ready to go for you YAPC::NA, so watch for that.

[RJBS](https://metacpan.org/author/RJBS) introduced me to [JMAP](https://jmap.io/), which FastMail is using to make their UI so snappy.  It looks really interesting.

# Trivia

I learned that [cpm](https://metacpan.org/pod/App::cpm) means "CPAN Package Manager" in the spirit of "npm" for Node Package Manager.

# The Other Stuff

Although I was able to make it to PTS this year, it was a bit harder, since I had a company summit at MaxMind scheduled for the same week.  My manager was very supportive of me attending PTS, so I was able to fly from Toronto to Boston on a Monday and then fly from Boston to London the following Wednesday morning.  I had to miss two days of company meetings in order to do this, but it meant having more productive time at PTS.  I got to bed at 1:15 AM on Wednesday morning.  I got up two hours later at 3:15 AM and got ready to head to Logan.  I had no delays and I arrived at Heathrow Wednesday evening where Leo Lapworth was waiting for me.  We waited for [Dan Book](https://metacpan.org/author/GRINNZ) to arrive and then headed down to Marlow.  We took a wrong turn on the way down which added 30 minutes to our trip.  It also meant we'd be arriving at the pub *after* the kitchen had closed.  I texted Neil and he was kind enough to order and pay for our meals so that they were waiting for us when we finally arrived.  He's a real diamond geezer.  I was able to repay him in the currency of Thai food later in the week.

We left the pub after the bell for last call had been rung.  When we arrived at the National Sports Centre we were told there was good news and bad news.  The good news was that we had rooms and the bad news was that we had to share our rooms, due to an overbooking.  So, Leo and I bunked together for the night.  No troubles there.  It reminded me of the old days of PTS where having a roommate was part of the arrangement.

A warm breakfast was provided at the National Sports Centre on Thursday morning.  At breakfast I was able to connect with many of the attendees whom I had missed on the previous evening.  After finishing up there we made our way down to Bisham Abbey in order to get to work.  The venue itself is historic and quite well suited to this kind of event.  Perhaps even more importantly, the seats were comfortable and the wifi was solid.  If you have uncomfortable seats or unreliable wifi, it makes for a very long four days.  Luckily this part of things was rock solid, which I was very happy about.

On the Thursday evening, a large group of us went out to a local Thai restaurant in Marlow.  Neil met us there, with his long flowing locks, looking like he'd just stepped out of one of those blow dry salons or perhaps a TV advert for a luxurious shampoo and conditioner.  

![](https://static.fashionbeans.com/wp-content/uploads/2019/03/kitharringtonhair.jpg)

*Not an actual photo of Neil Bowers.*

One of the more interesting things about this restaurant was the low ceiling beams, which were a concussion waiting to happen.  No concussions were witnessed however.  This is likely due in no small part to the fact that low beams were well marked with the amount of clearance required to pass safely.   After a fantastic meal we headed back to the venue on foot.

![Marlow Giggling Squid](http://olafalders.com/static/images/pts-2019/marlow-giggling-squid.jpg)

*The Giggling Squid helpfully notes how much vertical clearance you need to get past various parts of the establishment.*

On Friday morning several of us went to the gym for a workout.  I used to work in a gym after my first year of university, so I figured I knew my way around the place.  However, as I was struggling with one of the machines, [Paul Johnson](https://metacpan.org/author/PJCJ) kindly pointed out to me that I was doing it backwards.

After a productive Friday, we had a really unique group meal in the Abbey.  We sat in a room which was long enough to fit us all at one table (or a series of tables?) and a three course meal was served to us.  It was the only meal where the entire group was together, sitting down at the same table and it was a really nice experience.

![Photo by Lee Johnson: https://metacpan.org/author/LEEJO](http://olafalders.com/static/images/pts-2019/group-dinner.jpg)

*Photo by Lee Johnson: https://metacpan.org/author/LEEJO*

On Saturday morning several of us went outside for a hike.  We ran into [BINGOS](https://metacpan.org/author/BINGOS) in the lobby.  He had been up all night and hadn't slept.  We invited him to join us, which he did, since he wasn't sleeping anyway.

After a productive Saturday a group of us went to a really nice Indian restaurant in Marlow where we spotted various other pockets of PTS attendees.

On Sunday morning several of us once again took advantage of the gym.  This is the first PTS I've been to where a gym was an option.  It was kind of surreal to start the day seeing [Mickey](https://metacpan.org/author/MICKEY) on a stair climber or [Ingy](https://metacpan.org/author/INGY) running on a treadmill or [RJBS](https://metacpan.org/author/RJBS) doing leg presses.

After Sunday lunch, Leo took me and Shawn back to London where we had booked a hotel in Covent Garden.  We met [simbabque](https://metacpan.org/author/SIMBABQUE) as we were literally heading out the door of Bisham Abbey.  We didn't get a chance to chat, but at least now I know what he looks like.

As far as London goes, Shawn and I knew that the London Marathon had taken place earlier that day, but I just kind of assumed that would mostly be a done deal by the time we got into town -- just a bit of cleanup and maybe some stragglers.  I had no appreciation of the scale of this event.  When we walked down to Buckingham Palace around 5:30 PM there were still hundreds of people running (and sometimes walking) to the finish line.  The official announcement was that 9,000 out of 41,000 people had yet to cross the finish line.  We saw a man running while dressed as a rotary phone.  Sadly, we missed seeing [Big Ben](https://www.bbc.com/news/uk-england-london-48084878).

The city was packed.  We eventually made it to a neighbourhood which wasn't as busy and had some fish and chips.  We had wanted to tick this particular box back at Marlow, but there was one standout Google Review that I just wasn't able to get my head around.

![](http://olafalders.com/static/images/pts-2019/marlow-fish-and-chips.png)

The London fish and chips were great and I'm happy to report that the restrooms had not been abused in the manner described above.  We also stopped at a pub on the way back.  When in Rome...

![](http://olafalders.com/static/images/pts-2019/london-pub.jpg)

*Nice little place. I have no idea what the Canadian flag was there for.*

On Monday morning we got up and had some croissants and canelé for breakfast at Paul and then did some quick shopping.  We had an early lunch at Wagamama (hat tip to [ILMARI](https://metacpan.org/author/ILMARI) for the suggestion) and then I headed to the airport.  For some reason I had a 3 hour layover at JFK on my way back to Toronto, but by early Tuesday morning I was home, just over a week after my initial departure, putting my bags away and getting ready to get back to $work in the morning.  I was tired but well chuffed and you might even say I was gruntled.  Also, I had written the bulk of this blog post on the plane, so I felt a particular sense of accomplishment.  It has taken me two weeks to publish the post, but let's not get stuck in the weeds here.  It was *mostly done* before I got home.

PTS was, as always, a great experience.  I feel like this year we made particularly good progress.  Some years I've gotten a bit frustrated with what I was working on, but this year it was surprisingly free of roadblocks and really felt like time well spent.

I want to thank [Neil](https://metacpan.org/author/NEILB), [Book](https://metacpan.org/author/BOOK) and [Laurent](https://metacpan.org/author/LBOIVIN) for all of their work in organising this event.  I would say it's a thankless job, but I just thanked them, so that would be a lie.  Having worked to organize meta::hack, which is quite small, I can only imagine how much work and organizational skills go into running a PTS that went as smoothly as this one.  I look forward to future PTS events, hoping that I get invited again!
