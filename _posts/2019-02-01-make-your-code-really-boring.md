---
layout: post
title: Make Your Code Really Boring
---

This blog post is adapted from a talk I gave at [PyCon UK 2018](https://2018.pyconuk.org/). You can find the talk [here](https://www.youtube.com/watch?v=rwot0-FqRF8), and the slides [here](http://www.veryhappythings.co.uk/really_boring.pdf).

I’ve been developing software professionally for over 10 years, and I’ve made a lot of mistakes. A lot of those mistakes were down to me trying to do something a bit too clever and a bit too exciting when it should have been simple, so I’m going to work through some techniques I’ve learned the hard way for making things nice and boring.

This blog post has its roots in a lot of other people’s work - I’ve referenced everything I can, and if you want to dig deeper into anything, all of the references are linked.

Here’s my argument for boring code in a nutshell: your business has already got enough problems, whether they’ve come from your requirements, your customers, your contractual obligations - and those problems are worth money. Don’t give yourself any more problems to worry about. Hopefully this seems like a pretty obvious statement.

[Out of the Tar Pit](https://quoteinvestigator.com/2015/12/18/live/) describes these problems as accidental complexity, as opposed to essential complexity.

## Predictable and Manageable

So how do we avoid accidental complexity? I’ve decided to take an analytical knife and slice our aims into two categories. In reality they overlap, but I’m happy that these are things worth talking about separately, so for the purposes of this post, we’re going to discuss how to make code predictable - so that before you do a thing, you should more or less know what’s going to happen - and manageable - so as you work on a thing, you can fit everything you need to know about the thing in your head. These interact quite well - if something is predictable, it can leave your head, making things more manageable.

## Predictable

“Before I do a thing, I should more or less know what’s going to happen”

So first up, let’s talk about predictability. How do we make sure that a thing behaves like we expect it to? We’re basically talking about usability here - imagine yourself as a designer working on making an interface really easy to use, but the interface is your code. Your code is a user interface. Every line of code is. Think about your audience and cater to them.

This Philip Pullman quote from [Daemon Voices](https://www.goodreads.com/book/show/35478763-daemon-voices) frames what I’m talking about:

“This is partly a matter of taste. But a limpid clarity is a great virtue. If you get that right, the story you tell will not seem to anyone as if it’s intended for someone else. No one is shut out. There are no special codes you have to master before you can follow what’s going on. Of course, this sort of thing has been said many times before; W. H. Auden and George Orwell both compared good prose to a clear windowpane: something we look through, not at.

Which naturally brings me to the next question: what are you inviting your audience to look at, through the window of your telling? What are you showing them?”

Your code should be a window onto the problems you’re trying to solve. Let people look through your code into the subject matter, don’t make your code be the thing they’re trying to decipher.

### The principle of least astonishment

If there’s one thing you learn from this blog I’d like it to be this - [the principle of least astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment) - I love this idea. One great thing about this principle is that in my opinion, the principle follows its own principles - you basically know what it means already. It states that your users (and remember, that means you, and your fellow developers) should not be surprised by what your interface does. It should always do what they expect.

There’s a caveat with this that I think is important. Ruby, one of my favourite programming languages, is designed according to the principle of least astonishment, and that in itself surprises some people who pick up Ruby because they find it unpredictable. Matz, Ruby’s creator, has explained this: he says...

“The principle of least surprise means principle of least my surprise. And it means the principle of least surprise after you learn Ruby very well.” - Matz

So, he’s talking about consistency. He’s talking about conventions. He’s saying that if one part of a language does one thing, the other parts should follow suit. That’s not the same thing as being easy for beginners to learn. He’s thought about his audience and aimed his language at them. It just so happens that his audience is himself.

You might think this clashes with the Pullman quote from earlier, but both are talking about who you're speaking to - Pullman, as a writer, writes for people who understand English. You, as a developer, have to write code that can be read by who can read code. So what we’re saying here is: a writer doesn't use unnecessarily big words, and you shouldn't write unnecessarily complex code.

Think about this as you write your interfaces. Think about this as you write every line. There’s a usability book called [Don’t Make Me Think](https://www.goodreads.com/book/show/18197267-don-t-make-me-think-revisited), and the title says it all, for me. Make your code so obvious the reader doesn’t have to think about what it does.

So how do we implement that? Let’s talk about naming things. Names are how we tell our audience what things do. People say it’s one of the hardest things in programming and I tend to agree. I’ve got two real world actual examples of naming things going wrong. First up, CSS classes! Take a look at these ones: we’ve got green and red, and they make things green and red, cool… And then we’ve got yellow. What colour is yellow? For anyone who can’t read hex, that’s black. Black is not yellow.

```css
.green td {
    color: green;
}
.red td {
    color: red;
}
.yellow td {
    color: #000;
}
```

Buuuut hang on, this goes deeper. What do these change the colours of? The background? The text? You have to read the whole thing for this to make any sense. That’s what we want to avoid - we want to find a way to give our reader clues as to what something is going to do before they read the whole thing or try it out.

So let’s fix this: let's separate the implementation - the colour - from the intention - what is it trying to do? Well, what it actually does is colour the text of any table cell element within the class. Does that even make sense? So we have to put this on a table row, and then all the text within that becomes the colour we’ve chosen.

These are classes, that’s good, that tells us they can safely be reused. I like that! We can keep the classes. I’m not quite sure why it wants to be on a row, but let’s keep that, because it’s part of the original intention, whatever that may be. And the colours, well, let’s talk about what they mean. What do I actually want to present here? We can never truly know - this is an abstract piece of code - but let’s say we’re talking about successes and failures.

```css
.row--failure td {
    color: red;
}
.row--success td {
    color: green;
}
.row--warning td {
    color: #000;
}
```

Now there are standards for CSS naming conventions like [BEM](http://getbem.com/) and you should follow whichever of those you’re using - I’m just using this as an example - but here we’ve added the intended usage in two different ways - we’ve stated that we expect it to be used on a row, so the user has a hint as to how they should use it, and we’ve stated the intention - to mark a success, a failure, or a warning, instead of the colour we’re going to use, so that the implementation can change - like when the design changes - while the usage and intention remains the same.

We’ve separated intention from implementation.

### Noecho

One more example I’d like to mention is the story of noecho. Noecho was a variable in a previous product I worked on that controlled whether or not the user would see the output returned by an external device. It worked like this:

You sent the device a command, usually over SSH, and it would return you data. If noecho was set to True, you would receive the data. If it was false, you would not. I bet some of you have worked out what’s wrong here but some of you are still scratching your heads because the double negative throws your logic circuits off. I’m not sure how this ever happened, but it must have made sense at the time.

So what can we change here? First up, lose the “no” - always make your variables handle the positive case, if you can, because that’s a lot easier to understand. So now we have “echo”, and you set it to true to see feedback - an echo - from the device. Ok, that’s great! Is echo the right word? Maybe output, or receive_output, would be more appropriate, but echo’s pretty good, it mimics bash, that’ll do.

And when you get down to a variable name like that - that controls whether or not a thing is returned to you - you might be able to guess what we did next: we got rid of it. Why would you ever send a command and not care about the result? Ok, I can think of a few occasions but now we’ve reduced it down to conventional programming control flow: just don’t do anything with the returned value from the function. Job done.

### Removing the possibility of myths

So noecho was gone, lost to git history. But it wasn’t that simple! This variable was actually a part of an API we exposed to customers. And we didn’t version that API - oh no! Why not? Well, the way this worked was, our customers wrote XML documents that were effectively code, and they used our API. We were young and naive, and we didn’t have a schema, and our code was very permissive, so if you wrote XML that used noecho… It still worked. It just did nothing. The road to fixing this was pretty complicated, because enforcing a schema would break a lot of existing scripts, and we couldn’t really afford to add versioning and handle it completely, so we were stuck with a myth: people would add noecho when they didn’t get a response from something - they’d find it was another problem, fix it - and then noecho would just stay there. It was everywhere.

This is an anti-pattern I’ve seen a lot - it happens whenever a method can take arbitrary variables, but it also happens anywhere that doubt could creep in. If something isn’t documented well, if something can work at compile time, myths start forming. Data that doesn’t match your intended usage starts to collect. One example is django settings files - who knows if a setting in there is still in use?

From day one, we should have let a compiler do that work for us: we should have built a schema for the XML, so you couldn’t write anything invalid, and we should have been strict under the hood with what we received.

### So Let’s Do It

The best practice is easy to talk about though - let’s talk about a problem that I find in codebases and apply a solution. The problem was this use of keyword arguments when you mean to have a function argument. I’ve seen this pattern a lot, and I guess there are two main reasons for it happening: one, you’re not sure what even gets passed into this method, so you just put everything in keyword arguments, and two, you have too many arguments so you think that folding them up into a dictionary looks nicer.

So we start with a function, and it looks like this:

```python
def foo(*args, **kwargs):
    If ‘bar’ in kwargs:
        bar = kwargs[‘bar’]
    Else:
        bar = None
```

Or

```python
baz = kwargs.get(‘baz’, None)
```

And you just know that this function is going to be 200 lines long and it’s going to repeat this pattern at random intervals throughout it. The simplest thing we can do is to pull one argument out of keyword arguments and make it explicit:

```python
def foo(noecho=False, *args, **kwargs):
```

The behaviour here is subtly different though. Python 3 gives us [keyword only arguments](https://www.python.org/dev/peps/pep-3102/), so we can do this and maintain exactly the same behaviour from the outside:

```python
Def foo(*args, noecho=False, **kwargs):
```

Nice! And now we can use [type hints](https://docs.python.org/3/library/typing.html):

```python
Def foo(*args, noecho: bool = False, **kwargs):
```

And now we’ve gone from giving our future code reader a total mystery to giving them auto-completion in their text editor. Nice!

```python
Def foo(*args, noecho: bool = False, **kwargs):
    “”” Do the thing

        :param noecho: When Echo back data received by the device
    “””
```

And a few more keystrokes even gives them an explanation of what the argument does. Amazing. We’ve used built in language features to reduce the amount of logic we had to write, and we’ve made life easier for all the developers who see this in future.

Take time to make your function signatures explain what you’re doing: explicit arguments, type hints, and docstrings all help remove myths. Stop things from working if they’re invalid. Don’t allow for doubt. Someone will build on top of your myths. Someone will document them.


### Documentation

Finally for predictability, let’s move on to documentation. There are lots of talks dedicated to documentation. Other people can tell you how to write really good docs, and you should listen to them, but docs go a long way to making things predictable, so here are a few things you can do to make things a lot more boring without much effort:

Have a setup guide. Write down the steps you need to do to get your software running. Yes, I know you’re supposed to make a build in one command but lots of projects just aren’t there yet. Once you have your steps written down you can work on automating and reducing them.

Next, destroy the questions. Every time someone asks a question, ask them where they've already looked for the answer. Decide where the answer should be, together. Then write it. Make sure no-one else ever has to answer that question again. There’s a quote that pops into my head whenever I think about documentation:

“The only interesting answers are those that destroy the questions” - Susan Sontag

To me this means that yes, you can use documentation to ensure that no-one has to answer the question again, but that’s not the interesting answer. For bonus points, change the code. Make it so boring that no-one could even ask the question again. Use the documentation you write as a springboard to find that really boring place where all of this complexity you’re writing about just fades away.

In an uncertain environment - like a legacy codebase no-one understands - find facts and record them. It doesn't matter if you write an incomplete bit of documentation - even writing down what type one of your arguments should be will help. Even a comment about something you worked out while you were debugging can help. Slowly gather facts. You can make a better set of docs later - what you’re building now is just the first draft.

And once you have the notes, you can apply all the other steps to make your documentation less necessary.

When I started at Virtualstock we had a readme full of troubleshooting tips but it had no real structure. I wrote down all the steps I took to get things working. It was quite a long list, but I committed it all the same. Then I looked through them to see what I could automate. I wrote scripts, I deleted things, I changed defaults, and when other people started I asked them to follow my list. Now most people get up and running in no time at all. The best thing we’ve done with our docs is to iterate on them - we add things even if they’re not perfect, and we build on them as we go.

## Manageable

“As I work in the project, I should be able to fit everything I need in my head”

So that’s a few things to help make your code more predictable. Let’s get onto the second half of the talk: making things manageable. What I'm talking about here is making sure you can work on a codebase day by day without struggling.

### Conventions and Frameworks

Strong conventions and frameworks help developers to forget things. If you know that what you're working on basically fits into something standard you can forget about the standard and focus on the problem at hand. Let's take django as an example - if you see a django project you know that you can run manage migrate, manage run server, and hit the url it gives you and it should just work. As a project owner, you should endeavour to make sure this continues to just work, because that way, anyone who starts on your project and already knows django can just get going. You get free [documentation](https://docs.djangoproject.com/), too - to learn how your project works they can just follow the django tutorial.

What’s more, frameworks and design patterns give you a common vocabulary between your team members for free.

What happens when you don't use a framework? Well, you write your own framework. Whether you mean to or not, you probably end up doing it. My only advice here is to keep it simple and use standard tools - stick it all in a makefile and document it with a readme. Good luck.

Here's a big mistake I made once - I was writing a way of defining mobile phone plans and I thought it'd be great to make this writable by non-developers, so I wrote a domain specific language. Or at least, I tried. Here’s the thing I learned about domain specific languages - they’re languages, and you have to be pretty clever to write a good language, and I’m not clever enough. I tried to make python look as far from python as I could and it was awful. For example, I overrode the plus operator so that you could add bundles onto a mobile phone plan, and sure, it looked OK - but under the hood it was unreadable. I should have used lists, and wrote methods on objects, and avoided anything that felt magical. Sure, readability counts, and beautiful is better than ugly, but don’t forget, simple is better than complex. The readability of the entire stack matters, from top to bottom - if it’s ugly behind the scenes, it’s ugly.

So here’s my lesson: don't redefine language features - build on top of them. For example, don't write a method called unconditionally in your class’s initialisation method that you intend your subclasses to override, like this one on the slide. Your special cases aren’t special enough to break the rules.

### Boundaries

Let’s talk a bit about building boundaries. A man named Gary Bernhardt has an excellent talk called [Boundaries](https://www.youtube.com/watch?v=eOYal8elnZk), and you should all watch it - I’ll try not to repeat it. Like frameworks, boundaries in your code should let your developers forget things. In practice, defining boundaries is a game of limiting how far data travels, and how far into objects and modules you reach. Build big walls around your complicated code and make it really obvious what goes in and what comes out - you can unit test everything in the middle.

Try to separate your database and network access from your business logic - let your logic act on data that could have come from anywhere.

I had this great idea to explain this where I was going to say “treat your scary code like a nuclear reactor”. I thought that sounded great! And then I looked up how nuclear reactors worked, and decided it didn’t make any sense, so that was that out the window. Here's a slightly better analogy: treat your scary code the way we treat broken nuclear reactors. Big concrete sarcophagus around the outside, as little in and out as possible. Put all your inputs and outputs on the outside of the concrete.

### Testing

And finally let’s talk about testing. I’m sure you’ve all been told that testing is good - it really is - but here’s an angle on it that I don’t hear people talk about quite so much and I see missing from projects:

Make your code easy to test manually. I mean, make it easy to run like a user would run it. When you change your code, run your unit tests, obviously - and then run the script, click the button, scroll around the page a bit - does it actually still work? Will the user actually like it?

This is one of those things that sounds really obvious,  but it’s surprising how complex apps can start to require more and more state before it’s possible to… Just… Run them. Does that CSV importing function need a specific kind of CSV? Include it with your app. Do you need a specific database setup just to visit a page? Include it as fixtures or factories. How hard is it to see the shopping cart page of your app? You probably need a user and a product at least - and how hard is it for your developers to get those things set up? It should be very, very easy.

## Summary

So, in summary:

* Make your code predictable
  * Name things well - intention, not implementation
  * Eliminate the possibility of myths - use the compiler
  * Build your documentation bit by bit, by answering questions
* Make your code manageable
  * Use existing conventions and frameworks
  * Build boundaries - separate your logic from your inputs and outputs
  * Make your code easy to test and easy to run
