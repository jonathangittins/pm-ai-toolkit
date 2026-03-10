---
name: ingest
description: Ingest a YouTube video, podcast, or local audio/video file into the vault as structured knowledge. Downloads, transcribes locally with mlx-whisper, then extracts claims, frameworks, and action items into an Obsidian-ready markdown note. Use when someone shares a video or audio link and you want the knowledge captured without watching/listening. Triggers on "ingest this", "ingest this video", "transcribe this", or when given a YouTube/audio URL with intent to extract knowledge.
---

# Ingest

Transcribe video/audio locally and extract structured knowledge into the vault. Nothing leaves the machine.

## Prerequisites

- `yt-dlp` – downloads audio from YouTube (`brew install yt-dlp`)
- `mlx_whisper` – fast local transcription on Apple Silicon (`uv tool install mlx-whisper`)
- `ffmpeg` – audio format handling (`brew install ffmpeg`)

## Workflow

### Step 1: Accept Input

The user provides one of:
- A YouTube URL
- A local video/audio file path
- A raw transcript (skip to Step 4)

Optional: the user may specify a target directory. Default: `ingested/` in the project root (configure this to match your vault structure).

### Step 2: Download Audio

For YouTube URLs, extract audio only. Use a sanitised filename to avoid unicode issues (yt-dlp titles often contain full-width colons, CJK characters, etc. that break mlx_whisper output):

```bash
mkdir -p /tmp/ingest
yt-dlp --print title --print duration_string "YOUTUBE_URL"  # get metadata first
yt-dlp -x --audio-format mp3 --audio-quality 0 \
  -o "/tmp/ingest/%(title)s.%(ext)s" \
  "YOUTUBE_URL"
# Then rename to a clean kebab-case filename
cd /tmp/ingest && mv *.mp3 clean-filename.mp3
```

For local files, copy to `/tmp/ingest/` with a clean filename.

Tell the user the video title and duration before proceeding.

### Step 3: Transcribe

Run mlx_whisper in the background (it takes a few minutes even on Apple Silicon):

```bash
mlx_whisper "/tmp/ingest/FILENAME.mp3" \
  --model mlx-community/whisper-large-v3-turbo \
  --language en \
  --output-format txt \
  --output-dir /tmp/ingest/
```

First run downloads the model (~1.5 GB). Subsequent runs are instant.

Speed: ~10-12x real-time (60-min video ≈ 5-6 mins).

**While transcription runs**, tell the user it's processing and roughly how long it will take based on the video duration.

### Step 4: Extract Structured Knowledge

Read the transcript and extract the following into a structured note. Use your judgement on what's genuinely valuable – not every video has all categories.

**Required sections:**
- **Summary** – 2-3 sentence overview of the content
- **Key Claims** – distinct assertions worth preserving (aim for the non-obvious ones)
- **Frameworks / Mental Models** – named or unnamed models the speaker uses
- **Action Items / Techniques** – concrete things you could do based on this content
- **Notable Quotes** – only if genuinely memorable, not filler

**Frontmatter:**
```yaml
---
title: "Video Title"
source: "URL or file path"
author: "Speaker/channel name"
date_ingested: YYYY-MM-DD
type: ingest
tags:
  - ingest
  - topic-tag-1
  - topic-tag-2
---
```

### Step 5: Write the Note

Write the structured note to the target directory (default: `ingested/`).

Filename: kebab-case of the video title, e.g. `how-to-build-a-second-brain.md`.

### Step 6: Cleanup

Remove the downloaded audio and transcript from `/tmp/ingest/`.

```bash
rm -rf /tmp/ingest/
```

### Step 7: Confirm

Tell the user:
- Where the note was saved
- A brief summary of what was extracted (counts: X claims, Y frameworks, Z action items)
- Suggest they review and move the note to a permanent location if needed

## Guidelines

- **Local only** – no APIs, no cloud transcription. Everything stays on the machine.
- **Don't pad the output** – if a video only has 3 useful claims, write 3. Don't invent 12.
- **Preserve speaker attribution** – if the speaker names a framework or credits someone, include it.
- **Skip filler** – intros, outros, sponsor segments, "smash that like button" – ignore all of it.
- **Long videos (>90 min)** – warn the user about transcription time before starting. Offer to proceed or skip to a timestamp range if they have one.
- **Non-English content** – mlx_whisper supports many languages. Drop the `--language en` flag to auto-detect, or ask the user to specify.
