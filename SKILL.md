---
name: gemini-flow-visual-loop
description: Run multi-round, exhibition-grade still image and keyframe generation loops on Google Flow (Gemini Flow / Nano Banana) via flow-mcp. Use when drafting 500+ word Pure-Lens or Ref-Forge Flow prompts, driving flow_generate / flow_wait / flow_new_project, auditing generated images with native vision across the 4-stage Visual-Loop (Scout, Sculpt, Forensic-Check, Polish 2K), or configuring, patching, and troubleshooting flow-mcp.
---

# gemini-flow-visual-loop: Agentic Image Generation & Critique on Google Flow

Use this skill whenever driving **Google Flow** (`flow` MCP server, `GTAI-1/flow-mcp`) to create high-bar still artwork, visual series, or storyboard keyframes, or when diagnosing `flow-mcp` latency and browser state issues.

## 1. Session Pre-Flight & Credit Guardrails

1. **Verify session health**: Call `flow_status` first to confirm `signedIn: true` and an active project.
   - If `flow_status` fails with `fetch failed` while the Flow Chrome window (CDP port `9333`) is still open, the current MCP client is a `RemoteCore` proxy whose primary `LocalCore` listener on `127.0.0.1:8787` exited. Launch `node ~/flow-mcp/dist/studio.js` as a background process to re-bind `127.0.0.1:8787` and re-run `flow_status`.
   - If `signedIn` is `false` or Chrome was closed, ask the user to sign in once via `cd ~/flow-mcp && npm run login` (`~/.flow-mcp/chrome-profile`).
2. **Zero-credit stills vs. paid video gate**:
   - Still images (`type: "image"`, Nano Banana) and 2K upscaled image downloads (`download_quality: "upscaled"`) cost **0 credits**.
   - Before invoking **any** credit-spending video operation (`type: "video"`, `flow_edit`, `flow_continue`), always check remaining credits via `flow_status`, report the estimated credit spend to the user, and pass an explicit `max_credits` cap. Prototype video motion at `360p` before using free `1080p`/`2K` upscaling.

## 2. Mode Selection, Reference Staging & Series Orthogonality

Classify the task before writing Round 1 prompts:

| Mode | When to Use | Reference Strategy |
| --- | --- | --- |
| **`Pure-Lens`** (Text-First) | Real-world architecture, motorsport, landscapes, astrophysics, and conceptual photography where base optical/physical priors are strong | `0–1` mood references; drive geometry, optics, and texture entirely through a **500+ word** prompt |
| **`Ref-Forge`** (Reference-Anchored) | Specific game worlds, fictional IPs, custom architectural lore, recurring characters, or invented glyph systems | Collect primary 3D in-game screenshots, press-kit sheets, or official glyph tables (Steam Store API `appdetails`, studio blogs, publisher kits; skip fan art). Stage **1–2 high-signal anchors per scene** (`3–6` only when fusing distinct motifs) |

- **Pure-ASCII reference staging**: Symlink or copy all local reference images into a pure-ASCII directory (e.g., `~/flow-mcp-out/<slug>_refs/ref_01.jpg`) before passing paths to `flow_generate`. Avoid CJK characters, spaces, or punctuation-heavy folder names in `reference_images`.
- **Orthogonal multi-piece planning**: When creating a gallery or multi-piece set, design scenes to be orthogonal across five axes: **subject motif, aspect ratio (`16:9`, `3:4`, `9:16`, `1:1`, `4:3`), spatial vantage point, color palette, and physical medium**.

## 3. High-Density Physical Prompting Rules (500+ Words)

Write English image prompts of **500+ words** (and **650–900+ words** for video prompts) with concrete physical constraints:

1. **Explicit per-reference roles & Round 1 artifact stripping (`Ref-Forge`)**: State at the top of the prompt what each reference contributes (architectural silhouette, costume proportions, color palette, or glyph strokes), and explicitly strip source contamination in Round 1 (in-game HUD overlays, cover text, borders, or dead/fossilized props when depicting living subjects).
2. **Full-bleed lock on traditional craft media**: Whenever rendering screens, tapestries, porcelain plaques, stained glass, copperplate etchings, linocuts, or woodblocks, specify `full-bleed edge-to-edge flat artwork filling 100% of the canvas, zero museum wall, zero gallery spotlights, zero wooden stand or frame` from Round 1.
3. **Asymmetric cropping & microscopic scale anchors for megastructures**: Let colossal walls, domes, rings, and hulls extend asymmetrically beyond the top and lateral edges of the frame, dissolve upper rooflines into mist or shadow, and pair them with tiny world-consistent scale cues (a lone microscopic traveler, small skiff, or hairline catwalk lamp) so structures never shrink into centered "hovering UFO" props.
4. **Positive physical replacements over bare negations**: Replace vague `"no X"` bans with renderable physical interactions (e.g., `"concentric waterline ripples and a dark wet hull band grounding the boat"`, `"upward flashlight beam projecting a soft circular lit patch onto the overhead basalt vault"`, `"leaded turquoise stained-glass segments framed by dark solder cames"`).

Load [`references/prompt-templates-and-cases.md`](references/prompt-templates-and-cases.md) when you need complete 500+ word `Pure-Lens`, `Ref-Forge`, `Sculpt`/`Forensic`, or 2K `Polish` prompt templates and real R1–R4 defect-to-fix examples.

## 4. `flow-mcp` Execution & Queue Hygiene

1. **Use `flow_generate` with one scene per tool call**:
   - Submit `type: "image"` with `variants: 2` per scene. When queuing multiple scenes that carry `reference_images`, invoke `flow_generate` **once per scene** in parallel rather than packing nested `reference_images` arrays into a single multi-scene call.
   - Skip `flow_agent_images` for iterative loops (prone to stalling or timing out on Flow's `Reuse prompt` button and dropping the whole batch).
   - Never pass local file paths to `first_frame` for still-image iteration; pass all still references via `reference_images`.
2. **Pipeline `flow_wait` with immediate multimodal inspection**:
   - `flow_wait` returns within ~40 seconds with newly completed files (`~/flow-mcp-out/<project>/scene-NN-vX.jpeg`). Inspect finished variants immediately using your native image reader (`read_file`) and draft next-round adjustments while remaining queued scenes finish rendering.
3. **Manage `asset:<title>` reuse vs. `flow_new_project` rotation**:
   - Inside a clean project (`≤ 10–12` tiles), `asset:<title>` reuses an existing project image in 0 seconds (no re-upload). Note that `asset:` only resolves within the **currently open** project, and requires the `src/core.ts` patch from [`patches/flow-mcp-visual-loop.patch`](patches/flow-mcp-visual-loop.patch) so `-scene-\d+` / `fm-` stills do not trigger `CHARACTER LOCK`.
   - Once a project accumulates `> 12` tiles (pushing older assets out of Flow's `Asset list` DOM viewport and causing 10-second lookup timeouts) or if **any** tile hangs at `99%`, call `flow_new_project` to open a fresh blank workspace and pass the winning local disk path (`~/flow-mcp-out/<prev-project>/scene-NN-vX.jpeg`) in `reference_images`. Rotate projects routinely every **1–2 rounds**.

Load [`references/flow-mcp-runbook.md`](references/flow-mcp-runbook.md) when applying [`patches/flow-mcp-visual-loop.patch`](patches/flow-mcp-visual-loop.patch) or troubleshooting `flow-mcp` timeouts, `EPERM` errors, or Chrome CDP state.

## 5. Four-Stage `Visual-Loop` Critique & Iteration (4–5 Rounds Minimum)

Never approve a Round 1 image on atmosphere alone. Run every scene through four distinct stages, inspecting actual pixels with `read_file` after each round:

1. **R1 — `Scout` (Composition, Scale & Palette)**:
   - Generate `variants: 2`. Compare framing, focal compression, aspect balance, and color discipline; pick the stronger structural candidate.
2. **R2 — `Sculpt` (Structural Reconstruction & Ghost Purging)**:
   - Reference the R1 winner and fix macro geometry: push cropped megastructures past frame edges, adjust scale anchors, fix 3D pass-through collisions, and **explicitly delete every secondary trace of any removed object** (its reflection, water wake, cast shadow, contrail, or halo) to prevent inpainting ghosts.
3. **R3 — `Forensic-Check` (Five-Point Geometric, Optical & Lore Audit)**:
   - Audit every image at full zoom against five hard criteria, targeting the **2–4 clearest defects** per round:
     1. **Horizon & Perspective**: Straight level horizons, plumb verticals, smooth continuous ring/arch curvature without kinks or depth-order inversions.
     2. **3D Occlusion & Volumetric Terminations**: Distinct foreground/midground/background planes; beams, mist, bubbles, and sand streams start and end coherently on surfaces.
     3. **Reflections & Surface Contact**: Vertical mirror alignment on water/wet stone; concentric ripples, wet tide lines, footprints, or contact shadows where objects meet surfaces.
     4. **Light Transport & Scattering**: Unified primary light direction across lit faces and shadows; physically plausible Rayleigh scattering across distant silhouettes and planetary dark hemispheres.
     5. **Medium Purity & Lore Fidelity**: Edge-to-edge full-bleed craft surfaces; liquid glass swells free of waterfall streaks; worldbuilding anatomy, glyphs, and physics preserved over generic real-world priors (re-attaching close-up anchor references when needed).
4. **R4 (or R4–R5) — `Polish` (Geometry Lock & Free 2K Export)**:
   - Lock the verified R3 structure (`strictly preserving its exact locked composition... Zero new objects, zero text, zero UI`).
   - Polish micro-textures, film grain, specular roll-off, and edge mist transitions, setting `download_quality: "upscaled"` (2K, 0 credits). Verify that no new artifacts appeared before archiving final outputs and recording round-by-round decisions.

## 6. `Audio-Loop`: Batch Music Generation & Listening Iteration (`Lyria 3.5`)

When the patched `flow-mcp` is installed, you can also drive **Google Flow Music** (`https://www.flowmusic.app/`, `Lyria 3.5`) via `flow_music_generate`:

1. **Silent background batch generation**: Pass `project` and `tracks` (`1–20` items, each with `sound`, optional `lyrics`, `instrumental`, `advanced`, and `format: "mp3" | "wav" | "m4a"`). `flow_music_generate` reuses a background `flowmusic.app` tab without stealing window focus (`bringToFront()` is never called) and saves tracks to `~/flow-mcp-out/<project>/track-NN.<ext>`.
2. **Wait & listen silently**: Poll with `flow_wait`, then inspect each downloaded `.mp3`/`.wav` directly with `read_file` (which reads file bytes silently without playing through host speakers).
3. **Iterate on arrangement & lyrical realism (≥ 2 rounds)**: Audit genre fidelity, intro hook, vocal tone, lyrical specificity (replacing generic AI clichés and unintended genre-trigger nouns with concrete narrative details), and dynamic climaxes, then queue refined tracks until the audio meets the brief. Never upload copyrighted reference audio to Flow Music; use text-only original style descriptions.
