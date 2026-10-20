# The Syntax of Taste — Generative Art

**A seed-based generative collage system — catalogue edition.**

A single-page interactive website presenting *The Syntax of Taste*, a generative art system that composes 2×2 grids from a curated archive of 39 images. Each composition is driven by a pseudo-random seed, making every output reproducible, traceable, and licensable.

---

## Overview

The Syntax of Taste is an Iranian-born artist's study in algorithmic curation — a grammar of visual language where a grid, driven by chance, becomes a field of visual syntax. The site functions simultaneously as:

- A **portfolio plate** showcasing a live, interactive generative composition
- A **catalogue entry** documenting the system's structure and metadata
- A **commercial surface** presenting licensing, commission, and systems services
- A **surface preview tool** demonstrating the same seed across print, apparel, textile, and wallpaper applications

---

## Features

### Generative Engine
- **Seed-based generation** — every composition derives from a single numeric seed
- **2×2 grid composition** — four images drawn from a 39-image curated archive
- **Non-repeating selection** — images are drawn without duplication (up to 50 attempts)
- **Reproducibility** — the same seed always produces the identical composition
- **Graceful fallback** — failed image loads are replaced with generated placeholders so the layout never breaks

### Interactive Plate
- **New Seed** button (or keyboard `R` / `Space`) regenerates the composition
- **Download** button (or keyboard `S`) exports the current canvas as a PNG
- Live metadata display showing grid dimensions and archive size

### Live Surface Previews
The current composition is automatically propagated across:
- **Cover art** (hero section)
- **Framed archival plate** (Surfaces section)
- **Four surface mockups** at varying aspect ratios:
  - Print — 1:1 (cotton rag)
  - Apparel — 3:1 (silk scarf)
  - Textile — 4:3 (fabric yardage repeat)
  - Wall — 2:3 (wallpaper vertical repeat)

### Legal Modal
Accessible via footer links, covering:
- **Licensing** — seed-documented licensing terms
- **Terms** — intellectual property and usage terms
- **Credits** — design, typeface, and studio attributions

Modal supports overlay click, close button, and `Escape` key to dismiss.

### Responsive Design
- Fluid typography using `clamp()`
- Grid layouts collapse gracefully at 900px, 720px, and smaller breakpoints
- Horizontal-scrolling navigation ribbon on mobile with mask fade
- Touch-friendly control buttons
- `prefers-reduced-motion` respected

---

## Tech Stack

| Layer | Technology |
|---|---|
| Markup | Semantic HTML5 |
| Styling | Vanilla CSS with custom properties |
| Generative art | [p5.js 1.9.0](https://p5js.org/) (CDN) |
| Typography | Cormorant Garamond, DM Mono (Google Fonts) |
| Icons | Inline SVG favicon |
| Interactivity | Vanilla JavaScript (no framework) |

No build step, no dependencies to install — the page runs directly from a static file server.

---

## File Structure

```
/
├── index.html              # The complete single-page site
├── images/
│   ├── fav.svg             # Favicon
│   ├── image1-min.jpg      # Archive images (1 through 39)
│   ├── ...
│   ├── image39-min.jpg
│   ├── tote.png            # Mockup gallery assets
│   ├── tee.png
│   └── cushion.png
└── README.md
```

> **Note:** The archive requires 39 images named `image1-min.jpg` through `image39-min.jpg` in the `images/` directory. If an image is missing, the system falls back to a generated noise placeholder rather than breaking.

---

## Getting Started

1. **Clone or download** the repository.
2. **Place the archive images** (`image1-min.jpg` … `image39-min.jpg`) and mockup assets (`tote.png`, `tee.png`, `cushion.png`) in the `images/` folder.
3. **Serve the directory** with any static server:
   ```bash
   # Python
   python3 -m http.server 8000

   # Node
   npx serve .

   # PHP
   php -S localhost:8000
   ```
4. **Open** `http://localhost:8000` in a browser.

> Opening `index.html` directly via `file://` will work for the UI, but `p5.loadImage` may be blocked by browser CORS policies for local files — use a local server for full functionality.

---

## Usage

### Regenerate a composition
- Click **New Seed** in the Plate section
- Press `R` or `Space` anywhere on the page
- Resizing the window regenerates automatically (debounced 300ms)

### Download the current composition
- Click **Download** in the Plate section
- Press `S` anywhere on the page
- The exported PNG is named `Syntax_of_Taste_<hash>.png`

### Navigate
- **Ribbon** (fixed top bar) — Statement, Plate, Surfaces, Process, Commission
- **Footer colophon** — full catalogue and studio links

---

## How the Generative System Works

### Seeding
```js
let seed = Math.random() * 2147483647;

function rng() {
  seed = (seed * 9301 + 49297) % 233280;
  return seed / 233280;
}
```
A simple linear congruential generator (LCG) provides deterministic pseudo-randomness. The same starting seed yields the same sequence — and therefore the same composition.

### Grid Composition
For each of the 4 cells in the 2×2 grid:
1. Draw a random index from `0..38`
2. If that index has already been used in this composition, retry (up to 50 attempts)
3. Draw the selected image into its cell

This ensures no duplicate images within a single composition while keeping the structure simple and reproducible.

### Surface Propagation
After the main canvas is drawn, `updateSurfaces()`:
1. Captures the canvas as a data URL (`toDataURL`)
2. Injects it into the cover container and framed plate as an `<img>`
3. Applies it as a `background-image` (with `cover` sizing) to each `.live-surface` container

This means every surface preview — at every aspect ratio — is derived from the exact same rendered composition.

---

## Customization

### Change grid density
```js
const gridCols = 2;
const gridRows = 2;
```
The system generalizes to any grid size; the layout and composition logic will adapt automatically.

### Change archive size
```js
const numImages = 39;
```
Update this value and provide the correspondingly named image files.

### Adjust canvas behavior
The `p5Setup()` function uses `Math.min(rect.width, rect.height)` to size the canvas as a square that fits its container, with `pixelDensity(1)` for consistent export resolution.

### Theme
All colors, typography, and spacing are defined as CSS custom properties on `:root` — edit `--gold`, `--paper`, `--bg`, `--serif`, `--mono`, and `--page` to retheme the entire site.

---

## Keyboard Shortcuts

| Key | Action |
|---|---|
| `R` | Regenerate composition |
| `Space` | Regenerate composition |
| `S` | Download current composition as PNG |
| `Esc` | Close legal modal |

Shortcuts are suppressed when focus is inside an `input` or `textarea`.

---

## Accessibility

- Semantic HTML landmarks (`header`, `section`, `footer`, `nav`, `aside`)
- `aria-modal`, `aria-hidden`, and `aria-labelledby` on the legal dialog
- `aria-label` on the modal close button
- Focus states preserved on interactive elements
- `prefers-reduced-motion` disables animations and smooth scrolling
- Sufficient color contrast on primary text against dark backgrounds

---

## Browser Support

Modern evergreen browsers — Chrome, Firefox, Safari, Edge. Requires:
- CSS custom properties
- CSS Grid and Flexbox
- `clamp()` for fluid typography
- `aspect-ratio`
- `backdrop-filter` (graceful degradation on unsupported browsers)
- Canvas 2D API

---

## Credits

**Design & Generative System**
Reyhaneh Daneshdoost

**Typefaces**
Cormorant Garamond · DM Mono

**Platform**
Reyrove Studio

**Edition**
The Syntax of Taste — Autumn 2026

---

## On AI Tools

Where technical obstacles arose, AI tools were consulted for debugging and code optimization. Every structural, aesthetic, and conceptual decision remained the artist's own — as stated in the catalogue's artist statement.

---

## Licensing & Contact

All compositions are seed-documented and available for licensing across apparel, surface, and print applications. Standard licenses cover single-product production runs; commercial, custom, and exclusive rights are available by arrangement.

- **Email:** [reyhanehdaneshdoost@gmail.com](mailto:reyhanehdaneshdoost@gmail.com)
- **Website:** [reyrove.github.io](https://reyrove.github.io/)
- **Instagram:** [@rey._.rove](https://www.instagram.com/rey._.rove/)
- **LinkedIn:** [Reyhaneh Daneshdoost](https://www.linkedin.com/in/reyhaneh-daneshdoost-730481160/)
- **X:** [@reyrove](https://x.com/reyrove)

---

*© The Syntax of Taste · All compositions reproducible by seed.*