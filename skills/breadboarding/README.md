# Breadboarding

Transforms workflow descriptions into affordance tables showing UI and code boundaries with their wiring.

This skill is from [rjs/shaping-skills](https://github.com/rjs/shaping-skills). Install it from there.

## What it does

Takes a workflow description and produces structured tables that separate UI affordances (what the user sees and interacts with) from code affordances (what the system does behind the scenes), with explicit wiring between them. Useful for mapping existing systems or designing new ones from shaped parts.

## Setup

```bash
# Clone the skills repo
git clone https://github.com/rjs/shaping-skills.git ~/.local/share/shaping-skills

# Symlink into your project
ln -s ~/.local/share/shaping-skills/breadboarding .claude/skills/breadboarding
```
