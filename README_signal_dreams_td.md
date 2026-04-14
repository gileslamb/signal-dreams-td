# DS-01 Signal Dreams — TouchDesigner Session
`signal-dreams-v1.toe`

**Project path:** `/Volumes/GL Drive/PROJECTS/ACTIVE PROJECTS/Dreamscreens/Touch Designer/DS01_Signal Dreams/`
**Last stable state:** April 2026 — 60-panel tunnel, Linea additive layer, wired to master output

---

## External Dependencies

These paths must exist on the machine for the session to run correctly.

| Asset | Path |
|-------|------|
| Audio (Signal Dreams.mp3) | `/Users/gileslamb/Dream_Screens_ Signal_Dreams /Media/Signal Dreams.mp3` |
| Linea PNG sequence (1185 frames) | `/Users/gileslamb/Giles Apps/PNG Extractor/Linea-relazione_frames` |
| MCP server (.tox) | `/Users/gileslamb/Library/CloudStorage/Dropbox/Mac (2)/Downloads/touchdesigner-mcp-td/mcp_webserver_base.tox` |
| MCP port | `9981` |

---

## Architecture Overview

```
audio_chop (audiofileinCHOP)
  → audio_spectrum → amplitude → amplitude_smooth → amplitude_out
  → data_storm (glslTOP) — GLSL shader v7, bitmap numerals, 6 size tiers

Panel_System_3_1 (baseCOMP)
  ├── render1          — 60 data storm panels (geo1, phong1)
  ├── render_frost     — 60 Linea PNG panels (geo_frost, phong_frost)
  ├── comp_final       — addTOP (render1 + frost_level)
  └── master_level     — levelTOP → out1 → panel_system_out (root)

master_blend1 (crossTOP)
  ├── input 0: data_storm
  └── input 1: panel_system_out
  → master_out (nullTOP) — FINAL OUTPUT
```

---

## Panel System (Panel_System_3_1)

| Parameter | Value |
|-----------|-------|
| Panel count | 60 |
| Panel size | 3.2 × 1.8 units |
| Spacing | 0.9 units |
| Tunnel depth | ~46 units |
| Scroll speed | absTime.seconds × 0.3 (ramp phase) |
| Wrap buffer | 9 panels behind camera (invisible) |

---

## Camera Orbit

| Parameter | Value |
|-----------|-------|
| Primary LFO | ±0.5 units / 90s → ry ±4° |
| Secondary LFO | ±0.8 units / 240s → rare drift ±10° |
| Z range | 3.3 – 5.7 (deep in tunnel) |
| FOV | 80° |
| Face-on dwell | ~70% of primary cycle |

Camera expressions live on `cam1` inside `Panel_System_3_1`:
- `tx` / `ty` — driven by `orbit_merge` CHOP (orbit_lfo_x + orbit_lfo_y + orbit_lfo_x2)
- `ry` — `orbit_merge['tx'] * 8.0` (tracks sideways position)
- `tz` — `orbit_lfo_y['ty'] * 1.2 + 4.5` (tunnel breath)

---

## Linea PNG Layer

| Parameter | Value |
|-----------|-------|
| Source | `linea_seq` (moviefileinTOP, specify mode) |
| Frames | 1185 |
| Per-panel stagger | `frame_scale` gain=600, stagger window drifts at 1.5fps |
| Frame safety | `frame_clamp` (0–1184) |
| Blend mode | Additive (src=one, dest=one) over data storm |
| Tint | Cool blue-white (diffr=0.6, diffg=0.8, diffb=1.0) |

**Track arc (uTrackPos = audio index / 441.0):**
- `0.00–0.24` (0:00–1:46) — absent
- `0.24–0.46` (1:46–3:24) — emerging (linear ramp via tdu.remap)
- `0.46–0.75` (3:24–5:33) — fully present (face_on_lag gates final intensity)
- `0.75–1.00` (5:33–7:21) — receding (remap 1.0→0.3, ready for video layer)

**face_on_lag:** orbit_merge tx → face_on_math (abs, remap 0–1) → face_on_lag (4s rise / 2s fall) → drives phong_frost emit (0→5.0)

---

## Master Blend

`master_blend1` crossTOP cross expression:
```
tdu.clamp(tdu.remap(trackPos, 0.15, 0.55, 0.0, 1.0), 0.0, 1.0)
```
- Below 0.15: data storm solo
- 0.15–0.55: panel system blends in
- Above 0.55: panel system dominant

---

## Toggles & Controls

| Control | Location | Default | Effect |
|---------|----------|---------|--------|
| Black Pulse | Panel_System_3_1 → Signal Dreams page | OFF | Restores intermittent black panel pulse effect |

---

## MCP / Claude Integration

To reconnect Claude MCP after reopening .toe:
1. In TD Textport: `op('/project1/mcp_webserver_base').par.reinitialize.pulse()`
2. Or click `mcp_webserver_base` COMP → Reinitialize
3. Claude Desktop must be running with TD MCP config active

---

## Next Build (Future Sessions)

1. **Home video memory layer** — same architecture as Linea, warm/amber tint, gates from trackPos 0.60
2. **UI artefacts / status readouts** — text overlays, scan line aesthetic, machine data / poetic inference
3. **4K render export** — for web/Pictoplasma, Movie File Out TOP
4. **Narrative architecture** — bring ChatGPT story document into Claude, build full protocol narrative
5. **Cluster drift** — data storm cluster zones need to drift and reform over time

---

## Git Workflow

```bash
# Start of every session — commit before touching anything
git add -A && git commit -m "session start snapshot - [date]"

# After each stable working state
git add -A && git commit -m "description of what works"

# If something breaks — revert to last good state
git log --oneline
git checkout [commit-hash] signal-dreams-v1.toe
```

---

## Signal Dreams — Narrative Context

DS-01 is Protocol 1 of the DreamScreens system. A young composer (semi-autobiographical) whose mother died young enters the DreamScreens facility — a fictional neural memory reconstruction company. This is his first full dream connection process. The protocol attempts to locate, extract and reconstruct memory traces strong enough to enable reconnection.

**Visual arc:**
- Act I: Raw machine process — data storm, cold and computational
- Act II: System finds a trace — Linea geometric forms crystallise through the data
- Act III: Memory fragments distributed across 60 panels — a memory sculpture
- Act IV: Dissolution / glimpse of reconnection, then the machine tears it apart

**Bookends:** Frosted glass hospital environment (generative video, processed) at open and close — establishing physical reality before pulling audience inside his consciousness.

