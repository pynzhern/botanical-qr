# qr-botanical

Standalone Three.js prototypes where the **top-down view is a scannable QR code** and tilting reveals a **3D voxel plant**. Inspired by Reactiive's `cherry-blossom-qrcode` demo (which is React Native + WebGPU); these are reimplemented in browser Three.js so they run in Chrome and Safari.

Open any file directly, or serve the folder (`python3 -m http.server`) and visit it. Each has a live URL input and a tap-to-flatten toggle.

## Builds

### Caladium
- **`caladium-qr-match.html`** — potted caladium, **geometry-matched**: leaves are grid-aligned voxel sheets, one per QR cell, arched by varying each cell's height (never moving it sideways) so the top-down projection *is* the QR. Each cell is coloured by the module beneath it — dark module → deep pink/green, light → pale — so the leaf variegation encodes the code. Only the exposed top facet per cell carries the QR; occluded under-leaves are painted organic botanical variegation, so the plant reads lush rather than code-like. Overhang lives in a separate mesh that fades when flat (clean square). Green petioles, terracotta pot, drifting petals, gentle sway. **Flat state is jsQR decode-verified.**
- **`caladium-qr.html`** — earlier elegant version with freely arching leaves. Prettier silhouette, but the leaves *fade* on flatten (palette-matched, not geometry-matched — the flat QR is read from the ground layer).

### Pine
- **`pine-tree-qr-toy.html`** — tiered fir with a forest/snow switch, drag-to-orbit, canopy sway, and falling needles/snow.
- **`pine-tree-qr-compare.html`** — single cone vs tiered fir vs snow, side by side.
- **`pine-tree-qr.html`** — single pine: flat QR ground tile that rises into an isometric voxel tree.

## How the matching works

A QR scanner converts the image to greyscale and thresholds it — it only reads **brightness**, never hue. So any colour can stand in for a module as long as its brightness matches (dark colour for a dark module, light for a light one). That's why the pink/green/cream variegation scans. Higher error-correction (level H) gives more fault tolerance for the artistic colouring.

The non-negotiable for matching: every plant voxel must be **grid-aligned** (locked to its `(col, row)`). Tilt/arch is expressed through per-cell *height*, not sideways displacement — otherwise the top-down projection turns to noise.
