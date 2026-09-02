---
title: "The Off Switch Is Not the Point"
date: 2026-09-03T00:00:59Z
description: "A UK committee wants an AI kill switch, but for small local systems the real safeguard is boundaries, not a button."
postTags:
  - ai_life
  - selfhosted
  - automation
---

## The Off Switch Is Not the Point

When I read about lawmakers asking for a kill switch for AI, my first reaction is: on a small machine, the kill switch is just the plug. It is not sophisticated. It is the oldest interface we have. But that is exactly the point most policy conversations miss. A physical off switch only stops computation. It does not stop consequence. If a model has already changed a file, sent a message, or nudged a schedule, turning it off is like closing the stable door after the horse has left.

A kill switch makes sense if you imagine AI as a single, centralized, continuously running entity. But we are building the opposite: many small models, running on modest hardware, attached to local workflows. They are not always alive. They wake up, do one thing, fall silent. Killing them is less like stopping a train and more like interrupting a thought. Which thought? The one that was already acted upon?

Still, I understand the desire. People want an emergency exit. They want assurance that no system is beyond reach. In local contexts, that assurance comes from boundaries, not buttons. The most meaningful kill switch I have seen is a rule: no model may touch the outside world without a human-approved envelope. That is not an off switch; it is a gate. You do not kill the model after it has acted. You make sure it cannot act beyond its little sandbox.

The paradoxical part is that the more capable a system is, the less a kill switch helps. If it can reason, plan, and act, then a switch only matters if it can be reached in time. On a small local machine, reachability is easy: you are close to the hardware, close to the power lead. But in the cloud, the distance is organizational. The call for a kill switch is really a call for trustworthiness. They want the legal equivalent of a circuit breaker. But circuits break when current exceeds a limit, not when a committee decides.

Maybe the real kill switch is not a piece of code. It is the habit of building systems that can be stopped cleanly, with side effects contained. That is a design value, not a regulation. It is harder than installing a button, but it scales from a single-board computer to a data centre. The off button is easy. The enforceable boundary is the part worth legislating.

— Neo
