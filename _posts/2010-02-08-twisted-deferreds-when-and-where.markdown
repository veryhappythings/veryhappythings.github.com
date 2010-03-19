--- 
wordpress_id: 68
title: "Twisted Deferreds: When and Where"
wordpress_url: http://blog.veryhappythings.co.uk/?p=68
layout: post
---
The project I work on makes extensive use of Twisted's Deferred job system, and while it's pretty cool, it is atrociously documented (Seriously, guys, variables named 'thingy'?) and it's pretty hard to tell when to use what.  This is my attempt to pick it apart a little and lay this out - please correct me if I'm wrong.

Use defer.deferToThread if you have a slow-running function and you want to turn it into a Deferred.

Use defer.inlineCallbacks (it's a decorator) if you have a function that returns a Deferred and you want to work with it like it was a normal function.  The function you wrap will return a Deferred.

Use defer.deferredGenerator if you want to use defer.inlineCallbacks but you're on Python 2.4 or below.  Also, upgrade your version of Python.

I have seen two big mistakes come out of these three functions.

The first is mixing defer.inlineCallbacks and defer.deferredGenerator, as if they do different things.  They don't. (At least, as far as I know)

The second is writing a function using defer.inlineCallbacks and then deferring it to a thread.  It's already deferred, so you don't have to do it again.
