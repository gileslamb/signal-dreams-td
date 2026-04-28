# DS-01 Signal Dreams — Closeout Sprint

**Last updated:** 2026-04-28 (session 2)
**Status:** TD network cleaned, storm kill in place. Ready for 4K render pass.
**Repo:** `gileslamb/signal-dreams-td`
**File:** `DS01_Signal Dreams.toe` (note: working file is `.toe`, not `.11.toe` as previously logged)
**Runtime target:** 7:21 (441s)

---

## Sprint goal

Take DS-01 from current TD build to first fully locked protocol dream experience embedded in the Dream Screens site. After this lands, the same pattern repeats for DS-02 Feedback Memory.

---

## Current TD state

- 60-panel instanced tunnel
- Data storm GLSL v7 — now hard-killed to absolute zero from 3:00 onwards
- Linea PNG additive layer, track-gated blend 0.24 → 0.75
- Camera orbit ±4° / 90s, face-on hover
- `face_on_lag` → `master_blend1` → `master_out`
- Network cleaned: 123 nodes → 104 nodes (orphan token cluster + memory_layer removed)
- Motion slowdown into final dissolution: confirmed already working via existing phase weighting (no further change needed)

**Live signal path (verified intact):**
```
master_out (null)
  ← master_blend1 (cross)
       in0: master_blend (cross)
              in0: data_storm_fade (level) ← data_storm GLSL ← audio_source
              in1: chamber_layer GLSL
       in1: panel_system_out ← Panel_System_3_1
```

---

## Phase 1 — TD closeout

### 1.1 Storm absolute kill — DONE

`data_storm_fade.opacity` now expression-driven:
```python
max(0.0, min(1.0, 1.0 - (op('/project1/phase_gating/timeline_sec')['timer_seconds'] - 178.0) / 2.0))
```
Curve: 1.0 until 178s → 2-second crossfade → 0.0 from 180s through to end. Storm goes truly black behind chamber, no residual seep.

### 1.2 Node audit + cleanup — DONE

**Removed (19 nodes total):**
- `chamber_layer_pixel1` (duplicate)
- Token cluster: `token_data`, `token_char`, `token_noise`, `token_positions`, `token_composite`, `token_composite_glsl/pixel/compute/info`, `token_data_callbacks`, `token_script`
- `memory_layer` baseCOMP and all internals (`memory_out`, `fragment_glsl`, `fragment_glsl_pixel/compute/info`, `video_src`)

**Retained per decision:**
- `chamber_layer` + `chamber_layer_pixel` + `chamber_layer_compute` + `chamber_layer_info` — active in master_blend, NOT orphans (pixeldat/computedat bindings confirmed live)
- `r_channel_view` (diagnostic)
- `chamber_debug`

**Verification:** 0 node errors after cleanup, master_out signal path identical structure to pre-cleanup.

### 1.3 4K render — full 7:21 — NEXT SESSION

**Pre-flight checklist:**

| Item | Status |
|---|---|
| Save + commit current state | tonight |
| Confirm storm goes to black at 3:00 visually | pending |
| Confirm project FPS | pending |
| Check render TOP resolutions in `Panel_System_3_1` (likely need 4K bump) | pending |
| Audio source loaded and ready (drives `uAmplitude` for storm reactivity) | pending |
| Disk space confirmed | pending |

**Movie File Out TOP settings:**

| Parameter | Value |
|---|---|
| File Type | `.mov` |
| Codec | Apple ProRes 4444 (or 422 HQ if disk space tight) |
| Resolution | 3840 × 2160 |
| FPS | Match project (likely 60) |
| Audio Codec | None (audio handled separately in edit) |
| Realtime | Off |
| Unique Suffix | On |

**Render method:** Use `File → Export Movie...` dialog — most reliable for full timeline, runs offline, won't drop frames.

**Disk estimate:** ProRes 4444 at 4K ≈ 250–500 MB/s → ~100–200 GB for 7:21. ProRes 422 HQ ≈ 30–50 GB total.

**Note:** Several exports planned, hence Unique Suffix on.

---

## Phase 2 — Editing pass

### 2.1 Picture edit
Trim ends only. Light pass, mostly keep as is.

### 2.2 Soundtrack pass — mix to picture
- SFX: foley, movement sounds
- Binaural / spatial — ambisonics
- Muffled lab voices + beeps at the start
- Soft top detail throughout
- Mix to picture

### 2.3 Site integration
- New MP3 soundtrack into the WebGL site, replacing current DS-01 audio
- Wrap protocol with corporate / business / organism intro + outro framing
- Digital readout overlays at key tunnel shift moments
  - "Data logs"
  - "Locking into consciousness"
  - Synced to perspective shifts + speed changes

---

## Definition of done — DS-01

- [x] Storm absolute zero from 3:00 to end
- [x] All flagged orphaned nodes removed
- [ ] Clean 4K render, full 7:21 (multiple exports)
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

### 2026-04-28 — sprint planning (session 1)
- Plan agreed: TD closeout → edit → site integration → DS-01 locked → DS-02
- Project markdown workflow adopted in place of Miro text blocks

### 2026-04-28 — TD network cleanup + storm kill (session 2)
- Working file confirmed: `DS01_Signal Dreams.toe` (memory previously had `.11.toe`, now corrected)
- Audited all flagged orphans — confirmed `chamber_layer_pixel/compute/info` are LIVE shader bindings, not orphans (would have broken the chamber if removed)
- Storm absolute kill applied via expression on `data_storm_fade.opacity` (timeline_sec-driven, 2-sec crossfade ending at 3:00)
- 19 orphan nodes destroyed in single audited pass
- 0 node errors post-cleanup
- Visual verification by Giles: storm kill timing "great"
- Motion slowdown into end already working naturally via existing phase_out weighting — no extra change needed
- Session saved by Giles
- **Next session:** 4K render pass with Export Movie dialog, multiple exports planned

### Operational notes for future sessions

**MCP / Python script gotchas (re-confirmed this session):**
- `print()` does NOT return through MCP — use `result = ...` at end of script to get output back
- `ParMode` not in scope by default — get the enum via `type(par.mode).EXPRESSION` instead of bare `ParMode.EXPRESSION`
- Setting `par.expr = "..."` does NOT auto-switch mode to expression — must explicitly set `par.mode = EXPR_MODE` after
- Setting an invalid expression on a param leaves it in error state, blocking further script access — always test the expression accessor (e.g. `op(...)['chan'].eval()`) standalone before assigning it
- `findChildren()` count returned `0` post-deletion in same script — use `get_td_nodes` MCP tool for reliable count

**Timing reference points:**
- Canonical timeline source: `op('/project1/phase_gating/timeline_sec')['timer_seconds']` (not `timer` CHOP — it has `timer_fraction`/`ready`/`running`/`done`, not seconds)
- `timer.length = 441.0`
- Phase boundaries: P1 0:00–1:00, P2 1:00–2:30, P3 2:30–4:18, P4 4:18–6:15, P5 6:15–7:21
- Sub-events: shift_330 @ 3:30, intensity_518 ramp 4:18→5:30, dissolve_615 ramp 6:15→7:21

<!-- New session entries go here. Date-stamped, brief, what changed and what's next. -->
