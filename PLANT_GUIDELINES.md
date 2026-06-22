# Botanical QR — plant design guidelines

Compiled from every preference pz stated across **caladium, frangipani, pine·forest, pine·snow**.
Every new plant type (pitcher, ginger, orchid, …) must honour **all** of these. This is the checklist a plant must pass before it is "shippable".

---

## 1. The QR ↔ 3D contract (non-negotiable)

- **True top-down projection — NEVER fade.** The flattened top-down view is a pixel-for-pixel projection of the exact 3D model. Flatten/grow is **only a camera move**. Never fade or morph the pot, trunk, stems, pitchers — anything. (Stated 3+ times.)
- **Grid-aligned, height-only.** Every voxel stays on its `(col,row)`. The 3D shape comes from per-cell **height** alone — a cell never moves sideways off its module. Tilt/arch = height, never lateral.
- **Topmost cell carries the module.** At each `(col,row)` the highest (top-down-visible) cell is coloured by that module — dark colour for a dark module, light for light. Scanners read luminance only, so any hue works.
- **Cohesion — colour EVERY cell by its module, even occluded ones.** Hidden/lower cells get the *same* mock mottle in the same palette (an "occluded" variant of the light/dark pair), so the whole plant reads cohesively with no botanical-vs-codey seam. **Do NOT random-speckle occluded cells.**
- **Solid accents are a deliberate, budgeted exception.** A naturally-solid element (frangipani bloom, lime peristome) may be solid **only if** it's small and placed over the QR's **lightest** cells, so error-correction absorbs it. Matched-colouring only looks botanical when the element is naturally ~half-light/half-dark (variegation, dapple, mottle). Always surface this tradeoff.

## 2. Colour & palette

- **Soft, tasteful palettes only.** No garish contrast, no crisp high-contrast palette swaps, no global contrast boost for looks, no purple gradients. (pz hard-rejected a crisper forest palette — "ew, horrible" — and a contrast boost — garish orange.)
- **Decode-contrast spec:** every dark-module tone ≲ 90 luminance, every light-module tone ≳ 180.
- **Export contrast boost is per-model, minimal, robustness-not-looks** — 1.0 where it scans raw (export == on-screen); a small lift only where a low-contrast palette needs it (cal-M / pine-snow ≈1.5–2.0; pine-forest over-capacity fallback 4.0). Never a uniform global boost.

## 3. Geometry & realism

- **No clipping / fit-in-square.** Whole leaves/elements stay inside the tile — never snipped at the boundary. Scale to fit (per-leaf fan-out fit is the chosen approach).
- **No intersecting in 3D.** Elements may **touch** on the flat projection but must **never intersect / pass through** each other in 3D (caladium's golden-angle separation). De-intersection logic is required.
- **Potted, not a tree** (pitcher-specific): the pitcher plant grows from a **pot** like the caladium (terracotta pot + uniform soil — solid, no per-module speckle), not a bare trunk.
- **Levitating pitchers** (pitcher-specific): pitchers **hang from tendrils** off the leaf tips — they float above the ground/soil, do not sit on it, and do not intersect the rosette.
- **Genuinely different per URL.** Leaf count, angles, arrangement and colourway are seeded from the matrix hash → every code is a distinct plant, not a rotation of one.
- **Realism from REAL references.** Study multiple actual photos; don't guess. Match the real silhouette, proportions and colour.
- **Shape rules learned on frangipani** (general): leaves **few & distinct & non-overlapping**, spread **out + gently up** — not steep, not drooping/fountain; blooms as a posy at the centre.

## 4. Decode & testing standard (a plant is not done until this passes)

- **Decode-verify the FLAT state with jsQR on the real full-res 1080² exported artifact** (`qrBotanical.frame(0,0)` / `renderExportFrame`), never the low-res live canvas (false fails).
- **Stress test:** ≥ **200 URLs of varying lengths/shapes**, plus the exhaustive **600-URL version-bucketed dead-zone sweep to 1000 chars** → **0 failures, 0 dead-zones**. Bucket by QR version; pin a dead-set (`qrMatrixSafe`) skipping any version that fails under the canopy.
- **Pin the QR version + dead-set.** Sparse/coarse plant → level **M** (lower version, coarser, less speckle, more scannable). Dense fixed-version plant → level **H** (keeps the decode margin). Use v7+ (≥6 alignment patterns) so the centre isn't one obscured pattern.
- **Real phone scan of an exported PNG** is the human gate (jsQR-on-canvas is more forgiving than a camera). Safari especially.

## 5. Workflow & process

- **Versioning — NEVER iterate in place.** Every visual iteration is a **new numbered file** (`pitcher-v1.html`, `-v2.html`, …) so old vs new can be opened side by side. Label the header eyebrow with the version. (Reinforced twice.)
- **Demo-before-decide.** pz judges by eye on rendered output in **Safari** — build a standalone HTML page he opens; never rely on described or inline-rendered images.
- **Common builder shape.** Each plant builder returns `{ groups, view }` (groups carry `fade` + `sway` flags) so the one renderer/camera/loop stays generic; integrate as a new model in `botanical-qr.html`.
- **British English, no auto-caps** in commits/comments. No AI mentions or co-author lines in commits.
- **Signed exports.** Footer `pyn/zhern` logo band below the code (outside the quiet zone) + invisible PNG `tEXt` copyright. Don't call a plant shipped without the export path the others have.
- **Don't ship until pz validates.** "Shippable" = built + rigorously tested + all of the above honoured. The actual integrate/deploy waits for pz's eyeball.

---

_Source of truth for plant work. Update when pz states a new preference._
