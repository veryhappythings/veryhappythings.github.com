---
date: '2010-02-18T00:00:00Z'
title: The Great Passenger Debacle
wordpress_id: 70
wordpress_url: http://blog.veryhappythings.co.uk/?p=70
---
It would appear that, if you have Passenger running on Apache, and you put a Rails app on it, then you may find that the whole stack dies with a 500 error with absolutely no explanation and no stack trace or log to speak of it.

I had this today and banged my head against it for hours before I found the reason: Rails had a database.yml telling it to connect to the local MySQL server, but could not, in fact, get access, because I had not created the username.

Today was not a good day, diary.
