# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project overview

This repository contains a small, static personal website (HTML/CSS with a bit of inline JavaScript). There is no build system, framework, or backend; pages are served as-is. A `CNAME` file (`maximnota.dev`) suggests deployment via a static host such as GitHub Pages.

The site is organised around a polished homepage at `index.html` plus several content pages under `pages/`. Shared layout and styling live in `style.css` and `navbar.css`, with a few older, page-specific CSS files for experiments.

## How to run and work on the site

There are no dependencies or build steps defined in this repo.

- **Serve locally (simple static server)**
  - From the repo root:
    - Python (available by default on macOS):
      - `python3 -m http.server 8000`
    - Then open `http://localhost:8000/` in a browser to view `index.html`.
- **Open directly from the filesystem**
  - You can also open `index.html` directly in a browser without running a server, though some browser features work more consistently via `http://`.
- **Build step**
  - There is no build step; the "build" is just the contents of this directory.
- **Tests and linting**
  - No automated tests, linters, or formatters are configured in this repo. If you need HTML/CSS/JS validation, use external tools (e.g. browser devtools, online validators) or add explicit tooling and document it here.
- **Git & large files**
  - `.gitattributes` configures Git LFS for `videos/IMG_3498.mov`. If you add more large binary assets (especially videos), consider also tracking them via Git LFS.

## High-level architecture and structure

### Top-level layout

- **Entry point**: `index.html` is the main landing page and sets the overall design direction:
  - Uses a sticky `<header class="site-header">` with a flex-based `.header-inner` container and `.main-nav` navigation (styled primarily via `navbar.css` and `style.css`).
  - Hero section introducing the student, with a two-column layout (intro text + profile card) using inline grid styles.
  - "Selected pages" section that highlights key subpages (e.g. Library, Space gallery, Links) without hard-wiring every page into the homepage.
  - A simple contact section linking to a `mailto:` address.
  - A tiny inline script updates the `#year` element in the footer with the current year.

- **Subpages under `pages/`**
  - Modernised subpages (`personal.html`, `alllinks.html`, `library.html`, `space.html`) follow the same basic structure as `index.html`:
    - Shared header (`.site-header`, `.header-inner`, `.brand`, `.main-nav`).
    - A main content area wrapped in `.container` / `.hero` / `.projects` sections.
    - A footer mirroring the homepage, often with the same `#year` script.
  - Older/legacy pages (`good_info.html`, `herobrine.html`, `photo-animals.html`, `animations.html`, parts of `watch-videos.html`) use simpler `<ul>`-based nav and ad-hoc markup. They still work but are less consistent with the newer design.

When editing or adding pages, prefer matching the newer layout used by `index.html` and the newer `pages/*.html` files, keeping the header/footer patterns and container classes consistent.

### Styling and theming

- **Global stylesheet (`style.css`)**
  - Defines CSS custom properties (`--bg`, `--card`, `--muted`, `--accent`, etc.) and base typography.
  - Provides a layout system via `.container`, `.hero`, `.hero-inner`, `.hero-card`, `.projects`, `.cards`, `.card`, `.profile`, and related utility classes.
  - Implements responsive behaviour using CSS grid and media queries (e.g. hero two-column layout on wider screens, multi-column card grids).
  - Includes styles for the global header (`.site-header`, `.header-inner`, `.brand`), main navigation (`nav.main-nav`), buttons (`.btn`, `.btn.ghost`, `.btn.small`), quick links, forms, and the footer (`.site-footer`).
  - Declares a dark-theme variant via `body.dark` that overrides core color variables. There is currently no JS toggle in this repo; applying the `dark` class manually to `<body>` will switch themes.

- **Navigation stylesheet (`navbar.css`)**
  - Focuses on the `.main-nav` styles and assumes the color variables from `style.css`.
  - Provides hover, focus, and active states, including an `[aria-current="page"]`/`.active` styling and small-screen horizontal scrolling behaviour.

- **Page-specific / legacy styles**
  - `Funnyvids.css`, `backgroundimage.css`, `goodinfo.css`, and `alllinkscolor-font.css` are older, narrowly scoped styles used by some of the legacy content pages.
  - When modernising those pages, prefer reusing components and variables from `style.css` instead of adding more ad-hoc styles.

### JavaScript usage

There is no dedicated JS bundle or module system; all JavaScript is inline within individual HTML files.

- **Utility snippets used across newer pages**
  - A small IIFE on `index.html`, `alllinks.html`, `library.html`, `space.html`, `personal.html`, and others updates a footer `#year` span to the current year. These scripts assume that an element with `id="year"` exists in the footer.

- **Legacy cookie-based greeting**
  - Some older pages (`animations.html`, `good_info.html`, `herobrine.html`, `photo-animals.html`, `watch-videos.html`) define `setCookie`, `getCookie`, and `checkCookie` helpers, then call `checkCookie()` via `onload`.
  - These scripts:
    - Prompt the visitor for a name if `username` cookie is not set.
    - Store the name in a cookie for 30 days.
    - Show a `alert("Welcome again ...")` message on return visits.
  - If you refactor or remove this behaviour, ensure you clean up the duplicate function definitions that exist in several files.

- **Embedded media and interaction**
  - `watch-videos.html` embeds several YouTube videos using `<iframe>` elements and simple `onclick` handlers that show an `alert('Submited')` when a user clicks a "Click Me!" button next to an input.

### Content organisation

Rather than listing every file, think of the content in a few groups:

- **Home & profile**
  - `index.html`: main landing page introducing the student, highlighting a few key sections.
  - `pages/personal.html`: "About" page with skills, projects/goals, education, and contact details, sharing the same layout components as the homepage.

- **Site navigation indices**
  - `pages/alllinks.html`: central index page that lists important internal pages and some external learning resources (MDN, GitHub, Khan Academy, etc.).
  - `pages/library.html`: high-level index into long-form content (e.g. stories, notes), linking to pages like `herobrine.html`, `good_info.html`, and `photo-animals.html`.

- **Media galleries and fun content**
  - `pages/space.html`: responsive gallery of space/astronomy images, using a custom `.gallery-grid` with `figure`/`figcaption` elements.
  - `pages/watch-videos.html`: curated set of embedded YouTube videos.
  - `pages/photo-animals.html`: playful link hub with some cookie-based greeting logic.

- **Long-form text and experiments**
  - `pages/good_info.html`: long list of trivia, film/book recommendations, and external links, with its own color scheme via `goodinfo.css`.
  - `pages/herobrine.html`: extended story content, using mostly plain HTML plus shared navbar styles.
  - `pages/animations.html`: early experiment page with background image styling and cookie-based greeting.

When adding new content, decide which of these buckets it belongs to and mirror the structure and layout of the closest existing page.

### Things to keep in mind when editing

- Many newer pages contain partially malformed HTML (e.g. stray closing tags inside attributes or comments interleaved with tags), but browsers still render them. When making changes, it is safe—and helpful—to gradually clean this up towards valid HTML5, as long as you preserve the visible layout and functionality.
- Placeholders like `[Your Name]` and `you@example.com` are used consistently across pages. If you update them, do it across all relevant files to avoid mismatches.
- Paths are mostly relative (e.g. `../picture.jpg`, `../index.html`). New pages under `pages/` should follow the same pattern for assets and links.
- The presence of `CNAME` indicates this repo is likely deployed to a custom domain; ensure new files and directories are compatible with static hosting (no reliance on server-side routing).
