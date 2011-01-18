---
layout: post
title: Python DSLs - Do's and Don'ts
---

DSLs are becoming a little more popular in Python, now that Django is gaining popularity, and as they appear, I know that we're going to see a few bad patterns showing up as well as a load of great stuff.  I wrote a pretty big DSL to describe plans in [Billmonitor](http://www.billmonitor.com), so I've made a few mistakes myself, so I thought I'd write the lessons I learned along the way.

Everything You've Written Before Is A DSL
-----------------------------------------

Every time you write a program, you define new words that you then put together to create something else.  You're writing your own language that defines things that relate to your domain.  That's all a DSL is - don't make the mistake of thinking that your DSL is anything special, it's just another program.  It's sort of a special case of programming - something that you fully intend other people to use, presumably without knowing the rest of the language.  And as we all know, special cases aren't special enough to break the rules.

Beautiful is better than ugly - Readability Counts
------------------------------

These are the two parts of the Zen of Python that positively encourage DSL writing, but don't forget about that whole special cases thing.  I made the mistake of jumping through a lot of hoops to make things look as tidy as I could, but the end result was a terribly unflexible language.

Flat is better than nested.
---------------------------

If you're anything like me, your DSL will tend towards dicts of dicts everywhere it has the chance.  This will slowly become completely unusable.  If you've got any Ruby in your blood, you'll also start writing blocks using context managers.  Blocks can be great, and I thoroughly recommend them, but keep your code flat - Python Zen applies at all times.

There should be one-- and preferably only one --obvious way to do it.
---------------------------------------------------------------------

If there was one rule I wish Python developers would always follow, it's this one, and since you're writing a new language on top of Python, you absolutely must follow it.  Please.  Think of your users.

Explicit is better than implicit.
---------------------------------

And finally the most important bit I want to impress upon you: you will be tempted to encapsulate behaviour.  After all, that's probably why you're writing a DSL - you are wrapping up your complex domain in a more user-friendly language.  That's ok, I understand.  The thing is, though, in doing so, you will probably create two layers: one user-facing, very tidy, nice layer, and a real dungeon beneath that is comprehensible to no-one.  You need to make sure that this definition is clean and tidy, and the right bits are on the right sides.  Make that bit explicit.  Keep the boundaries between classes and other bits of functionality really clean and tight, and make sure that everything holds together well.  This stuff, of course, is true of any piece of code, which comes back to my first point: a DSL is just code.  Solid unit testing helps to enforce this behaviour, and I suggest you keep your DSL under a bank of both unit and integration tests - units for the individual bits of functionality, and integration to test that your user experience is what you want it to be.

Resist the urge to use wildcard imports. Just don't do it! Ever!  They seem like a good idea in DSLs, and the Django community is a little guilty of this, because they skip all that nasty importing and let you pretend that you're not writing Python for a bit.  Fact is, though, you are, and you need to remember that, because Python isn't the flexible language that Ruby is - it's a hard, cold, explicit language.  It is rigid and your DSL should follow the same rules.  Python's strengths are in readability and robustness: don't give that up just for a tiny gain in prettiness.
