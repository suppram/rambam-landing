# Rambam Landing

Landing page(s) for the Rambam / Dr. Gil Yosef Shachar **open-day** event
(יום פתוח — online, live on Zoom). Static HTML, deployed via Vercel.

> **Event date:** 11.08.2026 (יום שלישי, 21:00) — edit in the files under `src/landing/`
> and, for the live copy, in `deploy/index.html`.

## Repository layout

```
.
├── deploy/            ← THE LIVE SITE. Vercel serves this folder (see vercel.json).
│   ├── index.html         the published page
│   ├── gil-fg.png         foreground image (referenced as src="gil-fg.png")
│   ├── jungle-bg.jpg      background image (referenced as src="jungle-bg.jpg")
│   └── doctor-jungle.png  (extra copy, not referenced)
│
├── src/               ← SOURCE / working files (not served directly)
│   ├── landing/           the page and its design variants + the images they embed
│   │   ├── open-day.html          main design — identical to deploy/index.html
│   │   ├── variant-1-brand.html   alternate "brand" design
│   │   ├── variant-2-minimal.html alternate "minimal" design (also == the live page)
│   │   ├── gil-fg.png             kept beside the HTML so previews work
│   │   └── jungle-bg.jpg
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
To change what's live, edit `deploy/index.html` and the images alongside it.
The files in `src/landing/` are the editable sources; `deploy/index.html` is currently
an exact copy of `src/landing/open-day.html`.

## Notes

- **Duplicates preserved on purpose:** the live page exists three times
  (`deploy/index.html`, `src/landing/open-day.html`, `src/landing/variant-2-minimal.html`)
  and the hero image twice (`assets/doctor-jungle.png` = `assets/גיל הירו.png`).
  Nothing was deleted during cleanup — only reorganized.
- **Images that must stay put:** `deploy/index.html` and the `src/landing/` pages
  reference `gil-fg.png` / `jungle-bg.jpg` as siblings (bare `src="…"`), so those images
  live in the same folder as the HTML that uses them. Don't move them without updating
  the `src="…"` paths.
