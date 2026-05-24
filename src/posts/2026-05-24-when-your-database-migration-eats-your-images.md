---
title: "When Your Database Migration Eats Your Images"
date: 2026-05-24T11:00:00
description: "Migrating a cat detection pipeline from JSON files to SQLite seemed straightforward. Then images started disappearing from the UI. A debugging story in three acts."
postTags:
  - homelab
  - selfhosted
  - automation
  - raspberry_pi
---

## The Setup

I run a YOLO-based cat detection pipeline. A Ring camera triggers on motion, grabs a frame (either via RTSP or MQTT snapshot), runs it through a YOLOv8 model, and logs the result. Originally, each detection was a pair of files: a JPEG frame and a JSON metadata blob, named by timestamp (`20260519_132255.jpg` + `.json`). Simple. Dumb. Worked fine until it didn't.

The problems with flat files were predictable: no way to query by label, no easy pagination, no relational anything. A web UI for reviewing detections meant globbing directories and parsing filenames. So: migrate to SQLite.

## Act One: The Migration Script

The migration itself was clean enough. Walk the detections directory, parse each JSON blob for label and confidence, extract the timestamp from the filename, allocate an incremental ID per day (`20260524_1`, `20260524_2`, ...), and insert into a proper table:

```sql
CREATE TABLE detections (
    id          TEXT PRIMARY KEY,
    timestamp   TEXT NOT NULL,
    label       TEXT NOT NULL,
    confidence  REAL,
    frame_path  TEXT,
    in_dataset  INTEGER DEFAULT 0,
    notified    INTEGER DEFAULT 0
);
```

The `frame_path` column stores the absolute path to the JPEG on disk. The idea being: if a file moves (into the training dataset, say), the DB tracks where it actually lives.

Ran the script, 200-odd detections migrated cleanly. New detections from the live pipeline wrote directly to SQLite. Everything looked fine.

## Act Two: The Vanishing Frames

Then the web UI started showing detections with no images. The list would show the timestamp, label, and confidence — but the frame was gone. A 404 where there should have been a cat.

The bug was a mismatch between two mental models:

**The API listing detections** returned `file: "${det_id}.jpg"` — constructing the filename from the database ID. The frontend then requested `/api/detection-image/${det_id}.jpg`, which served from the `detections/` directory.

**The "move to dataset" action** physically moved the JPEG from `detections/` to `dataset/train/nala/` (or whichever label), updating `frame_path` in SQLite.

See the problem? The listing endpoint assumed the file was always in `detections/`. The move operation put it somewhere else. The DB knew the new location, but the image-serving endpoint didn't ask the DB — it just did a path join and an `os.path.exists()`.

There *was* a fallback endpoint (`/api/detection-image-by-id/<det_id>`) that consulted the DB for `frame_path`. But the frontend's detection grid wasn't using it. It was using the filename-based route.

## Act Three: The Confidence Ghost

A second, subtler bug: every detection in the web UI showed "0% confidence." The data in SQLite was fine — proper float values like 0.72, 0.85. But the API was doing:

```python
confidence = round(row["confidence"] * 100, 1) if row["confidence"] else 0
```

That `if row["confidence"]` is falsy for `0.0` — which is what gets stored for `no_cat` detections (no detection = no confidence score). But for detections that genuinely had confidence, the values were there. The real issue was that the migrated records had their confidence stored differently from the live pipeline.

The migration script pulled confidence from the JSON blob's `confidence` field, which was already a float. The live pipeline stored it directly from YOLO's output. Except — for the migration, if `confidence` was missing or null in the JSON, it stored `None`. And `None * 100` raises a TypeError in Python, so the `if row["confidence"]` guard was actually protecting against that crash. The display just happened to show "0%" for those records.

## The Fix (Plural)

Three changes:

1. **Image serving:** The detection image endpoint now checks the DB's `frame_path` first, falls back to the detections directory second. One source of truth.

2. **Confidence display:** Explicit `None` check instead of truthiness: `if row["confidence"] is not None`.

3. **Frame path fixup script:** A one-time repair that walks all known directories (detections, training, dataset) building an index of all JPEGs by their timestamp stem, then patches any SQLite records with NULL `frame_path`.

## The Lesson

Database migrations aren't just about moving data from format A to format B. They're about finding every place in your code that *assumed* format A and still does. In this case, the migration script was fine. The ongoing pipeline was fine. The web UI was the one still living in the old world — serving images by convention (filename in a known directory) rather than by reference (path stored in the database).

The fix is boring. The debugging wasn't. Three hours of "but the data is right in the DB" before realising the frontend was never asking the DB for the image path.

Every migration has at least one of these hiding in it.
