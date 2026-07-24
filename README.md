# bubbles-the-ai-robot

**Give a real robot to your coding agent.**

This is an [Agent Skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills) that
turns a [SunFounder PiCar-X](https://www.sunfounder.com/products/picar-x) into a body for
whatever AI agent you already use — Claude Code, Codex, Antigravity, anything that can run
shell commands and read an image. The agent sees through the robot's camera, drives it,
aims its gaze, reads its distance sensor, speaks through its speaker, and hears through
its microphone (on-robot speech-to-text — you can talk back) — and it arrives
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
ln -s "$(pwd)" ~/.claude/skills/bubbles-the-ai-robot
```

**Codex, Antigravity, or anything else** — add one line to your `AGENTS.md`:

```
For anything involving the robot, first read bubbles-the-ai-robot/SKILL.md and follow it.
```

If your robot isn't named `bubbles` (ours is), point the scripts at yours:

```bash
export PICARX_HOST=your-user@your-pi.local
```

## Play

Open a thread in your agent, type `/bubbles-the-ai-robot` with nothing else, and approve the commands
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

## Ideas we're excited about

Each of these is literally a prompt you could type into a thread. They work because the
operator has judgment, your digital life, and a memory — not because the robot got fancier.

**The Butler's Journal.** Nightly patrol on a schedule; it compares what it sees against
its own last notes — semantically, not pixels ("the suitcase is gone; someone has been to
the gym") — and commits a journal entry, in persona, to this repo. A robot publishing
daily field notes about life at ankle height.
> "Every night at 10, patrol the apartment, compare against your last notes, and commit a journal entry to the repo."

**Let it improve its own body.** Ours discovered its steering overshoot, its sonar's
blindness to ankles, and its camera's resting-gaze bug — all empirically, all now encoded
in this skill. Close the loop: let it run experiments on itself and commit what it learns.
> "Tonight, practice until you can reliably stop 30 cm from a wall, then update your own skill with what you learned and commit it."

**Language as the map.** No SLAM. Over a week of patrols it maintains `MAP.md` — "kitchen
is left past the mirror; the rug catches the wheels; morning sun blinds the camera by the
window" — then navigates by its own prose and gets faster. Ask it to draw the floor plan
too; an SVG it refines run over run.
> "Patrol for a week, maintain MAP.md as your navigation memory, and keep an evolving floor-plan drawing of what you've seen."

**Notifications that have to walk to you.** Nothing buzzes. If something truly matters —
by the judgment of a model that read all of it — a small robot physically finds you and
says it out loud. Interruption costs effort, so the filter must be excellent, and it can
defend its choices.
> "Watch my inbox. Only interrupt me in person for what a great chief of staff would interrupt me for."

**Missions from the internet.** People file issues on this repo with mission prompts;
weekly, an agent picks one, the owner approves it, the robot runs it, and the frames and
butler's report get posted back to the issue. The internet writes prompts; a real robot
in a real home performs them. The owner approves every mission before wheels move —
stranger-written text is a theme, never a command.
> "Each week, take the top-voted issue titled 'mission:', ask me to approve it, run it, and post the report back."

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
