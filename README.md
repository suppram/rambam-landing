# Rambam Landing

Landing page(s) for the Rambam / Dr. Gil Yosef Shachar **open-day** event
(יום פתוח — online, live on Zoom). Static HTML, deployed via Vercel.

> **Event date:** 11.08.2026 (יום שלישי, 21:00) — edit in `deploy/index.html`
> (the single source of the live page).

## Repository layout

```
.
├── deploy/            ← THE LIVE SITE and the page's single source of truth.
│   │                     Vercel serves this folder (see vercel.json).
│   ├── index.html         the published page — edit HERE
│   ├── gil-fg.png         foreground image (referenced as src="gil-fg.png")
│   └── jungle-bg.jpg      background image (referenced as src="jungle-bg.jpg")
│
├── src/               ← SOURCE / working files (not served directly)
│   ├── landing/
│   │   └── variant-1-brand.html   alternate "brand" design (unused; images load remotely)
│   ├── elementor/         WordPress / Elementor embed exports
│   │   ├── elementor-embed.html
│   │   ├── elementor-embed-iframe.html
│   │   └── elementor-iframe-snippet.html
│   ├── components/
│   │   └── leaf-particle.html     reusable falling-leaves particle effect
│   └── styles/
│       └── smoove-custom.css      custom CSS for the Smoove signup form
│
├── assets/            ← ORIGINAL design source images (high-res / layered exports).
│   │                     None are referenced by the pages — kept for future edits.
│   ├── doctor-jungle.png
│   ├── גיל הירו.png       "Gil hero" — same image as doctor-jungle.png
│   ├── גיל נפרד.png       "Gil, separated" layer
│   └── רקע נפרד.png       "Background, separated" layer
│
├── docs/
│   └── ADMIN-SPEC.md      Hebrew spec for a WordPress landing-page manager plugin
│
└── vercel.json        deploy config → outputDirectory: "deploy"
```

## Deploying

Vercel publishes the **`deploy/`** folder (`vercel.json` → `"outputDirectory": "deploy"`).
To change what's live, edit `deploy/index.html` and the images alongside it —
it is the **only** copy of the live page (the former identical copies in
`src/landing/` were removed; see git history if an old variant is needed).

## Notes

- **Hero image duplicate in assets/:** `assets/doctor-jungle.png` = `assets/גיל הירו.png`
  (same image, two names) — kept as-is; they're design sources, not served.
- **Images that must stay put:** `deploy/index.html` references
  `gil-fg.png` / `jungle-bg.jpg` as siblings (bare `src="…"`), so those images
  live in the same folder as the HTML. Don't move them without updating
  the `src="…"` paths.
