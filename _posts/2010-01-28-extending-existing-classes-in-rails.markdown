--- 
wordpress_id: 65
title: Extending Existing Classes in Rails
wordpress_url: http://blog.veryhappythings.co.uk/?p=65
layout: post
---
If you want to make a new class in Rails that isn't a model or controller, you'll probably put it into the lib directory.  If you name it sensibly, then Rails will automagically find it when you reference it in your code.  That's cool, but if you extend an existing class, Rails will find the existing class, and as such won't go looking for yours.  Therefore you'll need to require your shiny new alterations explicitly in <strong>environment.rb</strong>.

<code>require Rails.root.join('lib', 'hash.rb')</code>
