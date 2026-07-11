---
date: '2026-07-11T07:33:00Z'
title: Running Claude Code in Docker SBX with AWS Bedrock
---

[I wrote a post in January](https://www.veryhappythings.co.uk/post/2026-01-10-docker-sandbox/) about Docker's new
sandbox feature. They almost immediately reworked it and made a whole new command, `sbx`, so I guess I'd better
rewrite the post.

[sbx](https://docs.docker.com/ai/sandboxes/) is a totally new app, not nested in Docker at all. It's easy to get started
with - you install it as you like, run `sbx run claude .` and you get Claude Code running in a docker container using
sensible defaults inside that directly. If you just want to run the default options, that's it, you're done. Under this
mode, the working directory you give it (`.`) is mounted into the container. You can give it multiple working
directories and they'll all get mounted in.

There's also `--clone`, which will clone the git repo you give it instead of mounting it. Useful for building stand
alone agents running on their own branches.

Sandboxes are semi-ephemeral - they don't get destroyed unless you destroy them, but they're designed to be pretty
disposable, being containers.

When you want to go beyond the defaults, there are [two customization options](https://docs.docker.com/ai/sandboxes/customize/).
Templates - which are essentially just bringing your own Dockerfile - and kits, which are packagable collections of
files which allow you to add add a bunch of files and run your own commands on startup or creation. I'm going to talk
about kits a bit. They're experimental and likely to change, so [read the
docs](https://docs.docker.com/ai/sandboxes/customize/kits/).

Kits start with a yaml file and optionally have more stuff. The yaml file can list networking permissions (e.g. a list
of domains the sandbox can call out to), environment variables, and commands to run creation or startup. They can also
include agent context to inject, and credentials to pass in to the sandbox.

## Yeah but what about AWS Bedrock?

I said I'd talk about Bedrock. This is where I had issues - this whole setup works great until you need to get it
talking to Bedrock. Especially in the EU.

First problem, networking. Easy one, just allow the domains:

```yaml
schemaVersion: "1"
kind: mixin
name: bedrock-eu
displayName: Claude on Bedrock EU
description: Run Claude Code in a Docker SBX with AWS Bedrock in the EU

network:
  allowedDomains:
    - "bedrock.eu-west-2.amazonaws.com:443"
    - "bedrock-runtime.eu-west-2.amazonaws.com:443"
```

And of course you'll need to set your env vars:

```yaml
environment:
    variables:
        AWS_REGION: "eu-west-2"
        AWS_DEFAULT_REGION: "eu-west-2"
        CLAUDE_CODE_USE_BEDROCK: "1"
```

Then you need to get the credentials in. There are a few options, based on how you handle your creds: you can copy in
your ~/.aws/config with your profiles in, and it'll be able to do everything you do. To do this, you create a directory
called "files" within your kit, and put your config file in there - in "files/home/.aws/config". Then you'll be able to
`aws sso login` inside the sandbox.

There's an experimental, undocumented sandbox inside sbx called `claude-bedrock` that goes a step further: it will
inject your current credentials into the sandbox. This is great because you can scope those credentials and it can't
break out of them. Obviously being undocumented, it's likely to change, but it gives you an idea of what the credentials
injection can do. You can use that, and the above kit, like this:

```
AWS_PROFILE=my-profile sbx run --kit ./path/to/kits/bedrock-eu claude-bedrock .
```

That injects that profile's credentials into the sandbox, mixes in my kit, and gives me something that talks to EU
Bedrock.

There are a few bits missing. You will almost certainly want to install things, which is easy:

```yaml
commands:
    install:
        command: apt udate && apt install -yq pre-commit
        user: "root"
        description: Install deps
```

With these building blocks you can produce a kit that ships everything you use day to day into the container in a small,
easy to distribute package. I think this has a bright future ahead of it, and I hope it keeps getting developed.
