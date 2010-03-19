--- 
wordpress_id: 52
title: Using setuptools with Jython 2.5
wordpress_url: http://blog.veryhappythings.co.uk/?p=52
layout: post
---
Thanks to the people on <a href="http://www.nabble.com/Easy_install-on-jython-2.5-td23525500.html">jython-users</a> for this one.

On newer versions of Jython, you can use easy_install to grab python packages easily.
<code>
wget http://peak.telecommunity.com/dist/ez_setup.py
jython ez_setup.py
</code>

Your shiny jython-based easy_install will be available at $JYTHON_HOME/bin/easy_install. Brilliant.
