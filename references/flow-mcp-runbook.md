# `flow-mcp` Companion Runbook & Patch Reference

Operational guide for running [`GTAI-1/flow-mcp`](https://github.com/GTAI-1/flow-mcp) reliably across multi-round `Visual-Loop` sessions in Antigravity, Codex, and Claude Code.

---

## 1. Tool Selection Matrix for `Visual-Loop`

| `flow-mcp` Tool | Role in `Visual-Loop` | Field Behavior & Caveats |
| --- | --- | --- |
| `flow_status` | Session & credit check | Confirm `signedIn: true`, active project, credit balance, and queue state before queuing jobs. |
| `flow_generate` | **Primary image & video generator** | Use `type: "image"`, `variants: 2` (0 credits). Queue **one scene per `flow_generate` call** when attaching `reference_images`. Set `download_quality: "upscaled"` in Round 4 (`Polish`) for free 2K exports. |
| `flow_wait` | Pipelined completion poller | Returns within ~40 s with partial completions and file paths under `~/flow-mcp-out/<project>/`. Inspect newly finished files immediately via `read_file` while remaining jobs run. |
| `flow_new_project` | Workspace rotation & cleanup | Creates a clean blank Flow project. Call every **1–2 rounds** (or immediately if any tile hangs at `99%`) to avoid `Asset list` DOM viewport truncation. |
| `flow_assets` | In-project asset listing | Takes ~13 s (Playwright grid scroll). Useful inside small projects (`≤ 12` items) to grab `asset:<title>` handles; unnecessary when passing local `~/flow-mcp-out/...` paths. |
| `flow_download` | Free re-download / upscale | Pulls existing Flow media without regenerating (including free 1080p/2K upscales). Always use this if a generation succeeded in Flow's UI without saving locally. |
| `flow_retry` / `flow_cancel` | Queue recovery | `flow_retry` re-runs failed jobs under the same target filename; `flow_cancel` drops queued jobs that have not started. |
| `flow_music_generate` | **Batch music generator (`Lyria 3.5`)** | Drives `https://www.flowmusic.app/session` Compose panel silently in a background tab (no `bringToFront()`). Supports `1–20` tracks per call (`sound`, `lyrics`, `instrumental`, `advanced`, `format: "mp3" \| "wav" \| "m4a"`), downloading to `~/flow-mcp-out/<project>/track-NN.<ext>`. Uses `flow_wait` / `flow_cancel` / `flow_retry`. |
| `flow_agent_images` | *(Avoid for iterative loops)* | Drives Flow's batch Agent composer. Frequently stalls mid-render or times out (30 s) clicking `Reuse prompt`, losing the entire batch with 0 files saved. |

### `first_frame` vs. `reference_images`

- **Still-image generation (`type: "image"`)**: Always pass visual anchors and prior-round winners in **`reference_images`**. Passing local file paths to `first_frame` on still jobs consistently fails.
- **Video generation (`type: "video"`)**: Flow's composer accepts *either* `first_frame`/`last_frame` *or* `reference_images`, never both simultaneously (use `flow_continue` only when continuing a video clip with a locked character).

---

## 2. Three Surgical Patches (`patches/flow-mcp-visual-loop.patch`)

Upstream `flow-mcp` is tuned around paid multi-shot video filmmaking. Three small source edits in [`patches/flow-mcp-visual-loop.patch`](../patches/flow-mcp-visual-loop.patch) adapt it for fast 0-credit image iteration:

### Patch 1: `src/queue.ts` — Separate Image Pacing from Paid Video Pacing

- **Root cause**: `JobQueue.work()` enforces a single `25–70 s` random pause (`PAUSE_MIN_MS` / `PAUSE_MAX_MS`) after every job, adding ~45 s of dead wait between 0-credit still images.
- **Fix**: Adds `FLOW_MCP_IMAGE_PAUSE_MIN_S` (default `2`) and `FLOW_MCP_IMAGE_PAUSE_MAX_S` (default `4`) selected whenever `job.params.type === "image"`. Video jobs retain the original `25–70 s` pacing.

### Patch 2: `src/core.ts` — Exclude Scene Stills from `CHARACTER LOCK`

- **Root cause**: When `reference_images` includes `asset:<name>` and `<name>` matches `/^[\w .'-]{1,60}$/`, `LocalCore` appends a strict `CHARACTER LOCK` prompt block (*"same head shape and size, same face, same eyes, same hair, same clothing..."*). Referencing a Round 1 scene still (named e.g. `AL-Project-scene-01` or `fm-...`) triggers that character lock and fights Round 2 structural edits.
- **Fix**: Tightens the filter to `characterLook(n) !== undefined || (!/(-scene-\d+|^fm-)/i.test(n) && /^[\w .'-]{1,60}$/.test(n))`, preserving `CHARACTER LOCK` for named cast members while keeping scene-still `asset:` references lock-free.

### Patch 3: `src/flow.ts` — Guard `Agent` Mode Button on Fresh Projects

- **Root cause**: `runGeneration()` calls `await agent.getAttribute("aria-pressed")` unconditionally on `page.getByRole("button", { name: "Agent", exact: true })`. Right after `flow_new_project` opens an empty workspace where that toggle is absent, Playwright waits 30 seconds for the selector and aborts the job.
- **Fix**: Guards the check with `if ((await agent.isVisible().catch(() => false)) && (await agent.getAttribute("aria-pressed")) === "true")`.

### Patch 4: `src/music.ts`, `src/chrome.ts`, `src/queue.ts`, `src/core.ts`, `src/remote.ts`, `src/index.ts` — Google Flow Music (`Lyria 3.5`) Extension

- **Purpose**: Adds `flow_music_generate` for batch music generation and automated audio download via Google Flow Music (`https://www.flowmusic.app/session`), reusing the logged-in `~/.flow-mcp/chrome-profile` CDP session.
- **Key engineering details**:
  - `getMusicPage()` reuses an existing `flowmusic.app` tab silently without calling `bringToFront()`, so batch runs never steal foreground focus.
  - Drives the Compose panel (`textarea[aria-label="Sound description"]`, `textarea[aria-label="Lyrics"]`, `Toggle instrumental mode`, `Toggle advanced sound mode`, `Generate`).
  - Downloads `MP3` / `WAV` / `M4A` by asserting CDP `Browser.setDownloadBehavior` (`~/.flow-mcp/downloads`), opening `More options` → hovering `Download` → focusing the format menuitem and sending `Enter` (reliable across Radix UI submenus even in background tabs), then moving the finished file to `~/flow-mcp-out/<project>/track-NN.<ext>`.
  - Uses dedicated music queue pacing (`FLOW_MCP_MUSIC_PAUSE_MIN_S=5`, `MAX_S=10`). First-time use requires signing into `flowmusic.app` once inside the dedicated Flow Chrome window.

### Applying, Verifying, and Updating

```bash
cd ~/flow-mcp

# Preview patch compatibility without modifying files
git apply --check /path/to/gemini-flow-visual-loop/patches/flow-mcp-visual-loop.patch

# Apply and compile TypeScript -> dist/
git apply /path/to/gemini-flow-visual-loop/patches/flow-mcp-visual-loop.patch
npm run build
```

When pulling upstream `flow-mcp` updates later:

```bash
cd ~/flow-mcp
git stash
git pull --ff-only
git stash pop
npm run install-if-needed || npm install
npm run build
```

---

## 3. Operational Troubleshooting Runbook

| Symptom | Root Cause | Immediate Resolution |
| --- | --- | --- |
| `EPERM: operation not permitted` on macOS startup | `flow-mcp` lives inside `~/Documents`, `~/Desktop`, or `~/Downloads` (macOS TCC protected) | Move the repo to `~/flow-mcp` and update `args` in your MCP config |
| `flow_status` returns `fetch failed` while Flow Chrome is still open | Earlier primary `LocalCore` process (`127.0.0.1:8787`) exited; current MCP client is stuck in `RemoteCore` proxy mode while Chrome CDP (`9333`) is still alive | Start `node ~/flow-mcp/dist/studio.js` in the background to re-bind `127.0.0.1:8787` against the live Chrome CDP session |
| `asset:<title>` times out after 10 seconds | Asset is in a previous project, or the current project has `> 12–15` items and scrolled the asset out of Flow's `Asset list` DOM viewport | Call `flow_new_project` and pass the local downloaded file path (`~/flow-mcp-out/<prev-project>/scene-NN-vX.jpeg`) in `reference_images` |
| Uploads crawl or hang; a tile sits at `99%` in Flow | A wedged `99%` tile in the current Flow project stalls composer state | Call `flow_new_project` immediately to move into a clean workspace |
| Multi-scene `reference_images` call drops attachments or errors | Nested path serialization or non-ASCII characters in local file paths | Symlink references into `~/flow-mcp-out/<slug>_refs/` (pure ASCII) and invoke `flow_generate` once per scene |
| All browser actions time out mid-batch | Display slept, screen locked, or Chrome window was closed | Keep the machine awake (`caffeinate -dimsu` on macOS) and run `cd ~/flow-mcp && npm run login` if Chrome closed |
