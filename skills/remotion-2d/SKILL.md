---
name: remotion-2d
description: 2D scene craft in Remotion — animation tuning with spring/interpolate, kinetic typography, stagger choreography, layout grids, and per-scene structure. Use when building or polishing any non-3D scene.
---

# 2D Scene Craft

## Scene anatomy

- Each scene is a component receiving no props (reads `useCurrentFrame()` inside its own `<Sequence>` local timeline) or a `from` offset prop for cross-scene choreography.
- `<AbsoluteFill>` for full-bleed; explicit `backgroundColor` always (it defaults to transparent).
- Build scenes in layers: background (gradients/particles) → midground (cards/media) → foreground (text/captions) → vignette/grain overlay. Each layer is one component for easy tuning.

## Animation tuning

- `spring({frame, fps, config})` for entrances; `interpolate()` with `extrapolateRight: "clamp"` for everything mapped from progress. One spring drives many derived values (opacity, translateY, scale) — they stay in sync automatically.
- Presets (starting points, tune per project):
  - Punchy UI pop: `{ damping: 12, mass: 0.8, stiffness: 200 }`
  - Soft drift / ambient: `{ damping: 20, mass: 1, stiffness: 60 }`
  - Overshoot/bouncy: `{ damping: 8, mass: 1, stiffness: 180 }`
  - Heavy cinematic settle: `{ damping: 26, mass: 1.6, stiffness: 120 }`
- Easings: use `Easing.out(Easing.cubic)` for exits from screen, `Easing.inOut(Easing.sin)` for loops (breathing, floating). Reserve springs for things that should feel physical.
- Stagger lists by index: `spring({ frame: frame - i * stagger })`, stagger 2–6 frames per item. Reverse stagger (last first) reads as "reveal from the end" — good for lists where the punchline is last.
- Exit choreography: mirror the entrance (reverse translateY + fade) but faster (~60–70% of entrance duration). Never let exits overlap scene cuts unless the transition owns them (see remotion-transitions).
- Loop-friendly ambient motion: `Math.sin(frame * f + phase)` with f between 0.004 (slow drift) and 0.02 (noticeable bob). Combine two sines with different frequencies for organic motion.
- Overshoot/anticipation: pull back before a move (interpolate progress through [0, -0.1, 1] with keyframes) for cartoon emphasis; use sparingly (once per scene).

## Kinetic typography

- Split text into words or chars; wrap each in a span animated with the stagger pattern. Word-level for readability, char-level for display headlines only.
- Mask reveal: parent `overflow: hidden`, child `translateY(110%) → 0` — reads as "rising from a line", more premium than fade.
- Emphasis: scale + color shift on the single most important word; animate weight via separate font files only if preloaded (variable fonts cause layout shift mid-render).
- Text glow: `textShadow: 0 0 Npx color` — keep N ≈ fontSize/3 and alpha ≤ 0.6, else it reads cheap.
- Fonts load via `@remotion/google-fonts` `loadFont()` awaited at module scope, or `@fontsource/*` CSS imports in the root file. Verify with a still render before long renders.

## Layout

- 12-column mental grid; safe margins of ~5% width per side (larger, 8–10%, for 9:16 vertical).
- Hierarchy via scale jumps of 1.5–2x (not 1.2x) between levels; a video frame is seen for seconds, not read.
- Center is the default focal point; use rule-of-thirds (33%/66%) for diagrams and split layouts.
- All lengths relative: derive from `width`/`height` of `useVideoConfig()`, or use a scale factor `const s = width / 1920` applied to font sizes and spacing so the design survives resolution changes (see remotion-rendering).
- Cards/panels: border-radius ≈ 1/12 of card width; shadow `0 20px 60px rgba(0,0,0,0.3)` reads as depth without blur cost.

## Charts and data (no library needed)

- Bars/lines/donuts as SVG driven by spring progress: line reveals via `strokeDasharray`/`strokeDashoffset = length * (1 - progress)`; bar heights `interpolate(progress, [0,1], [0, value])`.
- Animate data, not CSS. Count-up numbers: `Math.round(interpolate(progress, [0,1], [0, target]))`.

## Gotchas

- `interpolate` throws on non-monotonic input ranges; guard `frame - delay` values that can go negative (`Math.max(0, ...)`).
- `filter: blur()` and large `box-shadow` are the most expensive CSS — test render speed early.
- Elements mounted mid-composition (inside later Sequences) crash at first-mount frame, not at bundle time; keep imports/props valid.
