# Remotion Killer Skill

Open-source agent skills for making **maximum-impact videos with [Remotion](https://remotion.dev)** — programmatic video in React. Content-free and technique-only: no project assets, just distilled, battle-tested guidance for what actually makes Remotion videos look stunning and ship reliably.

## What's inside

| Skill | Use for |
| --- | --- |
| [`remotion-killer`](skills/remotion-killer/) | Router + the shared working loop (start here) |
| [`remotion-2d`](skills/remotion-2d/) | Animation tuning (spring/interpolate), kinetic type, stagger, layout, charts |
| [`remotion-3d`](skills/remotion-3d/) | `@remotion/three` + React Three Fiber: determinism, camera, lighting, particles, headless GL |
| [`remotion-transitions`](skills/remotion-transitions/) | `TransitionSeries`, presentations, custom transitions, timing |
| [`remotion-audio-sync`](skills/remotion-audio-sync/) | TTS narration (ElevenLabs/Azure/MiniMax/OpenAI), beat sync & onset snapping, captions, ducking |
| [`remotion-style-system`](skills/remotion-style-system/) | Palettes & ramps, gradients/orb blobs, glassmorphism, grain, glow, style presets |
| [`remotion-rendering`](skills/remotion-rendering/) | Render CLI, codecs, `--scale` multi-resolution, fps interpolation, platform presets |
| [`remotion-video-qa`](skills/remotion-video-qa/) | ffmpeg evidence extraction + rubric-based acceptance of rendered video |

## Install

```bash
npx skills add https://github.com/Gang-of-Beads/remotion-killer-skill --skill remotion-killer
# or add individual skills
npx skills add https://github.com/Gang-of-Beads/remotion-killer-skill --skill remotion-3d
```

(Or copy a skill folder into your agent's skills directory — each is a self-contained `SKILL.md` following the [agent skills](https://www.skills.sh/anthropics/skills/skill-creator) convention.)

## Why "killer"

The official Remotion skills cover the API thoroughly. These skills cover what they don't: **the craft layer** — how to tune springs so motion feels expensive, build glassmorphism that survives headless rendering, snap cuts to beats detected from generated music, drive deterministic 3D from the frame clock, design one composition that renders at any resolution, and run a rigorous QA pass on the rendered file before calling it done. Everything here was verified against real renders (see each skill's gotchas for the failures we hit so you don't have to).

## Highlights

- **Determinism contract**: every visible value derives from `useCurrentFrame()`; seeded randomness only; `delayRender` for async assets.
- **Audio-first timing**: measure TTS offline with ffprobe, derive scene durations from narration + buffer, `calculateMetadata` for dynamic lengths.
- **Beat snapping without ML**: `framesPerBeat = fps * 60 / bpm` for generated music; onset detection (librosa/aubio) for arbitrary tracks; decaying-pulse envelopes for visual hits.
- **Multi-resolution by design**: relative sizing + `--scale`, with a grep rule that catches hardcoded pixels before they bite.
- **QA like an editor**: ffmpeg evidence bundles (metadata, sampled/scene-change frames, loudness, sync events) plus a visual inspection rubric.

## License

MIT
