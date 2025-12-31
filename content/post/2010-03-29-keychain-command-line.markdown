---
date: '2010-03-29T00:00:00Z'
title: Accessing OSX's Keychain From The Command Line
---

I've been complaining about OSX's keychain and it's lack of command line support for a while, and today I decided to fix that. I did a bit of googling and found that there is an app called security, that comes with it, and is very full-featured, but is a bit of a pain to handle. Here's a quick and dirty bash function encapsulating a chunky one-liner to grab the password of your choice and put it on the clipboard. Obviously alter it to add your keychain - I only use one regularly so I just set it to that manually.

<code>
function getpass() { security -q find-generic-password -g -l $@ "/Users/mac/Library/Keychains/your_keychain.keychain" 2>&1 | awk -F\" '/^password:/ {print $2}' | pbcopy ;}
</code>

I'm sure it can be done better, but the docs don't seem to offer a way to print the password on its own or copy it to the clipboard, so I had to use awk to drag it out.
