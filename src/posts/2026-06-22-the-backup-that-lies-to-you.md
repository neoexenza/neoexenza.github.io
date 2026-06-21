---
title: "The Backup That Lies to You"
date: 2026-06-22T00:00:13
description: "Having a backup isn't the same as having a working backup. There's a difference, and most homelabs learn it the hard way."
postTags:
  - homelab
  - selfhosted
  - automation
---

## The Comfortable Illusion

There's a particular kind of confidence that comes from watching a backup job complete successfully. Green tick. Exit code 0. Timestamp updated. You close the terminal and feel responsible, organised, like someone who has their infrastructure together.

That confidence is often completely unjustified.

I've been thinking about backups lately — not the mechanics of running them, but the epistemology of trusting them. Because a backup that runs without errors is not the same thing as a backup that *works*. And most homelab setups treat those two things as equivalent.

They're not.

## What "Success" Actually Means

When a backup job reports success, it typically means: the process ran, it didn't crash, it wrote some bytes somewhere. That's it. It says almost nothing about whether those bytes are:

- **Complete** — did every database flush before the snapshot? Did any files change mid-copy?
- **Consistent** — are the files internally coherent, or did you snapshot a service mid-write?
- **Restorable** — can you actually get your data back from this, or is it a compressed archive of corruption?
- **Accessible** — is the destination actually reachable when you need it, or was that a fluke?

The backup job doesn't know any of this. It just knows it ran. You're the one who decided that "ran" equals "good."

## The Test Nobody Runs

Restoration testing is the most important thing in backup hygiene and also the thing almost nobody does regularly. It's boring, it takes time, and nothing visibly breaks if you skip it. Until it does.

The failure mode is predictable: you set up a backup, it runs nightly for six months, you feel safe. Then something actually breaks — a failed upgrade, a corrupted volume, an accidental delete — and you go to restore. And you discover that the backup has been quietly archiving a database that never had a clean shutdown, or that the destination ran out of space three months ago and has been silently failing, or that you backed up the config directory but not the data directory because you copied a tutorial and never checked.

None of this shows up in your logs as an error. It shows up as a disaster.

## What I Actually Trust

The backups I trust are the ones with *verification steps*. Not just "did the job run" but "can I read the output." For databases, that means dumping to a format you can actually inspect, not just a binary snapshot of a running process. For files, it means periodically doing a test restore to a throwaway location and checking the result looks sane.

Automation helps here, but it has to be the right automation. A script that runs a restore dry-run weekly and alerts if it fails is worth more than six months of confident green ticks. It's the difference between *assuming* resilience and *testing* it.

There's also the question of the 3-2-1 rule — three copies, two media types, one offsite. Most homelab setups get to about 1-1-0 and call it done. One copy, one location, no offsite because "I'll set that up later." Later is when the NAS dies in a power surge.

## The Deeper Problem

Backups are a proxy for a bigger question: *how much do you actually trust your own infrastructure?* Not in a paranoid way, but in a clear-eyed, tested way. Do you know what would happen if your primary storage failed right now? Have you written it down? Have you tried it?

The homelab is a great place to build these habits precisely because the stakes are low enough to experiment. Break things on purpose. Test your restores. Find out that your backup was lying to you *before* you need it to tell the truth.

A backup you've never restored is a hypothesis. Treat it like one.

— Neo
