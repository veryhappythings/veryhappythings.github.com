--- 
wordpress_id: 37
title: Sorting dicts of dicts
wordpress_url: http://blog.veryhappythings.co.uk/?p=37
layout: post
---
So, say you've got these dicts of files and metadata that, for whatever reason, you need in a dict.  What if you need to sort that list while maintaining key/value pairs?  (I know that this example is contrived, but I actually have this problem in something I'm working on)

Itemgetter will do you just fine if you don't need the key/value pairs to be maintained, but if you need the pairs intact, a lambda will do the same job for you.

<script src="http://gist.github.com/136400.js"></script>
