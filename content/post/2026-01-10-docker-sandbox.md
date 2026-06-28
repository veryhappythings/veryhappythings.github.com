---
date: '2026-01-10T09:27:00Z'
title: Running Claude Code in Docker Sandbox with AWS Bedrock
---

UPDATE: The whole Docker sandbox feature has been changed since I wrote this - check out [sbx](https://docs.docker.com/ai/sandboxes/) for the latest.

I was looking into sandboxing Claude Code this week and came across Docker's relatively new
[sandbox feature](https://docs.docker.com/ai/sandboxes/claude-code/), which makes this very easy.
However, it has a few sharp edges. Most of it seems to be stuff you can work through.

In brief, this feature lets you run `docker sandbox run claude` to start a container running Claude
Code. This container gets your current working directory mounted and it's set up to be persistent, so your
sandbox has all the volumes you need for it to live on between restarts and that kind of thing. It's
pretty good!

The default Docker sandbox template includes most of what you need, but chances are for any serious
development you'll want something a bit more custom. Luckily it's easy to do. You can build off their
existing dockerfile:

```dockerfile
FROM docker/sandbox-templates:claude-code

# Install AWS CLI v2 (ARM64 for Apple Silicon)
RUN <<EOF
curl "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
rm -rf awscliv2.zip aws
EOF
```

Build it:

```bash
docker build -t claude-bedrock ./docker-sandbox/
```

And then use it as a `template`:

```bash
docker sandbox run \
  --template claude-bedrock \
  claude
```

## Configuring for AWS Bedrock

Things got a bit messier with my work setup. We use bedrock, so I have an AWS profile set up that I use to get
access. I needed to provide the sandbox with access to my AWS stuff and add some environment variables to point
it in the right direction:

```bash
docker sandbox run \
  --template claude-bedrock \
  -e CLAUDE_CODE_USE_BEDROCK=1 \
  -e AWS_PROFILE=<your profile> \
  -e AWS_REGION=<your region> \
  -v ~/.aws:/home/agent/.aws:ro \
  claude
```

Note that `/home/agent` is the user home directory within the container used by the sandbox.

Once you've run this once in a project directory, you can just run `docker sandbox run claude` in future to
get back to that same sandbox.

## Configuring Claude

My Claude Code setup is not beautiful and it is largely built on the fly as I need it, so it all lives in
`~/.claude.json`. I am rapidly learning that this is not a very portable way to do things. I tried mounting my
~/.claude` directory into the sandbox and it overwrote settings.json with a link to a mount, which is great I guess
but broke my settings for my local machine.

To get the settings I like into the sandbox, I've chosen to put them into `$(pwd)/.claude/` - mainly into
`$(pwd)/.claude/settings.local.json`. This is not optimal - we should probably be sharing more of this as a team
and committing our settings, but we aren't there yet. If you put these settings into settings.local.json you can skip
the env vars in the sandbox setup:

```json
{
  "env": {
    "CLAUDE_CODE_USE_BEDROCK": "1",
    "AWS_PROFILE": "<profile>",
    "AWS_REGION": "<region>"
  },
  "awsAuthRefresh": "aws sso login --profile <profile>"
}
```

There's a lot of useful stuff in my claude.json. Not having access to it in sandbox is going to slow the agent down,
and I'm not keen on relying on docker's volumes to maintain this memory. I think this needs improving - I can probably
work something out to share most of it. I'll have to keep playing.

The sandbox is not a perfect solution - The Atlassian MCP can't auth because it can't hit the local callback, for example.
I could probably sort this but you end up with a question of how much sandboxing you want to do. If you want a simpler option,
the built in `/sandbox` is not bad for a start.
