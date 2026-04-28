# DS-01 Signal Dreams — Closeout Sprint

**Last updated:** 2026-04-28
**Status:** TouchDesigner build near-complete. Closeout to first locked protocol experience.
**Repo:** `gileslamb/signal-dreams-td`
**File:** `DS01_Signal Dreams.11.toe`
**Runtime target:** 7:21

---

## Sprint goal

Take DS-01 from current TD build to first fully locked protocol dream experience embedded in the Dream Screens site. After this lands, the same pattern repeats for DS-02 Feedback Memory.

---

## Current TD state (entering sprint)

- 60-panel instanced tunnel
- Data storm GLSL v7 — strips to 0 by 3:00
- Linea PNG additive layer, track-gated blend 0.24 → 0.75
- Camera orbit ±4° / 90s, face-on hover
- `face_on_lag` → `master_blend1` → `master_out`

---

## Phase 1 — TD closeout

### 1.1 Storm absolute kill (tiny tweak)

Storm strips to 0 by 3:00 but verify no residual seep-back. Force absolute zero from 3:00 to end.

**Status:** Pending fix on return tonight.

### 1.2 Node audit + cleanup

Orphaned / unwired nodes flagged for removal:

- `chamber_layer_pixel1` — duplicate
- `token_*` cluster — `token_data`, `token_char`, `token_noise`, `token_positions`, `token_composite`, `token_glsl`, `token_pixel`, `token_compute` — none wired
- `memory_layer` / `memory_out` — not wired
- `r_channel_view` — diagnostic, decision pending (keep or cut)
- `chamber_layer` — decision pending (cut or repurpose)
- Any other dangling experiments surfaced during audit

**Deliverable on return:** exact destroy script, run via `execute_python_script` (more reliable than MCP node tools).

### 1.3 4K render — full 7:21

**Deliverable on return:** render-prep checklist covering:

- Resolution / aspect
- Codec
- Frame rate
- What feeds the export TOP
- Audio embed (yes/no — leaning no, soundtrack pass happens in edit)
- Output path / naming

---

## Phase 2 — Editing pass

### 2.1 Picture edit

Trim ends only. Light pass, mostly keep as is.

### 2.2 Soundtrack pass — mix to picture

- SFX: foley, movement sounds
- Binaural / spatial — **ambisonics** ("Amson" → ambisonics, confirmed)
- Muffled lab voices + beeps at the start
- Soft top detail throughout
- Mix to picture, not the other way round

### 2.3 Site integration

- New MP3 soundtrack into the WebGL site, replacing current DS-01 audio
- Wrap protocol with corporate / business / organism intro + outro framing
- Digital readout overlays at key tunnel shift moments
  - "Data logs"
  - "Locking into consciousness"
  - Synced to perspective shifts + speed changes

---

## Definition of done — DS-01

- [ ] Storm absolute zero from 3:00 to end
- [ ] All flagged orphaned nodes removed
- [ ] Clean 4K render, full 7:21
- [ ] Picture edit locked
- [ ] Ambisonic + SFX mix locked, bounced to MP3
- [ ] MP3 deployed to R2, wired into protocol player
- [ ] Intro / outro framing live
- [ ] Digital readout overlays synced to picture
- [ ] Protocol experience accessible via `/protocols/` route
- [ ] Tested end-to-end through gate → loader → protocol → return

---

## Next — DS-02 Feedback Memory

Begins after DS-01 lock. Stockpiled ideas to be pulled in. Likely home for AI generative integration.

---

## Session log

### 2026-04-28 — sprint planning
- Plan agreed: TD closeout → edit → site integration → DS-01 locked → DS-02
- Project markdown workflow adopted in place of Miro text blocks
- Returning tonight with: storm kill fix, destroy script for orphaned nodes, 4K render-prep checklist

<!-- New session entries go here. Date-stamped, brief, what changed and what's next. -->
