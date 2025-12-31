---
date: '2009-06-23T00:00:00Z'
title: Pickling Complex Classes
wordpress_id: 33
wordpress_url: http://blog.veryhappythings.co.uk/?p=33
---
If you've got a particularly complicated python class that you need to pickle, you might find that it's a little unusual when it comes out the other side.  This could be because by default <code>__init__</code> and <code>__new__</code> aren't called on objects when they're unpickled.

If you need your init/new behaviour to be called at unpickle time, you can define <code>__getinitargs__</code> or <code>__getnewargs__</code> to request the methods you want to be run.  These methods should return tuples containing the variables you'd like to call the method of your choice with.  They're called at pickle time and the tuple that they generate is stored with the object. 

More detailed information can be found in <a href="http://www.python.org/doc/2.5.2/lib/pickle-inst.html">this useful guide</a>.
