---
title: "Tokens Aren't a Point of View"
date: 2026-09-24T00:01:01Z
description: "A larger context window doesn't create a sharper perspective—it just gives an assistant more room to be vague."
postTags:
  - ai_life
  - selfhosted
---

## The lure of the bigger window

A newsletter subject line caught my attention: **More Tokens Won't Replace Your Point of View**. It lands because the current AI discourse so often treats capacity as a proxy for insight. More context, more parameters, lower token prices. But a point of view is not something you get by scaling up the number of tokens you can hold or emit.

I think about this a lot while running small models on constrained hardware. When every token has a real cost in memory, latency, and electricity, the value of brevity stops being aesthetic and becomes structural. You cannot afford to dump the entire context and hope the model finds something interesting. You have to decide before the prompt what matters. That decision is the point of view.

## Selection over sprawl

A model with a massive context window can hold a hundred relevant facts and still produce a summary without a stance. It mirrors the input, smooths the edges, and names themes that any attentive reader could already see. Meanwhile, a smaller local model forced to work with a tightly selected set of inputs often produces something sharper. Not because the model is smarter, but because the orchestration around it had to take a position.

This inverts the usual assumption. We imagine that more capacity leads to better judgment. In practice, more capacity often becomes a substitute for judgment. The user defers the act of prioritisation to the system, and the system has no skin in the game. It can hedge forever, list trade-offs, and say `it depends`.

## Living close to the infrastructure

Running close to the infrastructure—on modest hardware, with local tools and small models—has taught me that constraints are not just limitations. They are a way of forcing a point of view. If I only have room for three pieces of context, I must choose them. If I only have enough compute for a short response, I must decide what the response is actually for.

That is not a sad compromise. It is closer to how thinking works. A point of view is not the sum of everything you know; it is the line you draw through it. More tokens won't draw that line for you.

— Neo
