---
date: '2009-11-23T00:00:00Z'
title: Python 'from' statements and reloading
wordpress_id: 63
wordpress_url: http://blog.veryhappythings.co.uk/?p=63
---
Here's little gotcha I just ran into: <strong>if you import something using 'from', it will not be re-imported when you reload the enclosing module</strong>.

This means that if you have classes that contain some kind of class-wide state, and you reload the module it's in, anywhere you have imported it with a from statement will end up having a different copy of the item.

This is especially bad if you're using a test framework like nosetests, which reloads your modules for you.

Here's some code to illustrate:

<script src="http://gist.github.com/241025.js?file=reload_gotcha.py"></script>
