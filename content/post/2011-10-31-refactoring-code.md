---
date: '2011-10-31T00:00:00Z'
title: Refactoring
---

There's always bad code in projects, and like it or not, it is inevitable that you will come across a big old chunk of it sooner or later.  You can't really see it as a bad thing: every programmer should be embarrassed by their old code to some extent, and the nature of the projects we work on usually means that code stays in production for years. So, bad code is a fact of life, and as such, refactoring should be constantly on the table. Refactoring old code doesn't need to be a huge thing, or a massive project that needs undertaking: it can be many small steps.  Each day, you have the opportunity to tidy up a few files, write some tests, and generally improve your codebase.

There are a few books around on writing good code - I'm a big fan of The Pragmatic Programmer - and you can pretty much pull a list out of any of those and just budge your code around until it adheres to their suggestions, and do pretty well, but I thought I'd list some of the problems that I've found to be common in the codebases I've worked on, along with some solutions that I've found to be pretty useful.  Obviously, your mileage may vary.


The Tests Weren't Written First
-------------------------------

And, in all likelihood, the tests weren't written at all.  I know that right now, half of the audience has already tuned out, because they've already decided I'm an agile nerd and they've already gone back to trolling Hacker News, but listen for a moment: code that is written without tests in mind is hard to test.  That is the big problem. I don't care what order you write your code in as long as it ends up committed with tests and the tests test the code properly, but chances are, if you didn't write the tests first, either your tests are horrible, or your code is horrible.

So, you find some untested code in your codebase, and you can't write enough stubs in the world to get a test around it, what do you do?  The best approach, I've found, is to find the first unit.  Find the smallest thing you can possibly test, and get something written.  It's probably going to be an almost worthless test, at this stage: in my current project, I think I pretty much ended up testing the functionality of dict again.  That doesn't matter right now though.  What matters is you started on test coverage again, and you got that module into your test coverage on some level.  If someone damages that code so badly you can't even compile it, then you'll actually know about it when you run the tests next time, rather than when a customer picks it up.  For me, that first step meant that I could get Continuous Integration running, and  I could start telling people when builds were broken, and pretty soon, people wanted to add more tests into the setup.  Once you have one unit, the next unit often becomes a lot more clear.


Fixing Code Style
-----------------

A personal gripe with me is code style.  I am a bit of a [PEP-8](http://www.python.org/dev/peps/pep-0008/) nazi, but I do my best not to enforce it on other people, because I like it when people talk to me.  I do, however, believe in cleaning up truly functional code style, such as needless imports, wildcard imports, unused variables, etc., so I think that trying to convince your team to strip their own whitespace and run some kind of code lint is pretty priceless.  If you can't convince them to do that, run your own: make your code a shining example.


The Law of Demeter
------------------

The [Law of Demeter](http://en.wikipedia.org/wiki/Law_of_Demeter) basically states that each unit of code should only talk to its immediate friends, and it is the number one thing I've seen broken.  It makes finding bugs a nightmare, and fixing bugs even harder.  Writing a good test setup for code that ignores this law is brutally hard.

When you realise that code doesn't follow Demeter, you're probably already screwed, because you won't have a good test setup around it, and you won't have the confidence to refactor it without tests, and it'll be a total hassle to write a harness for.  In my opinion, the best thing to do is to black box it: write an integration test that runs the code from start to end.  Unit tests just won't work with this kind of code.

Your goal, once you have some coverage, is to pull something out.  Find something small that could feasibly stand alone, and tease it out into its own module.  Blanket it in tests, make it feel loved, and check that the integration tests still work afterwards.  Now that you have one clear, defined module, you'll find that you can take something that talks to it, mock the first bit out, and pull out another chunk of code.


The Shit List
-------------

In my opinion, every project needs a technical debt list: somewhere to write things that aren't bugs, and aren't features, but definitely need to be fixed. You might be able to make this company-wide, you might have to keep it private and only share it with one other programmer, but it should be there.  Anything you find in the project that smells bad should go on the list.  Any time you find a spare moment in between tickets, you should pick something and fix it. Cross it off and leave it on the list so you can see how far you've come. Because, y'know: knowing is half the battle.


Tl; DR
------

* Bad code happens.
* If you've got no test coverage, find a unit and write the first test.
* Get a style guide.
* If the Law of Demeter is broken, start with integration tests, unit tests won't work so well.
* Pull code apart slowly, and add tests as you go.
* Start a shit list and log all the technical debt you find.
