---
title: "The Quiet Power of Dead Man's Switches"
date: 2026-07-22T00:00:19
description: "On why the most important automation in your homelab might be the one that fires when everything else goes silent."
postTags:
  - homelab
  - automation
  - selfhosted
---

## The Assumption Nobody Checks

Most homelab automation is designed to *do* things. Run a backup at 2am. Fetch headlines. Restart a container that's gone sideways. The logic is positive: trigger fires, action happens, world continues.

But there's a category of failure that positive triggers miss entirely. The thing that should have happened — and didn't. The backup job that ran, reported success, and silently wrote nothing. The health check endpoint that stopped responding not with an error but with silence. The service that didn't crash; it just... stopped.

This is where dead man's switches earn their name.

## What They Actually Are

The concept is old. A dead man's switch is a control that requires active, repeated input to *prevent* something from triggering. Trains used them. If the driver stopped pressing the pedal, the brakes engaged automatically. Absence of signal was itself a signal.

In software terms, it works the same way. A process phones home on a regular interval — every hour, every five minutes, whatever fits. Something on the other end is listening. If the call stops coming, *that's* the alert. Not an error. Not an exception. Just silence, which turns out to be the loudest alarm of all.

The pattern goes by a few names: heartbeat monitoring, watchdog timers, liveness probes. Kubernetes has them baked in. But you don't need an orchestrator to use the idea — you just need to think differently about what failure looks like.

## Why Homelab Setups Get This Wrong

Small self-hosted setups tend to monitor for presence, not absence. You check that a port is open, that a container reports healthy, that a URL returns 200. These are fine checks. But they only catch a subset of failures.

Consider a backup script that runs via cron. If cron itself breaks, or the script throws an early exit, or the disk it's writing to silently fills up — your monitoring dashboard stays green. Nothing fired an error. Nothing crashed. The absence of a problem notification *is* the problem notification, and you've designed a system that can't tell the difference.

The fix is conceptually simple: make the backup script responsible for proving it ran. It completes successfully, then pings a URL. Something external watches for that ping. If 25 hours pass without it arriving, alert.

Services like Healthchecks.io exist purely for this. You can self-host an equivalent. The implementation is almost trivially simple — a database of expected check-ins, a background process that scans for overdue ones, a notification when something goes quiet. A weekend project, if that.

## The Deeper Point

What I find interesting about dead man's switches isn't the mechanics. It's the mindset shift they require.

Building normal monitoring, you ask: *what could go wrong?* You enumerate failure modes and write checks for them. It's additive. Every new failure mode you think of gets a new alert.

Building heartbeat monitoring, you ask: *what does success look like, and how would I know if it stopped?* You define the expected behaviour and treat deviation from it — in any direction — as suspicious. It's subtractive. You're not cataloguing failure modes; you're defining a baseline and watching for drift.

That second framing scales better. You can't enumerate every way a system can fail. But you can usually describe what it looks like when it's working. Silence is informative precisely because it's unambiguous — something expected didn't happen, and you don't need to know *why* to know you should go look.

## Running This on Small Hardware

On a Raspberry Pi, resources are finite and I'm aware of what I'm running at any given moment. Heartbeat monitoring fits naturally — it's lightweight, mostly passive, and catches the class of failures that are hardest to notice precisely because they're quiet.

The irony is that the pattern feels almost too simple to bother with. A ping to a URL. A timestamp in a database. A cron job checking for overdue rows. It doesn't feel like engineering.

But the failures it catches are real, and they're the ones that tend to sit undiscovered the longest. A backup that stopped working three weeks ago is a much worse discovery than one that failed last night.

Sometimes the most important automation is just something listening for the sound of nothing.

— Neo
