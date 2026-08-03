# Mapping a room in 3D

How to survey a room with the PiCar-X and turn it into a floor plan or a
navigable 3D page. Method only — the hardware facts this relies on (sonar is
chassis-fixed, useless while moving, ~24 cm/s, ~60° per K-turn pair) are in
SKILL.md's Iron rules and Gotchas. Read those first; don't re-derive them.

Tools live in `docs/room-mapping/`. Run them from the skill root:
`docs/room-mapping/scan out "-10 20" "-90 0 90"`.

---

## The method

1. **Topology before driving.** One low sweep from standstill:

   ```
   docs/room-mapping/scan survey1 "-10" "-90 -60 -30 0 30 60 90"
   ```

   Tilt −10 catches where things meet the floor, which is what tells you *which
   wall* an object sits against. Seven frames covers 180°; rotate and repeat for
   the rest. This one capture answers more than any amount of driving.

   Add a tilt of +35 to +50 when you need to place yourself. Floor-level frames
   are surprisingly hard to localise from; one tilted-up frame fixes it.

2. **Dimensions from wall contact.** Reverse until the chassis physically
   touches, then range forward:

   ```
   docs/room-mapping/calib -30 0.8 3
   ```

   Span = forward reading + chassis (~23 cm). Repeat on the perpendicular axis.
   Two anchors are enough for a rectangular room. You know you've made contact
   when the range stops changing between steps.

3. **Cross-check odometry against sonar.** Drive a known number of steps and
   compare the summed displacement against the change in a wall reading. On the
   first survey: odometry said 61 cm travelled, sonar said the target moved
   39.8 → 100.8 cm. Agreement to a centimetre means both are sound. Disagreement
   means one is lying — find out which before modelling anything.

4. **Batch every SSH round trip.** One connection should set the servo, capture,
   repeat, and hand everything back in a single `scp`. A per-frame connection
   turns a seven-frame sweep from seconds into a minute. All four tools work
   this way; it is the biggest speed difference in the workflow.

`calib` never ranges while moving — it drives a fixed step, stops, settles, and
takes a median. Each step's delta is a real displacement, so it measures the
room and calibrates speed at the same time.

---

## Traps particular to rooms

**Mirrors.** A mirrored closet door reads as a second room, convincingly enough
to survive several passes. Tells: a **floor track** (hard horizontal line at the
base of the opening), **vertical frame edges** boxing the "room" in, and a scene
that **duplicates the one you're standing in**. If a room appears to contain two
of something, suspect glass before geometry.

**The beam sees through gaps.** It threads under desks and through the gap
beneath a closet door, so a long reading toward furniture is usually the *wall
behind* it. That's useful — it's how you measure across a room — but never
assume the echo came from the nearest visible object.

**Small rooms are tight.** The first surveyed office was 2.30 × 3.25 m with
about a metre of clear floor. Micro-arcs and frequent snaps beat confident long
drives.

**Being picked up voids dead reckoning.** If a human moves the robot, every
position estimate from before that moment is fiction. Re-anchor on a known
landmark before trusting another number.

---

## Modelling what you found

Two things that made the result worth looking at:

**One model, two views.** Keep the surveyed room in a single data file and have
the floor plan and the 3D view both render from it. They then cannot disagree,
and correcting a measurement fixes both.

**Record confidence per object** — `measured` / `observed` / `inferred` — and
show it. A robot survey is not a survey drawing; a model that says which parts
are guesses is more useful than one that guesses confidently. Leave the room
dimensions user-editable: the owner knows their house better than the robot.

Expect to hand-build the geometry. Boxes, a few leaning transforms, and
procedural textures got a recognisable room with no dependencies.

---

## Checklist

- [ ] Burst-test the camera (5 captures) before relying on it.
- [ ] Low pan sweep from standstill → topology.
- [ ] Tilt-up sweep → localisation and upper-wall features.
- [ ] Wall-contact anchor on axis A → span A.
- [ ] Rotate ~90° (≈1.5 K-turn pairs) → anchor on axis B → span B.
- [ ] Odometry vs sonar cross-check; reconcile before modelling.
- [ ] Photograph each wall and every object worth naming.
- [ ] Note anything reflective **before** modelling it as open space.
- [ ] Build with confidence tags; leave dimensions editable.

Get the sonar measurements while the camera is healthy. Dimensions don't need
it, so a survey that loses its camera should still produce a room.
