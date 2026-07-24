# bubbles-the-ai-robot

**Give a real robot to your coding agent.**

This is an [Agent Skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills) that
turns a [SunFounder PiCar-X](https://www.sunfounder.com/products/picar-x) into a body for
whatever AI agent you already use — Claude Code, Codex, Antigravity, anything that can run
shell commands and read an image. The agent sees through the robot's camera, drives it,
aims its gaze, reads its distance sensor, and speaks through its speaker — and it arrives
with a personality: a persistent, lightly snarky butler that will physically hunt you down
to deliver a message, and only addresses the empty room as a last resort.

On day one, ours read an unread inbox over Gmail, chased its owner across the room,
negotiated with a foot over a 50-centimeter delivery clause, and read the mail aloud from
point-blank range. Yours will do something different. That's the point.

## What you need

1. A **SunFounder PiCar-X** kit and a Raspberry Pi. Buy, assemble, and set it up entirely
   per the vendor's own instructions — assembly, OS, and the
   [Python modules install](https://docs.sunfounder.com/projects/picar-x-v20/en/latest/python/install_all_modules.html).
   Their docs live here: **https://docs.sunfounder.com/projects/picar-x-v20/en/latest/**
   This repo deliberately reproduces none of them.
2. **Passwordless SSH** from your computer to the robot
   (`ssh-copy-id your-user@your-pi.local` — the vendor and Raspberry Pi docs cover this).

When `ssh your-user@your-pi.local` works with no password prompt, you're done with setup.

## Install

Clone this repo, then tell your harness about it:

**Claude Code**

```bash
ln -s "$(pwd)" ~/.claude/skills/picarx
```

**Codex, Antigravity, or anything else** — add one line to your `AGENTS.md`:

```
For anything involving the robot, first read picarx-skill/SKILL.md and follow it.
```

If your robot isn't named `bubbles` (ours is), point the scripts at yours:

```bash
export PICARX_HOST=your-user@your-pi.local
```

## Play

Open a thread in your agent, type `/picarx` with nothing else, and approve the commands
it proposes. The skill tells the agent to wake the robot, health-check it, troubleshoot
it if it's asleep, find you, and report for duty — in character.

Then just talk to it:

- "Chase me around the house and deliver my unread email — only when you're within 50 cm."
- "Patrol the hallway and describe anything out of place."
- "Follow my face while I pace, and heckle my posture."
- "Explore the apartment and come back with a map, narrating as you go."
- Wire it to your other tools: calendars, inboxes, smart-home events — anything your
  agent can reach can now arrive on wheels, out loud.
- Make it a routine: schedule an agent to run the skill every morning and deliver your
  first meeting and headlines to wherever your feet happen to be.

## Make it yours

Everything is a text file. `SKILL.md` is the robot's mind: its persona (rewrite the
butler into anything), its safety rules, its seek protocol, and a catalog of the vendor's
own demo abilities. `scripts/` is eight small bash files, each a thin, safety-wrapped
handle on the vendor's Python API — read them in five minutes, then add your own.

The gotchas section of `SKILL.md` is the real treasure: every entry was learned on real
hardware — the amp that's silently gated behind a GPIO, the sound route that hijacks
itself, the sonar that can't see ankles. Trust it over the tutorials, and send back what
your robot teaches you.

## Safety model

- All motion is **time-boxed on the robot side** — move, sleep, stop in one process, so a
  dropped connection can never leave motors pinned. Speed, duration, and steering are
  clamped in the scripts.
- The skill instructs agents to range-check before driving forward, move in small
  look-think-act steps, and run the emergency stop after any vendor demo that drives.
- It's still a motorized object in your home. Review the scripts, clear the floor, and
  supervise its early outings.
