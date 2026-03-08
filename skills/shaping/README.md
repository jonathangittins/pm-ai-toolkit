# Shaping

Collaborative solution shaping – iterating on problem definition (requirements) and solution options (shapes).

This skill is from [rjs/shaping-skills](https://github.com/rjs/shaping-skills). Install it from there.

## What it does

Guides a structured conversation between a PM and Claude Code to shape a solution before committing to a spec. The process alternates between refining the problem (what are the actual requirements?) and exploring solutions (what shapes could satisfy them?).

## Setup

```bash
# Clone the skills repo
git clone https://github.com/rjs/shaping-skills.git ~/.local/share/shaping-skills

# Symlink into your project
ln -s ~/.local/share/shaping-skills/shaping .claude/skills/shaping
```
