# qr-botanical

Standalone Three.js prototypes where the **top-down view is a scannable QR code** and tilting reveals a **3D voxel plant**. Inspired by Reactiive's `cherry-blossom-qrcode` demo (which is React Native + WebGPU); these are reimplemented in browser Three.js so they run in Chrome and Safari.

Open any file directly, or serve the folder (`python3 -m http.server`) and visit it. Each has a live URL input and a tap-to-flatten toggle.

## Combined showcase

- **`qr-botanical.html`** — the production-ready single page. One Three.js renderer/camera/loop with a switcher for **caladium · H**, **caladium · M** and **pine · M** (the pine keeps its forest/snow toggle). Shared URL input + flatten toggle, accent/background shift per plant, responsive (mobile controls wrap), Safari + Chrome. Each builder emits a common `{ groups, view }` shape — `groups` carry `fade` (pot/trunk/overhang fade when flat) and `sway` (the plant body sways in iso) flags — so the harness stays generic. Geometry and per-module colours are ported verbatim from the keepers below; only lighting/roughness are unified. **All four reachable flat states are jsQR decode-verified.** Rename to `index.html` to host it at a repo root.
  - **Export** — *save qr* writes a 1080² scannable PNG of the flat top-down; *record loop* captures a seamless **top-down → grow → 360° orbit → flatten** video (mp4 in Safari, webm in Chrome, via `MediaRecorder` + `captureStream`); *save gif* encodes the same loop client-side with `gif.js` (480², loops inline anywhere a video won't). All three render through a square offscreen renderer + a 2D compositor. The loop's two endpoints are the identical flat frame (and azimuth returns to 0, not 2π) so it loops with no seam or extra un-spin. Every flat PNG, recorded-mp4 flat frame and GIF flat frame is jsQR decode-verified for all four plant states.
  - **Why some exports get a contrast lift (and why it's *not* error correction):** as a standalone image the low-contrast designs — caladium·M and the snowy pine — wouldn't decode raw, even though they scan fine live. Investigation (per-module luminance vs the true matrix, plus hard-binarise-then-decode tests) showed this is **not** a Reed-Solomon / error-budget problem: pine-snow has **zero** mis-classified modules yet failed raw, and a pure black/white threshold decodes it — so the data was always intact. The real failure is in jsQR's **binarisation / finder-location** stage: when the "dark" tones sit at mid-luminance (the snowy-ground shadow grey ≈ 0.5; the caladium leaf-mottle veins/blades at 0.4–0.5) and the whole image is low dynamic range, the locator can't separate modules even though they're technically distinct. A gentle contrast lift (which lets jsQR run its own *local adaptive* threshold with more range) fixes it; a single global threshold does not (it can't resolve the caladium's sub-module mottle texture). So the boost is **per-model and minimal** — `1.0` (none) for caladium·H and forest pine, which scan raw and export exactly as shown on screen; `1.5` only for caladium·M and snowy pine. It applies at the flat (scannable) frame and ramps to none by full iso, so the 3D orbit always looks natural. jsQR's decode floor is ≈`1.2`; `1.5` leaves headroom for real-phone scanning.

## Builds

### Caladium
- **`caladium-qr-match.html`** — potted caladium, **geometry-matched**: leaves climb a central stem (golden-angle spiral), thin (1 voxel), each coloured by the module beneath it so the variegation *is* the QR. The exposed top facet per cell carries the code; occluded under-leaves stay botanical (magenta midrib → pink blade → veins → green margin). Higher-res QR (level H), monotone floor, terracotta pot, gentle sway. **Flat state is jsQR decode-verified.**
- **`caladium-qr-fit.html`** — H, **fit + cohesive**: the plant is *scaled* (not clipped) so every whole leaf stays inside the tile's inscribed circle ⊂ the square, for any QR — no overhang. Every leaf cell is mottled by the module under it (exposed tops = real QR, lower leaves = matching decorative "mock" mottle) so the whole plant reads as one cohesive variegated thing, no botanical-vs-codey seam. Bigger leaves (small base offset), bigger pot scaled to the grid.
- **`caladium-qr-fit-v2.html`** — iteration on `-fit`: **per-leaf square fit** — each leaf grows to the *square* edge in its own direction (not the inscribed circle), so corner-pointing leaves get bigger and the plant fans out to fill the tile. More leaves. Still no overhang.
- **`caladium-qr-m-fit.html`** / **`caladium-qr-m-fit-v2.html`** — the fit + cohesive caladium at **error-correction level M** (chunkier, calmer mottle); `-v2` has the same fan-out.

> **Versioning:** every visual iteration is kept as its own `…-vN.html` so they can be compared side by side — nothing is overwritten.
- **`caladium-qr-nooverhang.html`** — earlier attempt: leaves hard-*clipped* to the square (edge leaves trimmed). Superseded by `-fit` (which scales instead of cutting).
- **`caladium-qr-m.html`** — matched caladium at level M with overhang.
- **`caladium-qr.html`** — earlier elegant version with freely arching leaves that *fade* on flatten (palette-matched, not geometry-matched).

### Pine
- **`pine-tree-qr.html`** — single pine, now **geometry-matched** like the caladium: a conical canopy of grid-aligned voxels whose topmost needle per cell is dark or pale green by the module under it, so the QR shows *in the 3D tree* and the top-down scans (jsQR decode-verified). A monotone forest floor carries the QR outside the canopy. No snow.
- **`pine-tree-qr-toy-matched.html`** — the 4-tiered fir, **matched**: needles are dark/pale green by the module under each, so the canopy carries the QR (decode-verified). forest/snow colour toggle, drag-to-orbit, canopy sway — no falling particles.
- **`pine-tree-qr-toy-matched-v2.html`** — same, but the light-module needles are a **fresh yellow-green** (new-growth) instead of grey-sage, so the dapple reads as healthy foliage, not die-back. (v1's grey-green looked like a dying tree.)
- **`pine-tree-qr-toy.html`** — original tiered fir toy with a forest/snow switch and falling needles/snow (not matched).
- **`pine-tree-qr-compare.html`** — single cone vs tiered fir vs snow, side by side.

## Ideas / backlog (June 2026 dump)

- **CJ Hendry flower-market / flower-shop theme** — lean a set of botanical QR codes into the CJ Hendry "Flower Market" pop-up aesthetic (lush, hyperreal, flower-shop styling) as a visual direction / framing, not just single plants. Could tie the codes to a flower-market concept page.
- **Vanda Miss Joaquim orchid** — botanical QR build of Singapore's national flower. Grid-aligned voxel orchid; matching challenge is the slender, asymmetric petals — express tilt via per-cell *height* only (never sideways displacement), per the matching rule below. Mauve/violet petals whose brightness carries the modules.
- **Frangipani tree** — botanical QR build of a frangipani (plumeria): trunk + broad canopy of 5-petal blooms; warm cream/white petals with a yellow centre, bloom brightness carrying the modules (light blooms = light modules), foliage for the darker ones.

## How the matching works

A QR scanner converts the image to greyscale and thresholds it — it only reads **brightness**, never hue. So any colour can stand in for a module as long as its brightness matches (dark colour for a dark module, light for a light one). That's why the pink/green/cream variegation scans. Higher error-correction (level H) gives more fault tolerance for the artistic colouring.

The non-negotiable for matching: every plant voxel must be **grid-aligned** (locked to its `(col, row)`). Tilt/arch is expressed through per-cell *height*, not sideways displacement — otherwise the top-down projection turns to noise.
