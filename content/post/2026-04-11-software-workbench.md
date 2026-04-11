---
date: '2026-04-11T11:22:00Z'
title: A Workbench For Today's Software Engineering
---

I believe in evolving my tools. I see some developers always changing what they're using, making big leaps, and that works very well for them, but I prefer to build on what I know. For years I used vim, but these days I use Neovim. The plugins I use are always shifting, but never quickly - just tweaks here and there over time. I used to use bash, now I use zsh. Always slow, iterative improvements, nothing drastic.

I'm always on the look out for small things to improve, places where I can add tools or plugins. I think there are a few pieces of my workflow that are ripe for iteration.

## Code Review

AI agents are starting to cause a little friction. It used to be that I'd have a conversation with my team about some kind of large-scale architecture decision or concept, and then we would all go away and build for a while. The time between these big decision points is shrinking: if the core problems of the work have been solved then an AI agent can probably implement it pretty quickly. There's a lot of work where that isn't true, too, but more and more, I'm flipping between two kinds of work: discussions that produce decisions and reviewing pull requests written by agents.

The discussions are great. That's where we should be spending our time - we're talking about what really matters and moving the company forward. That's perfect. The reviews, however, are becoming a bit of a problem. They are difficult, they're getting harder, and they're very, very important. They're also hard to do continuously while maintaining a good standard of review, and it's not uncommon for our team to produce PRs that need review fast enough to require the equivalent of one full time employee reviewing 9-5. It is essential that we don't let quality slide as a result of this onslaught of AI, and I believe that it is important that we don't stop reviewing as human beings. At their best, I believe code reviews are a very productive part of the software lifecycle; they catch bugs, they increase quality, they help us plan for the future, and they help us understand our codebase. I do believe everyone should be reviewing code often. On the other hand, AI is producing a lot of code, and fast, and PRs are growing in size and complexity. The cognitive load is very high.

I think there are mitigations - good ones, that don't involve replacing human reviews. First up is documentation - it is trivial to get an agent to explain their PR. My team regularly give me PRs with diagrams and lengthy explanations that are not at all sloppy - they're genuinely great pieces of engineering. Secondly, we need to improve the ergonomics of the review process. If the engineer has put the time into breaking up their commits into easy to comprehend chunks then I can review commit by commit and understand the story in pieces, but often that hasn't happened, and often it isn't worth doing - it takes too long to refactor for my benefit. But, I believe, we can do the same thing as part of the review process.

I believe today's software engineering workbench needs a way to take a pull request and quickly break it down into pieces, so it can be reviewed and put back together again. The engineer should be able to understand the work and its context within the larger codebase, and indeed, the larger business, before making comments and hopefully approving the PR.

## Multitasking

I'm on the fence about running multiple agents in parallel. That said, I tend to run a few at a time at the moment, and I'm not really sure how I want that to work for me. I tend to be bouncing between them and progressing a few projects at a time, but maybe I really need to be focusing on just one. Even if I am running just one, chances are I'm also doing other things on slack or in a text editor. Whatever I'm doing, I'm going to be multitasking.

What I do think I'd like is a really good way to see what all my agents are doing on one pane of glass. It may well be just one agent, but if it is, I want to be able to tab away but still see it work. 2 monitors isn't a good solution - I put my work on one "main" monitor, and others are used for ambient information. I don't want to move my agent between monitors when I move to another application, so I need some way of telling me what it's doing elsewhere. If I'm running multiple agents, they're on multiple tmux tabs, so my visibility is poor. I believe there are 2 pieces to solving this:

### Better Notifications

I have notifications, but as my monitor has grown I've found that they're too far off in my peripheral vision. I want to improve them. My first thought was sound - I've long been of the opinion that computers should not be allowed to make noise, but lately I've come to think that, if I sculpt my notification sounds correctly, they could be useful. What if each agent, each codebase, each notification, could have its own sound? I could pipe all that context into some kind of algorithm that would produce a gentle, soothing chord or trill of notes. That could work.

### An Agent Dashboard

I've seen a lot of ways of running multiple agents, and I don't like any of them. They just don't fit with my workflow. What I think I want is a browser based dashboard that shows me what's going on with each agent, but remains completely separate to my workflow. Or maybe if I click on one it jumps to the tmux pane.

This is by far the least well thought out of my iterations. I think there's something here, but I'm still unsure if multi agent workflows are productive for me. I think it might just be a way to increase work in progress and reduce quality.

