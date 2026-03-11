# Ingest Video and Audio as Structured Knowledge

## The problem

Someone shares a YouTube video or conference talk. You add it to your watch list. Three weeks later it's still there. Even if you watch it, the insights live in your head – not in your searchable knowledge base where Claude can retrieve them later.

## What /ingest does

- Downloads audio from YouTube, HLS/m3u8 streams (Maven, Mux, course platforms), or local audio/video files
- Transcribes locally on Apple Silicon using mlx-whisper – nothing leaves your machine
- Extracts structured knowledge: claims, frameworks, action items, quotes
- Writes an Obsidian-ready markdown note with proper frontmatter and tags
- Cleans up temporary files

A 40-minute video takes about 4-5 minutes end-to-end.

## Prerequisites

- **Apple Silicon Mac** (M1/M2/M3/M4) – mlx-whisper uses Apple's MLX framework and won't run on Intel
- **Homebrew** – for installing dependencies

## Setup

### 1. Install dependencies

```bash
brew install yt-dlp ffmpeg
uv tool install mlx-whisper
```

If you don't have `uv` yet:

```bash
brew install uv
```

### 2. Verify installation

```bash
yt-dlp --version
mlx_whisper --help
ffmpeg -version
```

### 3. Add the skill

Copy `skills/ingest/SKILL.md` into your `.claude/skills/ingest/` directory.

### 4. Configure the output directory

The skill defaults to `ingested/` in your project root. To change this, edit the `Default:` path in Steps 1 and 5 of the SKILL.md to match your vault structure. For example:

```
Default: `03_reference/resources/ingested/`.
```

### 5. First run – model download

The first transcription downloads the whisper-large-v3-turbo model (~1.5 GB). This is a one-time cost – subsequent runs use the cached model.

## Usage

In Claude Code:

```
/ingest https://www.youtube.com/watch?v=VIDEO_ID
```

You can also pass HLS/m3u8 stream URLs – common on course platforms like Maven, or any site using Mux for video hosting:

```
/ingest https://stream.mux.com/PLAYBACK_ID.m3u8?token=JWT_TOKEN
```

For streams, also provide the page URL (e.g. the Maven lesson link) so the skill can set the correct Referer header. Most video platforms use domain-level access control on their streams – the token alone isn't enough, the request needs to look like it's coming from the hosting site.

To find the m3u8 URL: open the lesson page → browser DevTools → Network tab → filter by "m3u8" → copy the full URL including the token parameter.

Local files work too:

```
/ingest /path/to/recording.mp4
```

Or a raw transcript if you already have one:

```
/ingest (then paste or reference the transcript file)
```

Claude will show you the video title and duration, run the transcription in the background, then present the structured note for review.

## What the output looks like

Each ingested note includes:

- **Frontmatter** with source URL, author, date, and tags
- **Summary** – 2-3 sentences
- **Key Claims** – non-obvious assertions worth preserving
- **Frameworks / Mental Models** – named or unnamed models from the content
- **Action Items / Techniques** – concrete takeaways
- **Notable Quotes** – only if genuinely memorable

## Tips

- **Queue videos as you find them** – don't wait until you have time to watch. Ingest now, review the note later.
- **Review the output** – Claude extracts what it judges valuable, but you know your context better. Move, edit, or delete sections as needed.
- **Pair with semantic search** – if you're using QMD, ingested notes are automatically indexed and searchable by meaning. A claim from a video you ingested months ago can surface during unrelated work.
- **Non-English content** – mlx-whisper supports many languages. Claude will auto-detect or ask you to specify.
- **Long videos (>90 min)** – Claude will warn you about transcription time. You can provide a timestamp range if you only care about part of the video.

## Troubleshooting

| Issue | Fix |
|---|---|
| `mlx_whisper: command not found` | Run `uv tool install mlx-whisper` and ensure `~/.local/bin` is in your PATH |
| Transcription produces no output file | Usually a unicode filename issue. The skill sanitises filenames automatically – if you hit this, check the `/tmp/ingest/` directory for files with special characters |
| `yt-dlp` 403 errors | Run `brew upgrade yt-dlp` – YouTube frequently changes their API and older versions break |
| Slow transcription | Confirm you're on Apple Silicon (`uname -m` should return `arm64`). On Intel Macs, use OpenAI's whisper CLI instead (much slower but works) |
| m3u8 stream returns "not_authorized" | The stream requires a Referer header matching the hosting domain. Provide the page URL alongside the stream URL so the skill can set it. If it still fails, the JWT token may have expired – grab a fresh one from DevTools |
| `yt-dlp` hangs on m3u8 URLs | Known issue with token-authenticated HLS streams. The skill uses `ffmpeg` directly for m3u8 – if you're running manually, use `ffmpeg -headers "Referer: https://DOMAIN/"$'\r\n' -i "M3U8_URL" -vn -acodec libmp3lame -q:a 2 output.mp3` |
