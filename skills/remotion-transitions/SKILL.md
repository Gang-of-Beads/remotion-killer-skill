---
name: remotion-transitions
description: Scene-to-scene transitions in Remotion using @remotion/transitions TransitionSeries — presentations, timing, custom presentations, and when to use manual cross-fades. Use when joining scenes.
---

# Scene Transitions

## TransitionSeries (default choice)

- Structure: `<TransitionSeries>` containing alternating `<TransitionSeries.Sequence durationInFrames>` and `<TransitionSeries.Transition presentation timing>` elements.
- The transition OVERLAPS its neighbors: its frames are taken from the tail of the previous and head of the next sequence. Total length = Σ(sequence durations) − Σ(transition durations). Each sequence must be longer than the transition duration.
- Give each sequence 10–20 extra tail/head frames of "settled" content so the transition has calm material to blend, not mid-animation motion.

## Presentations

- Built-ins: `fade`, `slide` (direction: from-left/right/top/bottom), `wipe`, `flip`, `clock-wipe`, plus linear/radial wipe variants. Import per module: `@remotion/transitions/fade` etc. (no combined barrel export).
- Choosing:
  - `fade` — neutral, safe, slightly boring; use for tone shifts.
  - `slide` — spatial metaphor (next chapter coming in); keep direction consistent across the whole video.
  - `wipe`/`clock-wipe` — energetic, retro; good for montage/countdown.
  - `flip` — dramatic reveal of a "back side"; needs calm content on both sides.
- Custom presentation: implement `(props) => ReactElement` receiving `progress` (0→1) and `enterTransition`/`exitTransition` flags; return children wrapped in a style driven by progress (transforms, opacity, filters). Register via `presentation={myPresentation()}`. Use for brand-specific moves (e.g. mask wipes with a logo shape).

## Timing

- `linearTiming({durationInFrames})` — deterministic, predictable, default choice (12–24 frames at 30fps).
- `springTiming({config})` — physical ease for playful videos; harder to predict total length.
- Transition duration guideline: 15–25 frames (0.5–0.8s) for narrative videos, 8–12 frames for fast social edits.

## Manual cross-fades (when OK)

- A single one-off fade between two Sequences: overlap them with `<Sequence from>` offsets and interpolate opacity. Fine for one case; do not build a system of hand-managed offsets — use TransitionSeries for anything composed.
- Never nest plain `<Sequence>` inside `TransitionSeries.Sequence`; use `TransitionSeries.Sequence` (it supplies `from` automatically).

## Gotchas

- Transition duration must be ≤ both adjacent sequence durations.
- `flip` and 3D-ish presentations need perspective — built-ins handle it; custom ones must add their own `perspective` style.
- Scene-detect (ffmpeg `select=gt(scene,t)`) does not flag gradual fades — QA transitions with sampled frames, not scene scores.
- Audio does not transition automatically; VO/music continue across the cut (usually desired — narration bridges visuals).
