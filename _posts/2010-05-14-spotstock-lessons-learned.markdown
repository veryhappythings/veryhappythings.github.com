---
title: Spotstock - lessons learned
layout: post
---

At the end of the week before last I decided that Spotify's new social features were a good excuse to host a little festival using Spotify as a platform. I announced it on Twitter and facebook, and then tried to decide how best to run it.

I figured I'd try my best to just get the site done so people could see it and start thinking about their playlists, so I made it entirely static.  I put it on a rails stack so I could add to it later, which gave me an opportunity to use the awesome Heroku service for deployment.  In under an hour, the site was up.  This turned out to be really sensible, because I have a bad habit of starting projects and not finishing them.  Here I was finished before I'd even really started thinking about what I'd do.

People were interested, so the next thing to do was to find some way of getting the playlists that people posted onto the site.  I thought the Spotify API might do the job but I'd never used it, so I decided to stick to what I knew on this one and use the Twitter API.  I had code to pull searches from it in JavaScript so I just retooled that.  I'd asked everyone to use a particular hashtag, so I searched for that and dumped it to the site.  I also had some code to turn links from tweets into HTML links, so I used that too.  Now I had a website that listed playlists and a group of willing people.  I was all ready for the first day of the festival with less than 2 hours of code under my belt.  I felt a little cheap for sticking with technology that I knew well, but I think there's a lesson here - choose your battles.  I could focus on mucking about with something new when I'd served up what I'd promised to the festival-goers.

Monday came around and we kicked off with 8 or 9 playlists that morning! I was half expecting to do all the music work myself, but apparently I had captured some imaginations and there was plenty of music to sift through.  People were also using the hashtag to talk about the playlists, so I wrote a few more lines of JS to filter out anything that didn't include a playlist link.

On Tuesday, someone posted their playlist in a bit.ly link, which I hadn't accounted for, and as soon as I found time, I used the bit.ly API to expand the links and find stages.  I know it was only a tiny change, but this act of writing features only when they were needed by users was really refreshing.  A million people have said it before me, but it's true: users absolutely must drive development.

While I'm thinking about it, bit.ly's API is perfect.  It couldn't be simpler.  Good work!

Towards the end of the festival I wanted to immortalise the links to the playlists so that they could be found later.  Twitter's API isn't so great for finding historical data so I moved my search code into rails and stored my search findings into a database.  The festival was still going, but Heroku does not provide hourly cron jobs for free, so I made an admin page that would run the search and dump it to a database whenever I hit it.  I made the job stateless so I could mash refresh on it as often or as little as I liked.  Heroku and rails are my saviours here: it was so easy to do all this, I feel like I cheated.

All told, for a tiny week-long project that I thought up on a Friday, it was a huge success.  Thank you so much to everyone who contributed and listened - you made everyone involved very happy.  Thanks to Spotify, the playlists live on, and they're all still available on the Spotstock website.

Here are some stats:
* 24 playlists
* over 20 hours of music
* 10 people
* 188 unique visits
* 505 hits
