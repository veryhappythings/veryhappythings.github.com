---
date: '2009-10-05T00:00:00Z'
title: Web.py + Google App Engine
wordpress_id: 58
wordpress_url: http://blog.veryhappythings.co.uk/?p=58
---
I tried to write a little Hello World app for the app engine this evening and ran up against a sneaky problem.  I used some quick tutorial code and came up with an app that looked like this:

<script src="http://gist.github.com/202557.js"></script>

And it ran ok as a standalone program, but under dev_appserver.py it would hang, with no errors, and consume all of my memory and CPU time. Sucks.

After messing around a bit, I found that my problem was calling <code>app.run()</code> instead of <code>app.cgirun()</code>. 

<script src="http://gist.github.com/202549.js"></script>
