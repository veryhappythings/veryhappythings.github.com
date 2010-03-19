--- 
wordpress_id: 9
title: Building PIL with JPEG support under MacOS X
wordpress_url: http://blog.veryhappythings.co.uk/?p=9
layout: post
---
Getting the <a href="http://www.pythonware.com/products/pil/">Python Imaging Library</a> (v1.1.6) working in MacOS (Leopard) took a bit too much reading for my liking, so here's how I did it.

First off you'll need to get yourself <a href="http://www.ijg.org/">the libjpeg source.</a>
Basically, <a href="http://www.kyngchaos.com/wiki/macosx:build:libjpeg">Follow this guide to get libjpeg installed</a>.  It's not quite that simple though: when you run .configure, you'll need to get it to actually give you your libraries:
<code>./configure --enable-shared --enable-static</code>

You'll find the libraries in /usr/local/lib, should you need them.

You'll probably hit an error on the "make install" step, which is fixed with this:
<code>mkdir -p /usr/local/man/man1</code>

Then you should be able to download <a href="http://www.pythonware.com/products/pil/">PIL</a> and untar it.  Go into the directory and run:
<code>sudo python setup.py build</code>

And then:
<code>sudo python setup.py install</code>

That should do it.
