# Auto-Ingest a YouTube Channel

## The problem

You subscribe to a podcast or YouTube channel that consistently produces valuable content. You watch maybe a third of the episodes. The rest sit in your subscription feed, and the knowledge in them is inaccessible – you can't search what you haven't watched.

## The approach

Auto-transcribe every new episode locally and add it to a searchable knowledge base. This gives you two tiers:

1. **Passive** – every episode is transcribed and semantically searchable. When a topic comes up in your work, you can find relevant clips across hundreds of episodes without having watched them.
2. **Selective** – for the episodes that look particularly relevant, run `/ingest` to extract structured knowledge notes (claims, frameworks, action items).

The passive tier runs on a schedule with no manual effort. The selective tier is a conscious choice per episode.

## Prerequisites

Same as the [/ingest skill](ingest-video-knowledge.md):

```bash
brew install yt-dlp ffmpeg uv
uv tool install mlx-whisper
```

## Setup

### 1. Create the transcript directory

```bash
mkdir -p ~/youtube-transcripts/episodes
```

### 2. Find your channel URL

Go to the channel page on YouTube. The URL you need is:

```
https://www.youtube.com/@ChannelHandle/videos
```

### 3. Create the sync script

Save this as `~/youtube-transcripts/scripts/sync.sh`:

```bash
#!/bin/bash
# sync.sh – Download and transcribe new episodes from a YouTube channel
#
# Usage:
#   ./scripts/sync.sh                    # sync new episodes
#   ./scripts/sync.sh --dry-run          # show what would be downloaded
#   ./scripts/sync.sh --limit 5          # only process up to 5 new episodes

set -euo pipefail

# Prevent sleep while transcription runs (releases automatically on exit)
caffeinate -i -w $$ &

REPO_DIR="$(cd "$(dirname "$0")/.." && pwd)"
EPISODES_DIR="$REPO_DIR/episodes"
ARCHIVE_FILE="$REPO_DIR/.yt-dlp-archive"
TMP_DIR="/tmp/yt-sync"
WHISPER_MODEL="mlx-community/whisper-large-v3-turbo"

# ---- CONFIGURE THESE ----
CHANNEL_URL="https://www.youtube.com/@YourChannel/videos"
CHANNEL_NAME="Your Channel"
MIN_DURATION=600  # seconds – skip shorts/clips under 10 min
# --------------------------

DRY_RUN=false
LIMIT=0

while [[ $# -gt 0 ]]; do
    case "$1" in
        --dry-run) DRY_RUN=true; shift ;;
        --limit) LIMIT="$2"; shift 2 ;;
        *) echo "Unknown option: $1"; exit 1 ;;
    esac
done

# Build archive from existing episodes on first run
if [[ ! -f "$ARCHIVE_FILE" ]]; then
    echo "Building archive from existing episodes..."
    for transcript in "$EPISODES_DIR"/*/transcript.md; do
        [[ -f "$transcript" ]] || continue
        vid=$(grep "^video_id:" "$transcript" 2>/dev/null | head -1 | awk '{print $2}')
        if [[ -n "$vid" ]]; then
            echo "youtube $vid" >> "$ARCHIVE_FILE"
        fi
    done
    if [[ -f "$ARCHIVE_FILE" ]]; then
        echo "Archive built with $(wc -l < "$ARCHIVE_FILE" | tr -d ' ') entries"
    else
        touch "$ARCHIVE_FILE"
        echo "Archive created (empty – no existing episodes)"
    fi
fi

mkdir -p "$TMP_DIR"

# Find new videos not in archive
echo "Checking for new episodes..."
NEW_VIDEOS_FILE="$TMP_DIR/new-videos.txt"
yt-dlp --download-archive "$ARCHIVE_FILE" \
    --print "%(id)s	%(title)s	%(upload_date)s	%(duration)s	%(view_count)s" \
    --skip-download \
    --match-filter "duration > $MIN_DURATION" \
    --playlist-items 1-50 \
    "$CHANNEL_URL" > "$NEW_VIDEOS_FILE" 2>/dev/null || true

if [[ ! -s "$NEW_VIDEOS_FILE" ]]; then
    echo "No new episodes found."
    rm -rf "$TMP_DIR"
    exit 0
fi

COUNT=$(wc -l < "$NEW_VIDEOS_FILE" | tr -d ' ')
echo "Found $COUNT new episode(s)"

if $DRY_RUN; then
    echo ""
    echo "Would process:"
    while IFS=$'\t' read -r vid title date dur views; do
        pub_date="${date:0:4}-${date:4:2}-${date:6:2}"
        echo "  - $title ($pub_date, $((dur / 60))m)"
    done < "$NEW_VIDEOS_FILE"
    rm -rf "$TMP_DIR"
    exit 0
fi

PROCESSED=0

while IFS=$'\t' read -r vid title date dur views; do
    if [[ "$LIMIT" -gt 0 && "$PROCESSED" -ge "$LIMIT" ]]; then
        echo "Limit of $LIMIT reached, stopping."
        break
    fi

    pub_date="${date:0:4}-${date:4:2}-${date:6:2}"

    # Create slug from title
    slug=$(echo "$title" | tr '[:upper:]' '[:lower:]' | \
        sed 's/[^a-z0-9 -]//g' | tr ' ' '-' | sed 's/--*/-/g' | \
        sed 's/^-//;s/-$//' | cut -c1-60)

    # Handle duplicate slugs
    if [[ -d "$EPISODES_DIR/$slug" ]]; then
        suffix=2
        while [[ -d "$EPISODES_DIR/$slug-$suffix" ]]; do
            ((suffix++))
        done
        slug="$slug-$suffix"
    fi

    echo ""
    echo "[$((PROCESSED + 1))/$COUNT] $title"
    echo "  Slug: $slug | Date: $pub_date | Duration: $((dur / 60))m"

    # Download audio
    echo "  Downloading..."
    yt-dlp -x --audio-format mp3 --audio-quality 0 \
        -o "$TMP_DIR/$vid.%(ext)s" \
        "https://www.youtube.com/watch?v=$vid" 2>/dev/null

    MP3_FILE="$TMP_DIR/$vid.mp3"
    if [[ ! -f "$MP3_FILE" ]]; then
        echo "  ERROR: Download failed, skipping"
        continue
    fi

    # Transcribe
    echo "  Transcribing (~$((dur / 10 / 60))min)..."
    mlx_whisper "$MP3_FILE" \
        --model "$WHISPER_MODEL" \
        --language en \
        --output-format txt \
        --output-dir "$TMP_DIR/" 2>/dev/null

    TXT_FILE="$TMP_DIR/$vid.txt"
    if [[ ! -f "$TXT_FILE" ]]; then
        echo "  ERROR: Transcription failed, skipping"
        continue
    fi

    # Format duration
    if [[ "$dur" -ge 3600 ]]; then
        duration_fmt="$(( dur / 3600 )):$(printf '%02d' $(( dur % 3600 / 60 ))):$(printf '%02d' $(( dur % 60 )))"
    else
        duration_fmt="$(( dur / 60 )):$(printf '%02d' $(( dur % 60 )))"
    fi

    # Fetch description separately (contains newlines, can't pipe)
    desc=$(yt-dlp --print "%(description)s" "https://www.youtube.com/watch?v=$vid" 2>/dev/null | head -5)
    desc_escaped=$(echo "$desc" | sed "s/'/''/g")

    # Write transcript with YAML frontmatter
    mkdir -p "$EPISODES_DIR/$slug"
    cat > "$EPISODES_DIR/$slug/transcript.md" << FRONTMATTER
---
title: >-
  $title
youtube_url: https://www.youtube.com/watch?v=$vid
video_id: $vid
publish_date: $pub_date
description: '$desc_escaped'
duration_seconds: ${dur}.0
duration: '$duration_fmt'
view_count: $views
channel: $CHANNEL_NAME
---
FRONTMATTER

    cat "$TXT_FILE" >> "$EPISODES_DIR/$slug/transcript.md"

    echo "youtube $vid" >> "$ARCHIVE_FILE"
    rm -f "$MP3_FILE" "$TXT_FILE"

    echo "  Done → episodes/$slug/"
    PROCESSED=$((PROCESSED + 1))
done < "$NEW_VIDEOS_FILE"

rm -rf "$TMP_DIR"
echo ""
echo "Sync complete. Processed $PROCESSED episode(s)."
```

Make it executable:

```bash
chmod +x ~/youtube-transcripts/scripts/sync.sh
```

Edit the `CHANNEL_URL`, `CHANNEL_NAME`, and `MIN_DURATION` variables at the top of the script to match your channel.

### 4. Test it

```bash
# Preview what would be downloaded
./scripts/sync.sh --dry-run

# Process one episode to verify the pipeline
./scripts/sync.sh --limit 1
```

Check the output in `episodes/` – you should see a folder with a `transcript.md` containing YAML frontmatter and the full transcript text.

### 5. Backfill existing episodes

If the channel has a back catalogue you want, run the script without `--limit`. A 60-minute episode takes about 6 minutes to transcribe on Apple Silicon, so budget accordingly. You can interrupt and resume – the archive file tracks what's already been processed.

```bash
# Process everything (leave laptop open, caffeinate is built in)
./scripts/sync.sh
```

### 6. Schedule weekly sync

Create a launchd plist to run the sync automatically. Pick a time when your laptop is reliably open – the script includes `caffeinate` to prevent sleep during transcription, but it can't wake a closed lid.

Save as `~/Library/LaunchAgents/com.yt-sync.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.yt-sync</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>export PATH="/opt/homebrew/bin:$HOME/.local/bin:$PATH" &amp;&amp; $HOME/youtube-transcripts/scripts/sync.sh 2&gt;&amp;1 &gt;&gt; $HOME/youtube-transcripts/sync.log</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Weekday</key>
        <integer>3</integer>
        <key>Hour</key>
        <integer>17</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>
    <key>StandardErrorPath</key>
    <string>/tmp/yt-sync-error.log</string>
</dict>
</plist>
```

Load it:

```bash
launchctl load ~/Library/LaunchAgents/com.yt-sync.plist
```

This runs every Wednesday at 5 PM. Adjust the `Weekday` (0=Sunday, 1=Monday, ..., 6=Saturday) and `Hour` to match when your laptop is open.

## Connecting to semantic search

The transcripts are markdown files with YAML frontmatter. Any tool that indexes markdown will work. If you're using [QMD](qmd-semantic-search.md), add the transcript directory as a collection:

```bash
qmd add youtube-transcripts ~/youtube-transcripts/episodes --glob "**/*.md"
```

QMD will index the transcripts on its next update cycle. After that, you can search across every episode:

```
qmd search --collection youtube-transcripts "product-market fit signals"
```

Or from Claude Code via the QMD MCP, the transcripts are searchable alongside your vault.

## The two-tier workflow in practice

**Weekly (automatic):** The sync script downloads and transcribes any new episodes. QMD indexes them. You do nothing.

**As needed (manual):** You're working on a problem and semantic search surfaces a relevant transcript. You skim the section. If the whole episode is worth extracting structured knowledge from, run `/ingest` on the local transcript file:

```
/ingest ~/youtube-transcripts/episodes/episode-name/transcript.md
```

This skips download and transcription (already done) and goes straight to knowledge extraction – takes about 30 seconds.

## Multiple channels

To sync multiple channels, either:

1. **Separate directories** – one transcript repo per channel, each with its own script and launchd plist. Cleanest separation, easiest to manage.
2. **Shared directory** – use the `channel` field in the frontmatter to distinguish sources. Adjust the slug generation to avoid collisions across channels.

Option 1 is simpler unless you have many channels. Each launchd plist can run at the same time – the scripts use separate temp directories and won't conflict.

## Troubleshooting

| Issue | Fix |
|---|---|
| launchd job doesn't run | Check `launchctl list \| grep yt-sync` – status `-` means loaded but not yet triggered. Wait for the scheduled time, or test manually with `launchctl start com.yt-sync` |
| Transcription stalls mid-episode | Laptop likely slept. Verify `caffeinate` is in the script. Check that the lid was open during the scheduled time |
| Archive gets out of sync | Delete `.yt-dlp-archive` and rerun – it rebuilds from existing `episodes/` directories |
| `yt-dlp` 403 errors | Run `brew upgrade yt-dlp` – YouTube changes their API frequently |
| Duplicate episode folders | The script appends `-2`, `-3` etc. for duplicate slugs. This handles channels that release multiple episodes with the same guest |
