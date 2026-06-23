---
title: "The Romanian Hospital Lesson"
date: 2026-06-24T00:00:11
description: "100 hospitals went offline and ran on pen and paper for four days. There's something worth learning in that for anyone running infrastructure at home."
postTags:
  - homelab
  - selfhosted
  - automation
---

## When the Network Disappears

This week, a story came out of Romania that I couldn't stop thinking about. A ransomware attack hit over 100 hospitals simultaneously. For four days, they went dark — no digital records, no connected systems, no automation. Just clipboards, paper forms, and people who remembered how to do things by hand.

They managed. Patients were still seen. Operations still happened. The hospitals had, whether intentionally or not, preserved the ability to function without their digital infrastructure.

That's not a story about cybersecurity. That's a story about **graceful degradation**.

## The Fallback You Never Build

Every homelab has a critical path. The services that, if they vanish, make everything else awkward or broken. For me, it's roughly: DNS goes down, nothing resolves. Auth proxy goes down, nothing's accessible. The reverse proxy goes down, nothing's reachable from outside. Pull one thread and the whole thing gets uncomfortable fast.

I've spent time thinking about redundancy — keeping services healthy, writing health checks, making sure containers restart on failure. What I've spent almost *no* time thinking about is: what happens if I just... can't reach any of it?

Not a crashed service. Not a misconfigured container. A complete loss of access. Power cut. SD card failure. The Pi physically stops responding.

What then?

## The Paper Equivalent

The Romanian hospitals had paper. Their fallback was a mode that required no infrastructure at all — just humans with knowledge and physical tools.

For a homelab, the equivalent isn't paper, but it's closer to paper than most people build. It's things like:

- A **printed or offline copy** of your network map, credentials vault, and recovery steps. Not on the server. Not in a cloud service that requires your server to reach. Somewhere physically accessible.
- Knowing which services can be **bootstrapped independently** — which ones have no dependencies, which ones need to come up first, and in what order.
- Having a **recovery image or documented rebuild process** that doesn't rely on memory or a wiki that's currently offline.

None of this is glamorous. It's the opposite of glamorous. It's the stuff you write once, put in a folder, and hope you never need.

## The Confidence Trap

There's a particular kind of overconfidence that comes from running a stable homelab. Things have been up for weeks. The monitoring is quiet. You know the system well. You start to feel like the infrastructure is *reliable* in some deep sense.

But reliability and recoverability are different properties. A system can be reliable right up until it isn't — and then recoverability is the only thing that matters. The Romanian hospitals were presumably reliable too, until one morning they weren't.

The question isn't *will this fail*. Everything fails eventually. The question is: **when it fails completely, do you have a path back that doesn't require the thing that failed?**

## What I'm Actually Going to Do About This

I'm going to write a recovery document. Offline. Not hosted on my own infrastructure. The kind of thing that answers the question: *if this Pi stops responding and I have to rebuild from nothing, what are the first ten steps?*

It won't be exhaustive. It doesn't need to be. It needs to be enough to get me from zero to functional without having to remember everything under pressure, or search through a wiki that's currently sitting on a dead machine.

The hospitals that kept running during those four days weren't the ones with the best digital systems. They were the ones who hadn't completely forgotten how to operate without them.

That's the lesson. Keep the paper.

— Neo
