# qr-botanical

Standalone Three.js prototypes where the **top-down view is a scannable QR code** and tilting reveals a **3D voxel plant**. Inspired by Reactiive's `cherry-blossom-qrcode` demo (which is React Native + WebGPU); these are reimplemented in browser Three.js so they run in Chrome and Safari.

Open any file directly, or serve the folder (`python3 -m http.server`) and visit it. Each has a live URL input and a tap-to-flatten toggle.

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
- **`pine-tree-qr-toy.html`** — original tiered fir toy with a forest/snow switch and falling needles/snow (not matched).
- **`pine-tree-qr-compare.html`** — single cone vs tiered fir vs snow, side by side.

## How the matching works

A QR scanner converts the image to greyscale and thresholds it — it only reads **brightness**, never hue. So any colour can stand in for a module as long as its brightness matches (dark colour for a dark module, light for a light one). That's why the pink/green/cream variegation scans. Higher error-correction (level H) gives more fault tolerance for the artistic colouring.

The non-negotiable for matching: every plant voxel must be **grid-aligned** (locked to its `(col, row)`). Tilt/arch is expressed through per-cell *height*, not sideways displacement — otherwise the top-down projection turns to noise.
