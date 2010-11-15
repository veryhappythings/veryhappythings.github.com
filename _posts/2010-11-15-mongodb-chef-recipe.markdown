---
layout: post
title: Installing MongoDB using Chef and apt-get
---

I didn't end up using apt-get, because it installs most of X in the process of installing XULRunner, but if you want a Mongo recipe, here's one that should work, more or less.  Your mileage may vary, I haven't tested it out all that thoroughly.

<script src="https://gist.github.com/700440.js?file=mongo-chef.rb"></script>

