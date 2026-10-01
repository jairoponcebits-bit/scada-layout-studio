<div align="center">

# SCADA Layout Studio

**A browser-based equipment layout editor for SCADA / HMI / industrial plant diagrams.**

Drag, resize, rotate, multi-select and export industrial equipment to a
1080p landscape SVG or PNG — no build step, no server, no dependencies.

[![License: MIT](https://img.shields.io/badge/License-MIT-10b981.svg)](LICENSE)
[![No Build](https://img.shields.io/badge/build-none-10b981.svg)](#-quick-start)
[![Vanilla JS](https://img.shields.io/badge/vanilla-JS-f7df1e.svg)](#-tech-stack)
[![Canvas](https://img.shields.io/badge/canvas-1920%C3%971080-10b981.svg)](#-canvas)

</div>

---

## ✨ Features

### Canvas & Viewport
- **1920 × 1080 landscape artboard** with a visible green outline
- **Ctrl / ⌘ + Scroll** — zoom toward the cursor, point-perfect
- **Alt + Drag** (or middle mouse) — pan the canvas
- **Fit / − / +** zoom dock with live percentage readout
- Optional **grid overlay** and **snap-to-grid** (5 px) toggles

### Equipment
- Import the eight SCADA equipment assets directly from `scada_mixers.html`
- Click a palette entry to place; **Shift + Click** to add to the selection
- **Drag a marquee** on empty canvas to select everything it touches
- Move, resize (proportional corner handles) and rotate (top handle)
- **Shift + rotation** snaps to 15° increments
- Flip horizontally or vertically per item

### Multi-Select Group Editing
- Selection bounding box with **corner resize** and **rotation** handles
- Dragging any selected item moves **the entire group**
- **Duplicate all**, **Delete all**, **Flip H/V all** on a multi-selection
- Marquees respect **Shift** to add to the current selection

### Compact Name Tags
- Every placed item gets a name tag — default **`PE-PS-888`**
- Bold, zero-padding, monospace — reads like a real SCADA nameplate
- **Double-click** any tag to rename inline
- Drag the tag freely; **Center above** snaps it neatly over the equipment
- Tag size is adjustable from 6 to 72 px
- Tags render into SVG and PNG exports at their exact positions

### History
- **Undo** (⌘ Z) and **Redo** (⇧ ⌘ Z / Ctrl + Y)
- Every gesture — drag, resize, rotate, rename, reorder, background change —
  collapses into a single history entry
- Up to **120 steps** retained

### Project & Export
- **Save / Open** the layout as a `.json` project file
- **Autosave** to `localStorage` every 3 seconds
- **Export SVG** — clean, standalone, no JavaScript
- **Export PNG** — 1920 × 1080 bitmap with the current background colour
- Background colour picker (any hex)

### Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl / ⌘ + Scroll` | Zoom toward cursor |
| `Alt + Drag` | Pan canvas |
| `Click` | Select item |
| `Shift + Click` | Toggle item in selection |
| `Drag` (empty) | Marquee select |
| `Shift + Drag` (empty) | Add marquee to selection |
| `Double-click` tag | Rename |
| `Arrow keys` | Nudge 1 px |
| `Shift + Arrow` | Nudge 10 px |
| `⌘ / Ctrl + A` | Select all |
| `⌘ / Ctrl + D` | Duplicate selection |
| `⌘ / Ctrl + Z` | Undo |
| `⇧ ⌘ / Ctrl + Z` | Redo |
| `Ctrl + Y` | Redo (alt) |
| `Delete / Backspace` | Remove selection |
| `Esc` | Clear selection |
| `Shift + rotate` | Snap rotation to 15° |

---

## 🚀 Quick Start

### Option 1 — Open locally (fastest)

1. Download `scada-layout-studio.html` and `scada_mixers.html` into the same folder.
2. Double-click `scada-layout-studio.html`.
3. The editor asks for `scada_mixers.html` — pick it.
4. Start placing equipment.

> Nothing is uploaded. The equipment file is parsed entirely in your browser.

### Option 2 — Serve it (auto-load)

Any static server will let the editor auto-fetch the sibling `scada_mixers.html`:

```bash
# Python 3
python -m http.server 8080

# Node
npx serve .

# PHP
php -S localhost:8080
```

Then open <http://localhost:8080/scada-layout-studio.html>.

### Option 3 — GitHub Pages

1. Push both HTML files to your repo.
2. **Settings → Pages → Source: main / root**.
3. Open `https://<user>.github.io/<repo>/scada-layout-studio.html`.
4. Optional: rename to `index.html` so the demo loads at the repo root.

---

## 📁 Project Structure

```
scada-layout-studio/
├─ scada-layout-studio.html   ← the editor (single file, no build)
├─ scada_mixers.html          ← equipment library (8 assets + <defs>)
├─ README.md
├─ LICENSE
└─ .gitignore
```

Everything in the editor — UI, styles, logic — lives inside the single
`scada-layout-studio.html` file. There is no bundler, no npm install, no
transpilation step.

---

## 🧠 How It Works

### Equipment import

The editor uses `DOMParser` to read `scada_mixers.html`, extracts the shared
`<defs>` block (filters, gradients, masks, clip-paths) and clones each
`.equipment-concept` group. Assets are cached in `localStorage` so subsequent
loads are instant.

### Rendering

All placed items live inside one big `<svg>` element. Each item is:

```html
<g class="item" data-id="…" transform="translate(x y) rotate(…)">
  <g transform="scale(…) translate(-vx -vy)">
    <rect fill="transparent" pointer-events="all"/>
    <!-- equipment artwork -->
  </g>
</g>
```

Name tags are drawn into a separate `<g id="labels-layer">` so they always
appear above the artwork without disturbing the equipment stack order.

### Selection overlay

A dedicated `<g id="overlay">` draws the green selection rectangle, corner
handles, rotation handle and (for multi-select) per-item outlines. The overlay
is sized in canvas units divided by the current zoom so handles stay a
consistent size on screen.

### History

A `serializeState()` snapshot captures background + every item's position,
size, rotation, flips and label. Gestures push one snapshot on pointer-up;
inspector edits are debounced. `applySnapshot()` restores the whole scene.

### Export

`buildExportSVG()` clones the shared `<defs>`, paints an opaque background
rect, wraps content in a `clipPath` matching the artboard, and appends every
item plus its label. PNG export rasterises that SVG through an `<img>` and a
2D canvas.

---

## 🎨 Customisation

Open `scada-layout-studio.html` and edit the constants at the top of the
`<script>` block:

| Constant | Purpose | Default |
|---|---|---|
| `CW` | Canvas width in px | `1920` |
| `CH` | Canvas height in px | `1080` |
| `MARGIN` | Visible margin outside the artboard | `240` |
| `SNAP` | Snap increment in px | `5` |
| `MIN_W` | Minimum item width | `20` |
| `DEFAULT_TAG` | Default name-tag text | `"PE-PS-888"` |
| `HISTORY_LIMIT` | Max undo steps | `120` |
| `MIN_GROUP_SCALE` | Group-resize floor | `0.1` |

Colours live in the `:root { … }` CSS block — swap `--accent` to restyle the
whole UI.

---

## 🌐 Browser Support

| Browser | Status |
|---|---|
| Chrome / Edge 90+ | ✅ Full |
| Firefox 88+ | ✅ Full |
| Safari 14+ | ✅ Full |
| Mobile Safari / Chrome | ✅ Works, desktop-first UI |

Uses `PointerEvent`, `ResizeObserver`, `createSVGPoint`, `matrixTransform`,
`SVGElement.getScreenCTM` — all baseline widely available.

---

## 🤝 Contributing

Issues and PRs are welcome.

```bash
git clone https://github.com/<you>/scada-layout-studio.git
cd scada-layout-studio
python -m http.server 8080
```

Open <http://localhost:8080>, make your change, test all eight equipment
types, then submit a PR.

---

## 📄 License

Released under the **MIT License** — see [LICENSE](LICENSE).

---

## 👤 Author

**Jairo Ponce**

Built with ♥ for the SCADA and industrial automation community.

---

<div align="center">
<sub>If this saved you time, a ⭐ on the repo is appreciated.</sub>
</div>
