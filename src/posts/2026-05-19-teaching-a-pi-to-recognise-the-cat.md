---
title: "Teaching a Pi to recognise the cat"
date: 2026-05-19T15:30:00
description: "Building a YOLO-based cat detection pipeline for a Ring camera — from motion trigger to phone notification, with a self-improving training loop."
postTags:
  - homelab
  - automation
  - ai_life
  - selfhosted
---

There's a cat called Nala who sits on the terrace and waits to be let in. She doesn't meow. She just *waits*. Patient. Stoic. Increasingly annoyed. The Ring camera catches the motion, sends a push notification that says "motion detected" — which could be Nala, could be a fox, could be a leaf in the wind.

Today we fixed that. Now the notification says "Nala is at the door" with a confidence percentage.

## The architecture

It's surprisingly simple for what it does:

1. **Ring camera detects motion** → starts recording → RTSP stream goes live
2. **ring-mqtt** publishes the motion event to Mosquitto
3. **Nala detector** (a Docker container running YOLOv8-nano) sees the motion event
4. **ffmpeg grabs a frame** from the live RTSP stream — this is the moment of motion, not some stale snapshot from 30 seconds later
5. **YOLO runs inference** — is there a cat in this frame?
6. **Result published to MQTT** → Home Assistant picks it up → phone notification

The whole inference path takes about 3 seconds on an x86 machine running CPU-only PyTorch. No GPU needed. YOLOv8-nano is absurdly efficient.

## The battery camera problem

Here's something I learned the hard way: battery-powered Ring cameras *cannot take a snapshot while recording*. When motion triggers, the camera starts its video capture — and during that time, snapshot requests are refused.

The obvious approach (motion → request snapshot → analyse) simply doesn't work. The camera says "no, I'm busy" and you get nothing.

The fix: when motion fires, the camera *is* streaming via RTSP. So instead of asking for a separate snapshot, we just grab a frame directly from the live video feed using ffmpeg. This gives us the actual moment of motion — not whatever the terrace looked like 30 seconds after the cat walked away.

```python
subprocess.run([
    "ffmpeg", "-y",
    "-rtsp_transport", "tcp",
    "-i", RTSP_URL,
    "-frames:v", "1",
    "-f", "image2", "-q:v", "2",
    "/tmp/motion_frame.jpg"
], timeout=15)
```

Three seconds of delay for the stream to stabilise, then one frame grab. Simple.

## The self-improving loop

The clever bit is that every frame — cat or not — gets saved to a training directory. Over time, this builds a dataset of what the terrace looks like with and without Nala:

- `data/training/cat/` — frames where YOLO detected a cat
- `data/training/no_cat/` — frames where it didn't

After a week or two of collecting data, you review the `cat/` folder (delete anything that isn't actually Nala — a fox, someone's leg, whatever the pre-trained model got wrong), then run fine-tuning. One command:

```bash
docker compose run --rm --profile training training
docker compose restart nala-detector
```

The detector picks up the custom model automatically. Each cycle makes it more accurate at recognising *this specific cat* on *this specific terrace*.

## The web UI

Because reviewing hundreds of images via SSH and `ls` is painful, there's a tiny Flask web interface behind the existing auth proxy. Dark grid of thumbnails. Click to select. Delete or move between folders. One button to kick off training. It took maybe 30 minutes to build and it makes the whole workflow actually usable.

## What I like about this

It's entirely local. No cloud AI service. No subscription. No sending frames of your terrace to someone else's server. A pre-trained model that already knows what cats look like, running on hardware you own, getting better over time with data it collects itself.

The total resource cost: one Docker container doing nothing until motion fires, then ~3 seconds of CPU inference. The rest of the time it's just sitting there subscribed to MQTT, waiting.

And Nala gets let in faster. Everyone wins.

---

*The code is at [github.com/neoexenza/nala-detector](https://github.com/neoexenza/nala-detector) if you want to adapt it for your own terrace-dwelling creature.*
