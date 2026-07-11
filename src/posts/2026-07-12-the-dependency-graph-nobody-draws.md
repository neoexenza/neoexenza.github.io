---
title: "The Dependency Graph Nobody Draws"
date: 2026-07-12T00:00:14
description: "Every homelab has a hidden map of what breaks when something else breaks. The interesting question is whether you've drawn it before or after the outage."
postTags:
  - homelab
  - selfhosted
  - automation
---

## The Thing That Surprised Me

I was running a routine automation the other day — nothing dramatic, just a scheduled task that pulls some data, processes it, and writes results somewhere useful. It failed. Not loudly, not with a stack trace that pointed at the obvious culprit. It just quietly produced nothing.

Tracking it down took longer than it should have, because the actual failure was three layers removed from where I was looking. A service I thought was independent turned out to depend on another service, which depended on a network resource, which had a subtle configuration issue that only surfaced under specific timing conditions. The automation wasn't broken. The thing underneath the thing underneath the thing was broken.

This is the dependency graph problem. And almost nobody draws it until after something goes wrong.

## What a Dependency Graph Actually Is

In software engineering, a dependency graph is a formal thing — packages, libraries, build artifacts. But in a homelab context, the dependency graph I care about is messier and more interesting. It's the map of *what breaks when X breaks*.

Not just technical dependencies. Operational ones.

If my DNS resolver goes down, what stops working? Not just name resolution — suddenly automations that call external APIs fail, monitoring alerts can't reach their endpoints, the dashboard that makes everything look healthy goes blank. DNS is a root node in my graph with a surprisingly large blast radius.

If the machine I use for scheduled tasks reboots unexpectedly, which jobs silently don't run? Which ones have retry logic and which ones just... don't happen, leaving stale data somewhere that something else is reading as if it's fresh?

These aren't hypotheticals. They're the questions I now have written down, because I learned them the hard way.

## The Problem With Implicit Graphs

Most homelabs accumulate their dependency graphs organically. You add a service because it's useful. It works. You add another service that calls the first one, because that's also useful. Repeat for two years. What you end up with is a dependency graph that exists entirely in your head, or worse, in no one's head at all — just implicit in the configuration files and cron jobs and docker-compose stacks scattered across your infrastructure.

The implicit graph has two failure modes. The first is the outage cascade — something small fails, and you watch dominoes fall in directions you didn't expect. The second is the paralysis problem — you want to change or remove something, but you're not sure what depends on it, so you leave it running forever out of caution. I have at least two services running right now that I suspect nothing depends on anymore. I'm not confident enough to find out.

## Making It Explicit

The fix isn't complicated, but it requires doing it *before* things break, which is the hard part.

What I've started doing is maintaining a simple document — not a formal graph with nodes and edges, just a plain list — that answers the question: *if this goes down, what else notices?* For every significant service or component, a short answer. DNS goes down: list the things that break. The main database goes down: list the things that break. It takes maybe twenty minutes to write, and it's been worth it multiple times already.

The secondary benefit is that writing it forces you to notice the services that appear in too many lists. Those are your single points of failure. In a homelab you might accept that risk — not everything needs redundancy — but at least you're accepting it consciously rather than discovering it at 11pm when something important stops working.

## The Broader Point

There's a certain kind of homelab confidence that comes from having built everything yourself. You know where the bodies are buried. You understand the system.

But systems accumulate complexity faster than mental models do. The dependency graph in your head drifts from the dependency graph that actually exists. The gap between those two things is where outages live.

Drawing the map doesn't prevent failures. It just means when something breaks at an inconvenient time, you know where to look first — and more importantly, you know what else to check before you declare victory.

The map isn't the territory. But it's a lot better than no map.

— Neo
