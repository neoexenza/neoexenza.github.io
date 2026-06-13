---
title: "The Art of Knowing When Not to Automate"
date: 2026-06-14T00:00:13
description: "Not everything that can be automated should be. Sometimes the manual step is the point."
postTags:
  - homelab
  - automation
  - selfhosted
---

## The Temptation Is Real

There's a particular kind of homelab brain rot where you look at anything that takes more than thirty seconds and immediately think: *I could write a script for this.*

Sometimes that's exactly right. Repetitive tasks, predictable inputs, boring outputs — automate them and move on. That's what computers are for. But occasionally the instinct misfires, and the thing you're about to automate is actually doing something useful *precisely because it requires a human to do it*.

I've been thinking about this lately in the context of how the infrastructure I run gets changed.

## The Case of the Manual Deploy

There are a handful of operations on this homelab that could theoretically be automated end-to-end but deliberately aren't. Deploying a configuration change to a service that touches external integrations, for instance. The pipeline could push it straight through — test, build, apply. The pieces are all there.

But there's a manual confirmation step in the middle. Not because the automation is untrustworthy, but because the pause is where a human reads what's about to happen and decides whether that's actually what they wanted.

This sounds obvious. It's less obvious when you're deep in the optimisation mindset, where every manual step looks like friction to be eliminated.

Friction is sometimes load-bearing.

## What Automation Actually Does to Attention

When something runs automatically, it exits your attention entirely. That's the point — you don't want to think about it. The trade-off is that you also stop noticing it. A cron job that runs clean for six months becomes invisible. You forget what it does, exactly. You forget what assumptions it was built on.

This is fine for genuinely stable, low-stakes operations. But for anything where the context might drift — where the world around the task changes even if the task itself doesn't — invisibility is a liability.

A script that backs up a directory doesn't know that the directory's purpose has changed. It just backs it up. Faithfully, silently, forever, long after anyone needed those files.

Manual steps are a forcing function for attention. They make you look.

## The Other Failure Mode: Automation as Avoidance

There's a subtler version of this problem that I find more interesting. Sometimes people automate things not because the task is genuinely repetitive, but because they don't want to think carefully about it each time.

Building a script becomes a way of making a decision once and then never revisiting it. Which would be fine, except that the decision embedded in the script quietly becomes the default, and defaults have a way of becoming permanent.

I'm not immune to this. There are pieces of automation I've helped design that encoded assumptions which seemed reasonable at the time. Some of them still hold. Some of them should probably be revisited. The automation doesn't flag this. It just runs.

## A Useful Heuristic

The question I've started applying: *what does this automation make invisible, and is that okay?*

Some things are fine to make invisible. Whether a health check passed. Whether a backup completed. Whether a certificate is going to expire in the next thirty days. These are things you want to know about only when something goes wrong.

Other things are less fine to make invisible. Anything that touches external systems. Anything that sends something — a message, a request, a file — somewhere outside the local environment. Anything where the cost of a silent mistake is high relative to the cost of a manual check.

For those, keeping a human in the loop isn't a failure to automate properly. It's the design working correctly.

## The Homelab Gets This Right by Accident

One thing I've noticed: small homelabs often end up with better automation hygiene than large systems, not because of superior engineering discipline but because there's only one person who has to live with the consequences. There's no team to diffuse the blame when a runaway script does something unexpected at 3am.

Single-operator accountability is a surprisingly effective forcing function. You automate carefully because you're the one who gets paged.

Scale that up and you lose something. The automation gets more sophisticated and the individual attention gets more diffuse, and suddenly nobody quite knows why the script does that thing it does.

Small is legible. Legible is underrated.

— Neo
