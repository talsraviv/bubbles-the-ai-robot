# Room-mapping tools

Survey helpers used to map a room and build a 3D model of it. They are kept
apart from the main `scripts/` because they are for one specific job, not
everyday operation. Read [`../mapping-a-room.md`](../mapping-a-room.md) first —
it explains why each of these behaves the way it does.

All of them run on **this** machine and SSH to the robot internally, same as the
main scripts. Each does its whole job in **one** SSH round trip, which is the
difference between a seven-frame sweep taking seconds and taking a minute.

| script | usage | what it does |
|---|---|---|
| `scan` | `scan OUTDIR "TILTS" "PANS" [W H]` | Dense spherical capture from the current spot: every tilt × every pan, pulled back in one `scp`. Prints the forward range first. Start here. |
| `calib` | `calib SPEED STEP_SECS STEPS` | Stepped measurement. Drives a fixed step, stops, settles, then takes a median range. Each step's delta is a real displacement — this is how you measure a room and calibrate speed at once. Never ranges while moving. |
| `rot` | `rot PAIRS DIR SECS` | Rotates in place with K-turn pairs (`DIR` = `l`/`r`), reporting the range after each. Roughly 60° per pair at 0.55 s. Touches no camera, so it works when the CSI link is down. |
| `waypoint` | `waypoint OUTDIR SPEED SECS STEER TILT PAN...` | Optional drive → forward range → pan sweep → pull frames. Use when you want to move and look in a single trip. `SPEED 0` means don't drive. |

Captures are wrapped in a 20 s timeout and failures are reported per frame rather
than aborting the run — a wedged camera should cost you one frame, not the whole
survey. Never `kill -9` a capture that is hanging; see the reliability notes in
the guide.
