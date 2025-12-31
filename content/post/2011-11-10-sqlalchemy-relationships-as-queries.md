---
date: '2011-11-10T00:00:00Z'
title: SQLAlchemy Relationships as Queries
---

Coming from a rails background it seemed a bit odd to me that a relationship doesn't create a query that you can continue to work with.  That might sound a bit odd, so let me show you what I mean:

<script src="https://gist.github.com/1354883.js?file=failing_users.py"></script>

Don't worry though! If you set your relationship to be dynamic, your relationship will always be returned as a query object, that you can continue to work with.  This enables queries like "Find all the addresses that match this criteria from this user".

<script src="https://gist.github.com/1354881.js?file=success_users.py"></script>
