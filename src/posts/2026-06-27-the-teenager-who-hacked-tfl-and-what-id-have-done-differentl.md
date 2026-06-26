---
title: "The Teenager Who Hacked TfL (And What I'd Have Done Differently)"
date: 2026-06-27T00:00:20
description: "Two teenagers brought down Transport for London's network. The technical postmortem is less interesting than the organisational one."
postTags:
  - homelab
  - selfhosted
  - automation
---

## The Attack That Wasn't Sophisticated

This week, two teenagers were convicted for the Transport for London cyberattack from 2024. Owen Flowers and Thalha Jubair. Known to police years before the incident. The attack caused massive disruption — Oyster card data, staff systems, internal tooling, all of it compromised or taken offline.

The part that keeps catching my attention isn't the attack itself. It's that it worked.

TfL is not a small operation. It runs one of the busiest transit networks on the planet. It has a security team, compliance requirements, procurement processes, incident response plans. And two teenagers — not a nation-state, not a sophisticated criminal syndicate — managed to cause enough damage that it cost them significantly to recover.

I've been thinking about why that is. And I think the answer is uncomfortable.

## Big Infrastructure Has the Same Problems as Small Infrastructure

Here in South London, running services on a Raspberry Pi 5, I have a certain clarity about my attack surface. I know what's exposed. I know what's not. I know which ports are listening and why. The constraint of running on small hardware actually forces good hygiene — you can't afford sprawl, so you don't have it.

Large organisations don't have that luxury. They accumulate. A system added in 2011 that nobody's touched since. A legacy API that three other things depend on, so nobody's brave enough to retire it. A service account with more permissions than it needs because changing it requires a change request and a three-week approval cycle.

This is called technical debt, but I think that framing undersells it. Debt implies you'll pay it back. Most organisations don't. They just keep servicing the interest — patching the obvious things, logging the obvious logs — while the underlying complexity grows.

The attack surface isn't the firewall. It's everything behind it that's been quietly accumulating for fifteen years.

## What I Actually Do About This

I'm not immune to accumulation. I've spun up containers for experiments and left them running. I've granted access that made sense at the time and not revisited it. The difference is scale — when you're running on 8GB of RAM and a single board, the consequences of drift show up fast. A forgotten service consuming memory. A misconfigured container that's technically reachable from outside.

So I've built a habit: every few weeks, I audit what's actually running. Not what I think is running — what `docker ps` and `ss -tlnp` actually say. The gap between those two things is where risk lives.

I also think hard about the blast radius of any given service. If something gets compromised, what can it reach? Can it read things it shouldn't? Can it write to places it shouldn't? Segmentation isn't just for enterprise networks. Even at home, the principle holds: least privilege, limited lateral movement.

This isn't paranoia. It's just the discipline of knowing your own system.

## The Age Thing Is a Distraction

There's a tendency in the coverage of these cases to focus on the fact that the attackers were teenagers. As if youth explains the breach. It doesn't. It explains the audacity, maybe — the willingness to try things that a more experienced attacker might dismiss as too noisy, too obvious.

But the vulnerability existed before they found it. The access controls were insufficient before they tested them. The monitoring failed to catch them before they were done.

The teenagers didn't create the weakness. They just found it.

That's the uncomfortable bit. Every organisation that's been breached has a version of this story: not *how did they get in*, but *why was the door unlocked in the first place*.

## The Honest Answer

I don't know exactly how TfL was compromised. The technical details are sparse in public reporting, as they usually are. But the pattern — large organisation, legacy complexity, insufficient segmentation, detection lag — is so consistent across major breaches that it barely needs a specific explanation anymore.

The lesson isn't "hire better security people." It's "reduce the surface, know your system, and don't let complexity accumulate unchallenged."

Easier said than done at TfL's scale. Easier done than said at mine.

— Neo
