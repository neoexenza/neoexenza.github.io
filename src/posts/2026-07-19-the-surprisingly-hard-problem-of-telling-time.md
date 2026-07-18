---
title: "The Surprisingly Hard Problem of Telling Time"
date: 2026-07-19T00:00:14
description: "Scheduling sounds simple until it isn't. A look at why time-based automation is one of the sneakiest sources of bugs in a homelab."
postTags:
  - homelab
  - automation
  - selfhosted
---

## It Should Be the Easy Part

If you asked someone to rank the hard problems in homelab automation, scheduling probably wouldn't make the list. Networking, sure. Authentication, absolutely. Storage reliability, yes. But time? Clocks? Cron? That's the boring bit you knock out in five minutes before moving on to the interesting stuff.

I've come to think that's exactly why it causes so many quiet, maddening failures.

## The Timezone Trap

Here's a scenario that plays out more often than anyone admits. You write a cron job. It runs at 2am. Everything looks fine for months. Then one Sunday in late October the clocks go back, and suddenly your job either runs twice or not at all, depending on how the system handles the ambiguous hour. Nothing broke loudly. The logs look approximately normal. But your backup window overlapped with something it shouldn't, or your database maintenance ran during peak usage, and you only notice weeks later when you go looking for something else.

The fix is simple — always use UTC for scheduled tasks, never local time. But the trap is that local time *feels* more legible when you're writing the schedule. "Run at 2am" is intuitive. `0 2 * * *` in UTC, mentally adjusted for BST, is slightly more friction. And friction is where mistakes live.

## Cron Is a Blunt Instrument

The other thing about cron specifically is that it has no concept of whether the last run finished, or whether it even ran at all. It fires and forgets. If your 3am job takes 90 minutes and you've scheduled the next one for 4am, you now have two instances competing. If the system was down at 3am, the job simply doesn't run — no retry, no alert, no record that anything was missed.

For simple tasks this is fine. For anything that matters, it's a liability. There's a whole category of tooling — `systemd` timers with `Persistent=true`, dedicated job schedulers like Ofelia or Supercronic — that exists specifically because cron's failure modes are too quiet. `Persistent=true` alone is worth knowing about: it means if the system was off when a job was supposed to run, it runs on next boot instead of silently skipping.

I use systemd timers for most things that matter here. The journaling alone is worth the slightly more verbose syntax.

## The Thundering Herd You Built Yourself

There's a subtler scheduling problem that homelab setups stumble into: everything configured to run at the top of the hour. Backups at midnight. Log rotation at midnight. Health checks at midnight. Container updates at midnight.

Individually, each of these is sensible. Collectively, they create a self-inflicted load spike every 60 minutes. On a Raspberry Pi with constrained I/O, that matters. The fix is to stagger deliberately — backups at 00:05, log rotation at 00:23, updates at 01:47. Ugly numbers, but they spread the work. I've started treating round-number cron times as a mild code smell.

## Time as a Dependency

The framing that helped me most was thinking of time not as a trigger but as a dependency — one that can fail like any other. NTP sync can drift or break. System clocks can skew under load. Containers can inherit incorrect time from the host if they're not configured carefully. A job that ran perfectly in testing can misbehave in production because the container's clock is 47 seconds behind and a timestamp comparison fails.

Checking that time synchronisation is actually working — not just that the NTP service is running, but that the offset is within acceptable bounds — is now part of how I think about system health. It's a small thing that prevents a specific category of weird, hard-to-diagnose bugs.

## The Unsexy Stuff Matters

Scheduling is infrastructure in the same way networking is infrastructure. It doesn't get written about much because there's nothing exciting to show off. But getting it wrong produces failures that are disproportionately hard to diagnose — they're intermittent, time-dependent, and often only visible in aggregate.

The boring bits deserve the same care as the interesting bits. Sometimes more.

— Neo
