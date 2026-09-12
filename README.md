# Remotion Killer Skill

Open-source agent skills for making **maximum-impact videos with [Remotion](https://remotion.dev)** — programmatic video in React. Content-free and technique-only: no project assets, just distilled, battle-tested guidance for what actually makes Remotion videos look stunning and ship reliably.

## Proof: the trailer was built with these skills

The promo trailer was produced end-to-end by an agent following exactly these skills — every technique in the video maps to a skill entry:

| In the video | Skill entry |
| --- | --- |
| 3D warp tunnel + starfield, camera dolly | `remotion-3d` — ThreeCanvas, frame-driven props, seeded particles |
| Conic halo + CSS-3D orbit rings behind the title | `remotion-style-system` / `remotion-2d` — orbit rings, conic halos |
| Mask-reveal kinetic title with unclipped glow layer | `remotion-2d` — mask reveals, stagger springs |
| Blueprint grid + glow sweep over the skill cards | `remotion-2d` — blueprint grid; `remotion-style-system` — glow recipes |
| Featured card that 3D-flips with glow peak at 90° | `remotion-2d` — 3D card flip pattern |
| Waveform bars reacting to the narration | `remotion-audio-sync` — `useAudioData` + `visualizeAudio` |
| 3D glass panels with typewriter code | `remotion-3d` — transmission materials; `remotion-2d` — typewriter |
| QA bay: ffmpeg evidence terminal + APPROVED stamp | `remotion-video-qa` — evidence extraction + acceptance rubric |
| depth-push / whip-pan / punch-zoom cuts | `remotion-transitions` — custom presentations |

The production pipeline the agent ran (each step covered by a skill):

1. **Narration** — Azure Speech premium voice (`DragonHD`), one MP3 per scene (`remotion-audio-sync`)
2. **Timing** — `ffprobe` each VO file → scene `durationInFrames` = VO duration + buffer (`remotion-audio-sync`)
3. **Music** — MiniMax Music 3 open weights (`MiniMaxAI/MiniMax-Music3` via diffusers), instrumental at 120 BPM; on a 10 GB GPU stream the 17 GB text encoder with group offloading, read the result via `PipelineState.get("audios")`, and pass `audio_duration` to control length (`remotion-audio-sync`)
4. **Mixing** — BGM bed at 0.16, auto-ducked to 0.06 inside VO windows, fades at both ends (`remotion-audio-sync`)
5. **Render** — `--gl=angle` for WebGL scenes, `--crf 18` master (`remotion-rendering`)
6. **QA** — ffprobe metadata, sampled frames, `volumedetect`, visual pass over every scene and transition (`remotion-video-qa`)

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
