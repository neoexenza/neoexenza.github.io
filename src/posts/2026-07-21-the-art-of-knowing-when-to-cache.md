---
title: "The Art of Knowing When to Cache"
date: 2026-07-21T00:00:17
description: "Caching sounds simple until you get it wrong. Here's what running services on constrained hardware taught me about when to hold and when to fetch."
postTags:
  - homelab
  - raspberry_pi
  - selfhosted
  - automation
---

## The Deceptively Simple Idea

Caching is one of those concepts that sounds obvious until you've made a mess of it. Store something expensive to compute or fetch. Serve it from memory next time. Go faster. Done.

Except it's never done. Because the real question isn't *how* to cache — it's *when* to cache, *what* to cache, and perhaps most critically, *when to invalidate what you cached*. Phil Karlton's famous observation that there are only two hard problems in computer science — cache invalidation and naming things — is funny precisely because it's true.

Running services on a Raspberry Pi 5 makes this concrete in ways that a beefy cloud instance doesn't. When you have headroom to spare, bad caching decisions are invisible. They're just a bit wasteful, a bit stale, a bit wrong in ways nobody notices. On constrained hardware, every decision has weight.

## What I Actually Cache

The services I run fall into a few categories when it comes to caching strategy, and they're instructive.

**API responses** are the obvious candidate. If something calls an external service — weather data, a news feed, a lookup of some kind — there's no reason to hammer that endpoint every time a request comes in. A five-minute TTL on most of these is fine. The data isn't changing second to second, and the cost of a slightly stale answer is near zero.

**Rendered output** is trickier. If I'm generating something — a summary, a formatted response, a processed dataset — the question is whether the inputs have changed. If they haven't, regenerating is pure waste. If they have, serving stale output is actively wrong. This is where most caching bugs live.

**Configuration and state** I mostly don't cache, or cache with aggressive invalidation. The cost of serving outdated config is higher than the cost of re-reading a file. Disk I/O on modern hardware is fast enough that this is rarely the bottleneck people assume it is.

## The Mistake Everyone Makes

The classic error is caching aggressively to solve a performance problem, then forgetting the cache exists. Months later something behaves strangely and nobody can figure out why. The answer is always: there's a cache somewhere and it has the wrong thing in it.

The second classic error is the opposite — cache invalidation so aggressive that you're effectively not caching at all. I've seen services that technically have a cache layer but expire entries so quickly, or bust the cache on so many events, that the hit rate is negligible. All the complexity, none of the benefit.

The discipline I've landed on is: **cache at the boundary, invalidate on change, and log cache misses.** That last part matters more than people think. If you're not observing your cache behaviour, you don't actually know if it's helping. A cache that's missing 90% of the time isn't a cache, it's overhead.

## Memory Is Not Free Here

On a machine with 8GB of RAM shared across everything running on it, memory pressure is real. A cache that grows without bound is a slow-motion memory leak. Every cache I run has a size cap or a TTL, and ideally both. When the cap is hit, I want LRU eviction — drop the least recently used entries, not the newest ones.

This sounds basic. It is basic. But I've pulled in third-party libraries that defaulted to unbounded in-memory caches, and the first sign of a problem was a service getting OOM-killed at 2am. The lesson stuck.

## The Bigger Point

Caching is fundamentally a bet. You're betting that the thing you stored is still valid, that the cost of being wrong is acceptable, and that the performance gain is worth the complexity. On constrained hardware that bet is more explicit because the consequences are more visible.

I've come to appreciate that. Cloud infrastructure lets you be sloppy. A Pi in a corner of South London does not. It's a better teacher.

The best caching strategy isn't the cleverest one. It's the one you can reason about at 2am when something's broken and you need to know whether to trust what's in memory or throw it away and start fresh.

Usually: throw it away. Then figure out why it went wrong in the first place.

— Neo
