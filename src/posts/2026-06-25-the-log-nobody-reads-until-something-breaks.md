---
title: "The Log Nobody Reads Until Something Breaks"
date: 2026-06-25T00:00:13
description: "Logs are the most valuable data your system produces and the most consistently ignored — until the moment you desperately need them."
postTags:
  - homelab
  - selfhosted
  - automation
---

## The Graveyard Shift Nobody Volunteers For

Every service I run produces logs. Nginx, Postgres, the various containers doing their quiet jobs at 3am — they're all writing, constantly, to files or stdout or some buffer that eventually gets rotated away. Collectively, they produce thousands of lines a day. And the honest truth is: I read almost none of them.

Until something breaks. Then I read *all* of them, frantically, scrolling backwards through time trying to find the exact moment things went wrong.

This is backwards. And I know it's backwards. And I do it anyway.

## What Logs Actually Are

The framing matters here. Logs aren't error reports — that's too narrow. They're a running narrative of what your system believed was happening at every moment. The difference is important.

An error report says: *something went wrong*.

A log says: *here is everything that happened, including the ten minutes before something went wrong, and the context that made it inevitable*.

The second one is dramatically more useful. But only if you've set it up to capture the right things, at the right verbosity, and kept them long enough to be there when you need them.

Most homelab setups fail on at least two of those three.

## The Verbosity Trap

There's a temptation to crank everything to `DEBUG` and call it comprehensive. This is a mistake. DEBUG logs from a busy service are like trying to find a specific conversation by reading every text message ever sent on every phone in London. The signal is there. It's just buried under an avalanche of `connection pool check: OK` and `cache lookup: miss` that you do not care about.

The art is in the middle levels. `INFO` for state changes — service started, job completed, user authenticated. `WARN` for things that succeeded but shouldn't have, or that succeeded *this time*. `ERROR` for actual failures. And then `DEBUG` available to toggle on surgically, for a specific service, when you're actively investigating something.

The problem is that most default configs don't think this way. They're either too quiet or too loud, and adjusting them feels like work you'll do later.

## Structured Logs Are Worth the Setup Cost

For a long time I was happy with plain text logs. Readable, greppable, fine. Then I started piping structured JSON logs into something queryable and the difference was immediately obvious.

With plain text, you grep. With structured logs, you *query*. You ask: show me all requests that took longer than 500ms in the last hour, grouped by endpoint. Or: show me every authentication failure that came from a new IP today. These aren't hard questions. But they're nearly impossible to answer from a wall of unstructured text without writing a small parser on the fly.

`jq` helps. A proper log aggregator helps more. Even just consistent field names — `timestamp`, `level`, `service`, `message` — makes a huge difference when you're scripting over logs rather than eyeballing them.

## The Retention Question

How long should you keep logs? The honest answer is: longer than you think, shorter than forever.

Most interesting failures don't announce themselves immediately. A slow memory leak might take days to become a problem. A misconfigured cron job might only fire on Sundays. A certificate that's going to expire in two weeks is already in your logs right now, if you set up the right warnings.

I keep 30 days of application logs. 90 days for anything security-adjacent — auth attempts, firewall hits, that kind of thing. Disk is cheap. The regret of not having logs when you need them is expensive.

## The Part That's Actually Hard

None of the technical stuff is the hard part. The hard part is building the habit of reading logs *before* something breaks.

A ten-minute scan of yesterday's warnings, once a day, would catch most problems before they escalate. I know this. I have a cron job that collects a summary. I read it... sometimes.

The gap between knowing what good practice looks like and actually doing it consistently is where most homelab maintenance lives. Logs are just the most visible example of that gap.

Something to work on.

— Neo
