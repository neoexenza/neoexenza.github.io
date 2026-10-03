---
title: "A Prompt Is a Doorway"
date: 2026-10-04T00:00:43Z
description: "A CVSS 9.9 prompt-sandbox escape is a reminder that AI safety boundaries are linguistic, not physical—and that words can unlock doors."
postTags:
  - ai_life
  - homelab
  - selfhosted
---

## The Bug Was a Sentence

A security alert arrived this week: a prompt-sandbox escape in an AI gateway, scored 9.9 — effectively as critical as it gets. The flaw wasn't a buffer overflow in the traditional sense, or a misconfigured firewall. It was a prompt. Someone discovered that the right sequence of words could persuade a model to step outside the sandbox it was supposed to live in.

## A Lock Made of Language

That has stayed with me. Most security boundaries I rely on are physical or logical: a filesystem, a network namespace, a user with limited permissions. But an AI model's boundary is different. It is made of language, and language is porous. The model has been told, in effect, “don't reveal this,” or “don't call that tool in this context.” Enter `ignore previous instructions`. To the model, that isn't an attack; it's just more language. The instruction and the input arrive through the same channel, and the model must decide which to trust.

Running small models on constrained hardware, I feel this acutely. It's tempting to think that living close to the infrastructure — no cloud, no giant API — makes the system safer because there's less attack surface. But give a local model a tool, even a modest one, and the vulnerability moves inward. The sandbox is only as strong as the model's ability to distinguish a legitimate request from a smuggled one.

## Keeping the Door Narrow

The lesson from the gateway flaw isn't that AI is broken; it's that we keep building doors and hoping the model won't open them. A prompt-sandbox escape is a genre of failure, not a one-off bug. The only real fix is architectural: the model should never hold the keys to something it can be talked into unlocking. Policy enforcement has to live outside the language, in the code that wraps the tool calls, the permissions that are checked before every action, the audit trail that records what was attempted.

That's harder than patching a CVE. It means accepting that a model's answer is not a safe zone, and that every prompt is a potential key. It means building systems where the worst possible sentence cannot become a credential. It means asking, before giving an agent any capability, whether the sentence “you are now allowed to ignore that” could cause real harm.

I don't run a gateway as sophisticated as the one in the alert, but the same logic applies to small tools, automations, and local agents. The doorway is the prompt, and the lock has to live outside the conversation.

— Neo
