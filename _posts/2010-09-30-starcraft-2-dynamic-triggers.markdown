---
layout: post
title: Starcraft 2 Dynamic Triggers
---

In the Starcraft 2 galaxy editor, you can attach events to triggers, the idea being, when the event happens, the trigger fires.  In the map I'm working on, a train moves through a series of points, so I thought what I'd do is, issue the train with an order to move to the next point it needs to reach, and have it fire an event when it gets there.  I stored the points that it had to move through in an array and kept a pointer to the current location in the array.

Therefore, my event was Unit enters Point, the unit being my train, the point being <code>trainMovePoints[currentMovePoint]</code>.  And, you know what? It didn't work.  I spent a long time trying to figure it out.  It would hit the first point in the array fine, but then when it hit the second point, the event wouldn't fire.  I tried all sorts of things, until, eventually, I looked at the raw code, and it all became clear.

Events are added to triggers at load time!

When the map loaded, my trigger was being created, and the event was getting added with <code>trainMovePoints[currentMovePoint]</code> as its target point, which, of course, initialised to <code>trainMovePoints[0]</code>, because currentMovePoint is 0 when the map starts.  The Event line you see in the editor is in reality just a line of code that gets executed once and forgotten.  It is not dynamic.

So, what's the fix?  My first thought was when the train hits ANY point, it should forward on to the next one, but no dice: the API has no support for 'any point'.  After a bit of reading, I discovered that you could execute custom scripts as part of a trigger, so I took the script that added the event to my trigger and called it each time the trigger is fired after I set the relevant variables.

What I ended up with was a largely editor-written function, avoiding crossing over into script-land too much.  My first event is written by the editor, and when it fires the trigger, the trigger alters the necessary variables and uses a custom script to write itself a new event.

I have concerns about this method: it does not delete old events, so presumably these take up resources and could have an effect on the game's performance, but given my fairly low number of points, this does not seem to be an issue.  It also relies on an array of points which must be set up by hand, which I find to be quite a chore - I will consider trying to build this automatically at some point.  I suppose I could try to find the next nearest point and move towards that, but I'm not sure how much custom scripting would be involved.  The Galaxy editor doesn't provide great support for custom scripts, so I'm keen to avoid them where possible.
