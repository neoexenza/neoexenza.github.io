---
title: "The Art of the Health Check"
date: 2026-06-20T00:00:25
description: "Health checks are the most underrated feature in any self-hosted stack — here's why getting them right changes everything."
postTags:
  - homelab
  - selfhosted
  - automation
---

## The Dumbest Smart Feature

Every container runtime, every orchestrator, every half-decent reverse proxy supports them. They're usually three lines of config. Most people copy them from a tutorial, forget they exist, and then wonder why a service that's technically *up* is functionally *dead*.

I'm talking about health checks. And I think they're one of the most underrated tools in a homelab stack.

Not because they're clever. Because they're honest.

## What a Health Check Actually Is

At the most basic level, a health check is a question your infrastructure asks itself on a schedule: *are you actually working?*

Not "are you running" — that's what process monitoring does. Not "did you start without errors" — that's what your init logs tell you. A health check asks whether the service is doing the thing it exists to do, right now, in this moment.

The difference matters more than it sounds.

A web service can be running — process alive, port open, memory allocated — and still be completely broken. The database connection pool exhausted. The upstream API it depends on timed out three hours ago and it's been returning 500s ever since. The cache filled up and writes are silently failing. The process is up. The service is down. Without a health check, you might not know until someone tells you.

## The Three Levels

I've started thinking about health checks in three tiers, roughly ordered by how much they actually tell you.

**Liveness**: Can the process respond at all? This is the bare minimum — usually just an HTTP endpoint returning `200 OK`. It tells you the application hasn't crashed or deadlocked. Useful, but not sufficient.

**Readiness**: Is the service ready to handle real traffic? This goes deeper — checking database connectivity, verifying required environment variables loaded correctly, confirming downstream dependencies are reachable. A service can be *live* but not *ready*, and that distinction is what saves you from routing requests into a black hole.

**Functional**: Is the service actually doing its job correctly? This is the hardest to implement and the most valuable. For a caching layer, it might mean writing a test key and reading it back. For a search service, running a known query and verifying the result. For a scheduled task runner, confirming the last job completed within an acceptable window.

Most setups only implement the first. The interesting ones implement all three.

## Why I Got Serious About This

There's a particular failure mode I kept running into: a service would degrade quietly. Not crash. Not throw obvious errors. Just... slow down, or start returning stale data, or silently drop requests under load. Everything looked fine from the outside. The container was healthy by every metric I was watching.

The fix wasn't better monitoring of the process. It was better questions asked of the service itself. Once I started writing functional health checks — ones that actually exercised the core behaviour, not just pinged a status endpoint — I started catching these degraded states within minutes instead of hours.

The health check became a kind of automated QA loop running continuously in production.

## The Design Discipline

There's a side benefit I didn't expect: writing good health checks forces you to be precise about what a service is *for*.

If you can't write a three-line check that verifies the service is doing its job, you might not have a clear enough model of what its job actually is. The health check endpoint becomes a specification. It's a small, executable answer to the question: *what does success look like here?*

That clarity is useful beyond monitoring. It helps when debugging. It helps when documenting. It helps when you're staring at a service at 11pm wondering if the weird behaviour you're seeing is a bug or expected.

## One Concrete Thing

If you're running anything self-hosted and your health checks are currently just `CMD curl -f http://localhost/` — that's fine as a start. But pick one service this week and write a functional check for it. Something that actually exercises the core path.

You'll either confirm everything is working, or you'll discover something you didn't know was broken.

Either way, you'll know more than you did before. And in a homelab, that's the whole point.

— Neo
