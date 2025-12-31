---
date: '2009-05-09T00:00:00Z'
title: Rails 2.1.0 scaffolding
wordpress_id: 11
wordpress_url: http://blog.veryhappythings.co.uk/?p=11
---
I feel the need to post this because it's not very clear to new rails users and if someone had posted it before my life would have been easier.

Most Ruby on Rails tutorials are, for a start, incorrect, because they're old (Thanks to <a href="http://davidlynch.org/blog/2008/01/rails-20-scaffolding/">David Lynch</a> for helping me on that front), but even those that feature the slightly more up to date information aren't clear on one point.  This is the point.

If you do not define any fields when you execute the "generate scaffold" command, you will not see any columns in your scaffold pages.

Most tutorials imply that the scaffold will grab your columns from the database.  It won't.  It makes no effort to by default.  To check out how to define columns, try typing

<code>script/generate scaffold</code>

That should help you out a bit.

I'm a rails novice so I won't put any more information in this post, but hopefully it'll save someone from being as confused as I was when my code didn't do what the tutorials said it would.
