---
date: '2010-08-13T00:00:00Z'
title: Running SSH Tunnels as Daemons
---

If you need an ssh tunnel, chances are good that you need it to hang about for a while, so you might as well stick it in the background somewhere.  Start-stop-daemon is a good candidate tool for the job, but it's pretty weird to use.  Here's what I figured out.

<script src="http://gist.github.com/522575.js?file=ssh_tunnel.sh"></script>
