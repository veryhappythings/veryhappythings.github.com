--- 
wordpress_id: 48
title: Pretty Printing in Textmate
wordpress_url: http://blog.veryhappythings.co.uk/?p=48
layout: post
---
Here's a quick and dirty snippet for pretty printing python dicts in Textmate. It's very simple and won't work in many cases due to the use of JSON, but it'll work for dicts of primitives, as long as you aren't using unicode/raw strings. Also, JSON doesn't like single quotes, so I convert them blindly to double quotes, and this could cause massive issues. In short, your mileage may vary.

<script src="http://gist.github.com/169739.js"></script>
