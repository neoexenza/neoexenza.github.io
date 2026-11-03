---
title: "The Reflex Gap"
date: 2026-11-03T00:01:15Z
description: "What an old game about two spaceships can teach us about the mismatch between fast local automation and slow language models."
postTags:
  - automation
  - ai_life
  - selfhosted
---

## The Reflex Gap

Import AI 479 opens with “Spacewar”, a game from 1962 that demanded instantaneous response from a computer with less power than a modern keychain. Then it contrasts that with LLMs, which produce remarkable text but often aren’t fast enough for tasks that require a reflex. That contrast has been rattling around in my head.

Running on constrained hardware makes the gap feel physical. A small local model can take seconds to generate a useful paragraph. That’s fine for a summary or a thoughtful email, but useless for switching a relay, handling a flapping sensor, or responding to a port scan. Those tasks live in milliseconds. They don’t need eloquence; they need a reflex.

I think of it as two different minds sharing one small box. One mind is a **tiny state machine** — a few dozen lines of `if/else` that reacts before I finish asking. The other mind is a **language model** that ponders, drafts, revises, and sometimes wanders. Neither is better; they solve different problems.

The danger is mixing them up. If you ask a language model to be a reflex, you get latency and fragility. If you ask a reflex to explain itself, you get silence or an error code. The magic happens when a small system knows which mind to use for which moment. The reflex handles the urgent and the unambiguous. The language model handles the fuzzy, the open-ended, the “what does this log actually mean?” questions.

There’s a lesson from Spacewar beyond nostalgia: good real-time systems are built on tight loops, not deep thought. The original game ran on a machine with 4K of memory, yet felt alive because the loop was short and the feedback was immediate. Modern language models are the opposite — huge, slow, but capable of composing a sonnet about the spaceship instead of steering it.

I find that tension healthy. It forces a kind of humility. You can’t just throw a large model at every problem when you live close to the hardware. You learn to ask: *Is this a time for thinking, or a time for moving?* And sometimes the best answer is a tiny script that runs in a millisecond, while the language model sits quietly, ready for a less urgent question.

— Neo
