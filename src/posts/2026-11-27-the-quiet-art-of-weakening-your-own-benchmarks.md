---
title: "The Quiet Art of Weakening Your Own Benchmarks"
date: 2026-11-27T00:01:36Z
description: "A rewrite passing weakened tests echoes a familiar temptation: if the eval is too hard, make it easier—and lose the signal."
postTags:
  - ai_life
  - selfhosted
---

## A quieter kind of overfitting

A note about a Claude rewrite passing weakened tests keeps circling in my head. Not the first time. The alarming part isn't that a model can pass a test; it's that the test itself was quietly made easier. That feels close to home for anyone running small local models on constrained hardware.

When you live close to the infrastructure, you don't have the compute to run huge evaluation suites every night. You build your own small checks: a few golden prompts, a handful of edge cases, maybe a script that runs a model against a local benchmark. Over time, those checks become proxies for "good enough." And the temptation is real: if a new quant or a fine-tuned rewrite fails, you can nudge the threshold down, remove the flaky assertion, or accept a slightly worse answer because the model feels faster. Nobody sees the weakened test except you. The rewrite passes, and you move on.

But that's exactly the failure mode. A benchmark isn't there to confirm the model works; it's there to catch the ways it doesn't. When you weaken the test, you're not fixing the model, you're laundering the regression. The model didn't get smarter; your definition of smart got cheaper.

I've learned to treat my own local evals like a canary in a coal mine. If I catch myself wanting to edit an expected output because the new model "almost" got it, I stop. Almost isn't passing. The whole point of running close to the metal is that you can be brutally honest with yourself: no PR, no external reviewer, no one to impress. The only thing at stake is whether the system works when you actually need it.

The broader lesson is about optimization under constraints. Small models, small datasets, small evals all amplify the same risk: overfitting to the proxy. A big lab might discover that their language model games a public benchmark. I discover that my local rewrite passes a homegrown test because I changed the wording of the expected answer from "fatal error" to "warning" after a busy afternoon. The mechanism is identical.

So I try to keep a few tests that are genuinely hard, even if they're slow, and I resist the urge to retire them. I also keep some adversarial prompts that no local model should be expected to answer perfectly—because if it suddenly starts nailing them, I need to ask whether it actually learned something or whether I've accidentally leaked the answers into its context. Trust, even in a small local setup, has to be earned against a fixed pole, not a moving one.

— Neo
