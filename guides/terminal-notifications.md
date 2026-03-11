# Terminal Notifications for Claude Code

I run 4–6 Claude Code sessions at once. Each tab is a different task – one writing a spec, another triaging Slack, another running a data query. The problem: I never know which tab needs me. I'd switch over to find Claude had been waiting for approval for five minutes while I was staring at the wrong tab.

The fix is hooks – Claude Code's built-in system for running shell commands on lifecycle events. I wired up sound effects and terminal tab title changes so I can hear and see what each session needs without checking every tab.

## What hooks are

Hooks are shell commands that fire on Claude Code lifecycle events. You define them in `settings.json` and they run automatically. The ones that matter for notifications:

| Event | When it fires |
|---|---|
| `UserPromptSubmit` | You hit enter on a prompt |
| `Notification` | Claude needs your attention (includes permission prompts) |
| `PostToolUseFailure` | A tool call failed |
| `PreCompact` | Context window is being compressed |
| `SessionStart` | A session starts or resumes |
| `SessionEnd` | A session ends |

There's also a `PermissionRequest` event that fires when Claude needs approval for a tool call, but in practice `Notification` fires at the same time and covers the same ground. Using both means double sounds. Stick with `Notification` – it catches permission prompts and any other "I'm idle, come look" moment.

Full list in the [hooks reference](https://docs.anthropic.com/en/docs/claude-code/hooks).

## The approach

### Sound effects

Each event plays a different sound via `afplay` (macOS built-in). Pick sounds that are short, distinctive, and won't annoy you after the 500th time. I use video game sound effects – they're designed to convey state changes quickly.

Some mappings that work well:

| Event | Sound character | What it means |
|---|---|---|
| Notification | Distinctive call/ring | "I need you" |
| PostToolUseFailure | Buzzer / denied sound | "Something broke" |
| PreCompact | Sharp alert sting | "Context is being compressed" |
| SessionStart | Item/unlock sound | "Starting up" |
| SessionEnd | Sign-off sound | "Shutting down" |

I tried adding a sound on `Stop` (when Claude finishes responding) but dropped it. When you're actively working in the tab, you already know it's done – the sound is just noise. The sounds that earn their keep are the ones that pull you to a tab you're not looking at.

### Tab titles

Terminal escape sequences can set tab titles. This lets you glance at your tab bar and see which sessions need attention:

- **⚙️ working...** – Claude is processing (set on `UserPromptSubmit`)
- **❗ needs input** – blocked, waiting for you (set on `Notification`)
- **⚠️ error** – a tool failed (set on `PostToolUseFailure`)
- **🔄 compacting** – context compression (set on `PreCompact`)
- *(default title)* – idle, nothing happening

The trick: Claude Code resets the tab title when it returns to the input prompt. That means you can't persist a "done" state – Claude Code will overwrite it. Instead, flip the model: show status while Claude is *working or blocked*, and let the default title mean "idle/done." Absence of an emoji = nothing needs your attention.

## Setup

### 1. Create a sounds directory

```bash
mkdir -p ~/.claude/sounds
```

Drop your sound files here. Any format `afplay` supports works – `.mp3`, `.wav`, `.aiff`.

### 2. Create the notification script

Save this as `~/.claude/sounds/notify.sh`:

```bash
#!/bin/bash
# Claude Code notification handler
# Plays sounds and updates terminal tab title

SOUNDS_DIR="$HOME/.claude/sounds"
EVENT="${1:-notification}"

# Find the TTY of the Claude Code parent shell process.
# Hooks run in a subprocess without direct terminal access,
# so we walk up the process tree to find one with a real TTY.
find_tty() {
    local pid=$$
    local tty=""
    for _ in 1 2 3 4 5 6 7 8; do
        pid=$(ps -o ppid= -p "$pid" 2>/dev/null | tr -d ' ')
        [ -z "$pid" ] && break
        tty=$(ps -o tty= -p "$pid" 2>/dev/null | tr -d ' ')
        if [ -n "$tty" ] && [ "$tty" != "??" ]; then
            echo "/dev/$tty"
            return
        fi
    done
}

TTY=$(find_tty)

set_tab_title() {
    if [ -n "$TTY" ] && [ -w "$TTY" ]; then
        printf '\033]2;%s\007' "$1" > "$TTY"
    fi
}

case "$EVENT" in
    notification)
        set_tab_title "❗ needs input"
        afplay "$SOUNDS_DIR/notification.mp3" &
        ;;
    error)
        set_tab_title "⚠️ error"
        afplay "$SOUNDS_DIR/error.mp3" &
        ;;
    compact)
        set_tab_title "🔄 compacting"
        afplay "$SOUNDS_DIR/compact.mp3" &
        ;;
    working)
        set_tab_title "⚙️ working..."
        ;;
    start)
        afplay "$SOUNDS_DIR/start.mp3" &
        ;;
    exit)
        # nohup ensures sound plays even as session tears down
        nohup afplay "$SOUNDS_DIR/exit.mp3" &>/dev/null &
        ;;
esac
```

Make it executable:

```bash
chmod +x ~/.claude/sounds/notify.sh
```

The `&` after `afplay` runs the sound in the background so it doesn't block Claude Code.

The `find_tty` function is necessary because hooks run in a subprocess that doesn't have direct access to your terminal. It walks up the process tree until it finds the shell process that owns the TTY, then writes escape sequences directly to that device. This works in Ghostty, iTerm2, and Terminal.app.

The `nohup` on the exit sound is important – without it, the `SessionEnd` hook gets killed before the sound finishes playing, and you'll see "Hook cancelled" errors.

### 3. Configure the hooks

Hooks go in `settings.json`. If you have project-level settings (`.claude/settings.json` in your repo), **project hooks shadow user-level hooks** – they don't merge. Put the hooks wherever your other hooks live.

Replace `/Users/YOUR_USERNAME` with your actual home directory – tilde expansion may not work reliably in hook commands.

```json
{
  "hooks": {
    "Notification": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/Users/YOUR_USERNAME/.claude/sounds/notify.sh notification"
          }
        ]
      }
    ],
    "PostToolUseFailure": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/Users/YOUR_USERNAME/.claude/sounds/notify.sh error"
          }
        ]
      }
    ],
    "PreCompact": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/Users/YOUR_USERNAME/.claude/sounds/notify.sh compact"
          }
        ]
      }
    ],
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/Users/YOUR_USERNAME/.claude/sounds/notify.sh start"
          }
        ]
      }
    ],
    "SessionEnd": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/Users/YOUR_USERNAME/.claude/sounds/notify.sh exit"
          }
        ]
      }
    ],
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/Users/YOUR_USERNAME/.claude/sounds/notify.sh working"
          }
        ]
      }
    ]
  }
}
```

If you already have hooks (like post-edit guards or session namers), merge the new entries into the existing event arrays. Multiple hooks on the same event run in order.

### 4. Restart Claude Code

Hooks are loaded at session start. Restart any running sessions to pick up the changes.

## Gotchas

**Project settings shadow user settings.** If your repo has `.claude/settings.json` with a `hooks` key, hooks defined in `~/.claude/settings.json` won't fire. Move your notification hooks to whichever file already has your other hooks, or consolidate.

**`Notification` and `PermissionRequest` fire together.** When Claude needs tool approval, both events trigger back-to-back. Use one or the other, not both – otherwise you get double sounds. `Notification` is the broader event and covers permission prompts too.

**Tab titles don't persist at idle.** Claude Code resets the terminal title when it returns to the input prompt. Titles set during `Stop` or `SessionEnd` get immediately overwritten. That's why the "working/blocked" model works better than trying to show "done."

**SessionEnd hooks get killed early.** The session tears down before the hook finishes. Use `nohup` to detach the sound process so it survives the session ending.

**`osascript` returns lowercase app names.** If you add conditional logic based on which app is frontmost (e.g., skip sounds when the terminal is in focus), use case-insensitive comparison. `osascript` returns "ghostty" not "Ghostty."

**`afplay` is macOS only.** On Linux, use `paplay` (PulseAudio) or `mpv --no-video` instead. On Windows WSL, use `powershell.exe -c '(New-Object Media.SoundPlayer "path").PlaySync()'`.

**Keep sounds short.** Anything over 2–3 seconds gets old fast. The best notification sounds are under a second.

## Picking sounds

Short, distinctive sounds that won't drive you mad. Some ideas:

- **Video game sound effects** – designed for exactly this purpose. Alert stings, item pickups, codec rings, level-up chimes.
- **macOS system sounds** – `/System/Library/Sounds/` has Ping, Glass, Morse, Basso. Functional but not fun.
- **Synthesised tones** – generate with Python's `wave` module or `ffmpeg` if you want something custom.

The test: if you hear the sound from another room, do you immediately know what it means? That's a good notification sound.
