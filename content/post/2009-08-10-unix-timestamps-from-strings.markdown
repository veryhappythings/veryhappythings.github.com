---
date: '2009-08-10T00:00:00Z'
title: UNIX timestamps from strings
wordpress_id: 44
wordpress_url: http://blog.veryhappythings.co.uk/?p=44
---
If you have a string, let's say, "2009-02-19 02:18:00", and you need a unix timestamp, the <a href="http://labix.org/python-dateutil">dateutils</a> library is super useful.
You can grab it with

<code>sudo easy_install python-dateutil</code>

and then you can run your strings through it like so:

<code>dateutil.parser.parse('2009-02-19 02:18:00').strftime('%s')</code>

Why isn't '%s' anywhere to be found in the <a href="http://docs.python.org/library/datetime.html#strftime-behavior">strftime docs</a>? That would make this somewhat easier to figure out.

Note that this returns a string containing the number, not the number itself.

