# dimension-preview

A web-based preview viewer, a tool for Minecraft.

Select values in a form (MCStacker-style) and see the result rendered live in 3D
in the browser — no game client required. Rendering is done with Three.js (WebGL);
shader-style looks (shadows, lighting, tone mapping) are approximated in the browser.

## Status
Early scaffold. Blocks are rendered with placeholder colors; real textures TBD.

## Live demo
https://toggi0991.github.io/dimension-preview/

## Files
- `index.html` — single-file app (form panel + Three.js 3D canvas)
- `iris-dimension-preview-notes.md` — research & direction notes (incl. usage-policy findings)

## Run
Open `index.html` in a browser, or visit the live demo above. Requires an internet
connection (Three.js is loaded from a CDN via importmap).

## Notes
This is an unofficial fan project. "Minecraft" is used only in a secondary,
descriptive sense; no official Minecraft branding or assets are included.
See `iris-dimension-preview-notes.md` for details.
