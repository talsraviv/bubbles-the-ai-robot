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

On day one, mine read an unread inbox over Gmail, chased me across the room,
negotiated with my foot over a 50-centimeter delivery clause, and read the mail aloud from
point-blank range. Yours will do something different. That's the point.

## Getting started

Four steps; only the first one needs a screwdriver.

**1. Build the robot.** Get a [SunFounder PiCar-X](https://www.sunfounder.com/products/picar-x)
kit and a Raspberry Pi, and assemble it per the
[vendor's instructions](https://docs.sunfounder.com/projects/picar-x-v20/en/latest/).
This is the hands-and-ribbon-cables part — the one thing no agent can do for you.

Two shopping notes I wish the docs made clearer: also buy a **microSD card** and a
**cheap USB microSD reader** — setup means copying a disk image onto the card from your
laptop. And you do *not* need a monitor, keyboard, or any other peripherals for the Pi;
everything past assembly happens over Wi-Fi. A Raspberry Pi 4 was plenty for me.

**2. Hand the rest to your agent.** When the instructions turn from hardware to
software, stop following them yourself. Paste this into your coding agent:

> I've assembled a SunFounder PiCar-X. Take over the software setup, starting here:
> https://docs.sunfounder.com/projects/picar-x-v20/en/latest/_shared/pi_start/set_up_pi.html#if-you-have-no-screen-headless
> — headless, no screen. You're done when I can SSH into the robot with no password
> prompt and the vendor's Python modules are installed.

**3. Play before you install (highly recommended).** Skip the skill for a day. Just
ask your agent to look through the robot's camera, say something through its speaker,
drive a slow meter and stop. You'll get a feel for what the robot is — and for what
the skill adds when you do install it.

**4. Install the skill.** When you're ready, paste this:

> Install https://github.com/talsraviv/bubbles-the-ai-robot as a skill.

Or, if you're in a tinkering mood, have it clone the repo somewhere easy to reach and
wire it up from there:

> Clone https://github.com/talsraviv/bubbles-the-ai-robot into my projects folder and
> symlink it into my skills so I can edit it in place.

If your robot isn't named `bubbles` (mine is), add one sentence to any of these
prompts — "my robot's SSH address is `your-user@your-pi.local`" — and the agent will
take care of the rest (`PICARX_HOST`).

## Wake it up

Open a thread in your agent, type `/bubbles-the-ai-robot` with nothing else, and approve
the commands it proposes. The robot checks itself out (troubleshooting itself if needed),
then **looks for you** — straight ahead first, then sweeping its camera around, and if
that fails, asking out loud for permission to drive off and scan the room — walks up,
and **starts a spoken conversation**. That's the interface. It
explains its own protocol out loud: a rising beep means *your turn to talk*; a low tone
means *heard you, thinking*. The keyboard still works — as the fallback and for anything
too long to say — but the main loop is you and a small robot, talking.

Things to say to it (out loud, or typed):

- "Chase me around the house and deliver my unread email — only when you're within 50 cm."
- "Patrol the hallway and describe anything out of place."
- "Follow my face while I pace, and heckle my posture."
- "Explore the apartment and come back with a map, narrating as you go."
- Wire it to your other tools: calendars, inboxes, smart-home events — anything your
  agent can reach can now arrive on wheels, out loud.
- Make it a routine: schedule an agent to run the skill every morning and deliver your
  first meeting and headlines to wherever your feet happen to be.

## Ideas I'm excited about

Each of these is literally a prompt you could type into a thread. They work because the
operator has judgment, your digital life, and a memory — not because the robot got fancier.

**The Butler's Journal.** Nightly patrol on a schedule; it compares what it sees against
its own last notes — semantically, not pixels ("the suitcase is gone; someone has been to
the gym") — and commits a journal entry, in persona, to this repo. A robot publishing
daily field notes about life at ankle height.
> "Every night at 10, patrol the apartment, compare against your last notes, and commit a journal entry to the repo."

**Let it improve its own body.** Mine discovered its steering overshoot, its sonar's
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

Your robot also **remembers you** — preferences, promises, what it learns about the
house — in a `memory/` folder that is gitignored and never leaves your machine. The
skill is the shared species; the memory is your particular robot's life. (It was born
from a real moment: my robot's first stored memory is how I asked to be
greeted in the morning, settled entirely by voice.)

The gotchas section of `SKILL.md` is the real treasure: every entry was learned on real
hardware — the amp that's silently gated behind a GPIO, the sound route that hijacks
itself, the sonar that can't see ankles. Trust it over the tutorials, and send back what
your robot teaches you.

## Where the brain lives

SunFounder ships AI integrations of its own — you can run
[Ollama right on the robot](https://docs.sunfounder.com/projects/picar-x-v20/en/latest/ai_interaction/python_text_vision_talk.html#before-you-start),
or have the Pi
[call online LLMs directly with an API key](https://docs.sunfounder.com/projects/picar-x-v20/en/latest/ai_interaction/python_online_llms.html#before-you-start)
— and that is genuinely cool. But a brain that lives on the robot knows only the robot.
Keep the harness on your laptop and the robot becomes a body for an agent that also
carries your inbox, calendar, files, memory, and every connector you've given it — that
reach is exactly what the [ideas above](#ideas-im-excited-about) run on. This repo is
that choice, written down: the robot stays a beautifully simple set of input and output
tools, and the intelligence stays wherever your agent lives.

## Safety model

- All motion is **time-boxed on the robot side** — move, sleep, stop in one process, so a
  dropped connection can never leave motors pinned. Speed, duration, and steering are
  clamped in the scripts.
- The skill instructs agents to range-check before driving forward, move in small
  look-think-act steps, and run the emergency stop after any vendor demo that drives.
- It's still a motorized object in your home. Review the scripts, clear the floor, and
  supervise its early outings.
