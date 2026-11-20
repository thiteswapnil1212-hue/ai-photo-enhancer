# ai-photo-enhancer

A static single-page **photo enhancer demo** — upload a photo, get a brighter, sharper version, and download it. Everything runs in the browser; nothing is uploaded anywhere.

**Live demo:** https://ai-photo-enhancer-thiteswapnil1212-hues-projects.vercel.app/

> **Not AI.** Despite the "Strangers AI" name shown on the page, there is no AI model and no API call anywhere in this project. The enhancement is a fixed set of Canvas pixel filters (brightness, contrast, saturation, gamma, sharpen) applied with `getImageData` / `putImageData`. The name is branding only.

## What it does

- **Upload a photo** from your device (`<input type="file" accept="image/*">`)
- **Enhance it automatically** — one fixed preset, no sliders to adjust
- **Compare before / after** — drag the red line across the photo to reveal the original on one side and the enhanced version on the other
- **Download the result** as `enhanced.png`
- **Reset** to start over with a different photo

## How to use it

1. Open the [live demo](https://ai-photo-enhancer-thiteswapnil1212-hues-projects.vercel.app/) — or open `index.html` in any browser, no build step needed.
2. Choose a photo with the file picker.
3. Wait for the short loading animation, then drag the red line to compare the original with the enhanced version.
4. Click **Download Enhanced Image** to save the result, or **Reset** to try another photo.

## What the enhancement actually does

The whole pipeline is one fixed preset in `enhanceImage()` — the same adjustments for every photo:

| Step | What happens | Value |
|---|---|---|
| Auto brightness | Brightness is nudged up or down based on the photo's average brightness | `1 + (128 − avg) / 256 × 0.2` |
| Contrast | Pivots every channel around 128 | × 1.1 |
| Saturation | Pushes each channel away from its grey value | × 1.08 |
| Gamma | Lightens mid-tones slightly | 1.03 |
| Highlight boost | Bright channels get a lift | +10 above 200 |
| Shadow boost | Dark channels get a lift | +8 below 50 |
| Black point | Near-black channels are deepened | −5 below 20 |
| Vignette | Edges are darkened toward the corners | strength 0.12 |
| Sharpen | 3×3 convolution kernel | `[0,-1,0, -1,5,-1, 0,-1,0]` |

Every value is clamped to 0–255. There are **no user-adjustable controls** — the enhancement is not tunable; it is one preset applied the same way every time.

## How it works

```mermaid
flowchart LR
    User([User]) --> Upload["Upload photo<br/>FileReader.readAsDataURL"]
    Upload --> Canvas["Canvas processing<br/>getImageData → pixel filters"]
    Canvas --> Filters["Fixed preset<br/>brightness · contrast · saturation<br/>gamma · highlights · shadows<br/>vignette · sharpen"]
    Filters --> Preview["Before / after preview<br/>two stacked canvases +<br/>draggable red slider (clip)"]
    Preview --> DL["Download enhanced image<br/>toDataURL → enhanced.png"]
```

The page is a single `index.html` (12,949 bytes). The original and enhanced versions live on two stacked canvases; the enhanced one is clipped with CSS `clip: rect(...)`, and dragging the red handle moves the clip edge. A 5-second loading overlay ("STRANGERS AI EDITING...") plays while the image is processed.

## Tech stack

| Layer | Choice |
|---|---|
| App | Single static `index.html` — no framework, no build step, no backend |
| Image processing | Canvas 2D API (`getImageData` / `putImageData`, per-pixel loops) |
| Fonts | Google Fonts — Bebas Neue + Orbitron |
| Styling | Inline CSS, dark red/black theme, responsive down to 400 px |
| Hosting | Vercel (static) |

## Project structure

```text
index.html     The whole app — markup, CSS and JavaScript in one file
README.md      This file
```

That is the entire repo: two files, no dependencies to install, no `package.json`.

## Run locally

```bash
# No install, no build — just open the file:
open index.html
# or serve it with any static server:
npx serve .
```

## Honest limits

- **No AI** — no model, no API key, no network call for the enhancement. The "AI" in the page title is branding.
- **No adjustable settings** — brightness, contrast and the rest are fixed values in the code, not sliders.
- **No backend, no upload, no storage** — your photo never leaves your browser.
- **PNG-only download** — the result always saves as `enhanced.png`, whatever the input format was.
- **Slow on very large photos** — the enhancement loops over every pixel twice in JavaScript, so big images take noticeably longer.
- **Background image is hotlinked** — the page background loads from a Bing image URL (`tse1.mm.bing.net`); if that link dies, the background breaks (the enhancer itself still works).
- **Fixed 5-second loading animation** — the overlay plays for 5 seconds regardless of how fast the processing actually finishes.
