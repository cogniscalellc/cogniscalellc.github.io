# Cogniscale Site — Project Showcase Design

**Date:** 2026-07-23
**Status:** Approved pending final spec review

## Goal

Replace the empty "03 — Selected work" placeholder on cogniscalellc.github.io with a visually impressive showcase of three shipped projects: GridMind, CosmicMerge, and the book *Agentic AI & DevOps*. Everything else on the page (hero, Studio, Focus, Contact, footer) stays as-is.

## Design Direction (approved via mockups)

**Treatment C — Themed Panels:** the site keeps its light paper-white editorial base, but each project sits in a full-width rounded panel with its own visual world. Panels alternate content orientation (media left / media right / media left) for rhythm.

Section heading changes from "What's next" to **"Built here."**; the section label stays "03 — Selected work". The empty-state copy is removed.

## The Three Panels

### 1. GridMind — Energy · iOS + watchOS

- **Panel:** near-black warm brown (`#14120e`) with an amber radial glow (`rgba(224,123,0,.26)`) anchored behind the devices.
- **Media (left):** two Apple device frames side by side, phone larger, watch smaller, bottom-aligned:
  - **iPhone 17 Pro Max (Cosmic Orange)** frame (`iphone-17-pro-max-cosmic-orange.webp`) with the looping muted demo video `iphone-demo.mp4` (~2.6 MB) playing beneath the transparent bezel. Poster/fallback image: the **live app dashboard screenshot** (`ios.png` from the marketing site — glowing power-flow diagram with tab bar). Chosen by user over the three promo-card screenshots.
  - **Apple Watch Ultra 3 (Terra Cotta)** frame (`apple-watch-ultra-3-terra-cotta.webp`) with looping `watch-demo.mp4` (~234 KB). Poster/fallback: watch dashboard screenshot (`packages/ios/screenshots/watch/1_dashboard.png`).
  - Exact screen-window geometry copied from the GridMind marketing site (measured from the bezel assets' alpha channels):
    - iPhone: aspect `1470/3000`; video at `left:5.102%; top:2.267%; width:89.796%; height:95.5%; border-radius:6.5%/3.1%`.
    - Watch: aspect `600/960`; video at `left:14.833%; top:23.229%; width:70.333%; height:53.542%; border-radius:28.4%/23.3%`.
- **Text (right):** tag `01 · Energy — iOS + watchOS` in amber (`#ffb85c`); title **GridMind**; blurb: "Solar and battery monitoring & automation for your home. Live power flow on your phone, goal rings on your wrist, and smart charging that works around utility rates and grid events."
- **CTA:** "Visit gridmindpower.com →" → `https://gridmindpower.com` (sole link, per user).

### 2. CosmicMerge — Game · iOS

- **Panel:** deep-space gradient `linear-gradient(135deg, #0d0a1e 0%, #151038 60%, #1d1147 100%)`; reversed orientation — media on the right, text on the left with right-aligned type on desktop (left-aligned when stacked on mobile).
- **Media:** the text-free procedural "merge" artwork (two planets colliding), generated from the game's own `generate_icon.swift` (1024×1024). **No baked-in text** — the achievement images were rejected because of their hard-coded typography.
- **Wordmark overlay** (bottom-center of the art): "COSMIC MERGE" set thin (weight 200), wide-tracked (`letter-spacing:.42em`), uppercase, filled with an ice-to-ember gradient (`#9ee8ff → #e6d9ff → #ffc37a`) via background-clip, with a soft dark drop shadow for legibility. Subline "MOONLET → BLACK HOLE" small, tracked, at 75% white-violet.
- **Text:** tag `02 · Game — iOS` in violet (`#a58cff`); title **CosmicMerge**; blurb: "A cosmic drop-and-merge physics puzzle. Eleven tiers of celestial bodies — moonlet to black hole — with every visual procedurally generated. Chain combos, climb the leaderboard."
- **CTA:** "Download on the App Store →" → `https://apps.apple.com/us/app/cosmic-merge-game/id6761395215`.

### 3. Agentic AI & DevOps — Book

- **Panel:** stark black (`#0a0a0a`); media left.
- **Media:** the book cover (from `Apple Book/Agentic AI & Devops (Cover).jpg`), ~21% panel width, rounded corners, deep shadow. Source is 7.7 MB — must be resized/compressed for web (~600 px wide JPEG, target < 150 KB).
- **Text:** tag `03 · Book` in the cover's orange (`#f59e0b`); title **Agentic AI & DevOps**; blurb: "A book on putting AI agents to work in real engineering practice — architecture, guardrails, and the human–AI partnership. Available in paperback, hardcover, and Kindle."
- **CTA:** "Read on Amazon →" → `https://www.amazon.com/Agentic-AI-DevOps-Architecture-Partnership/dp/B0FLWVRDV5` (tracking parameters stripped).

## Motion & Interaction

- Panels reveal on scroll: fade + small upward translate via `IntersectionObserver`, staggered per panel. Fully disabled under `prefers-reduced-motion: reduce` (content simply visible).
- Hover: panels get a subtle lift (translateY + shadow deepen); CTAs get an arrow nudge.
- Videos: `autoplay loop muted playsinline`, `preload="metadata"`, `poster` set to the chosen screenshots; `loading="lazy"` on all panel imagery. Videos are below the fold, so initial page weight is unaffected until scroll.

## Implementation Notes

- Plain HTML/CSS + a small inline `<script>` for the IntersectionObserver — no frameworks, no build step. Extend the existing `styles.css` using its current custom properties/idiom; new classes namespaced `.project-panel`, etc.
- Responsive: panels switch to column layout (media above text, text left-aligned) below ~720 px; device duo scales down; wordmark scales with container.
- Assets committed under `assets/` in the site repo:
  - `assets/iphone-frame.webp`, `assets/watch-frame.webp` (copied from marketing site)
  - `assets/iphone-demo.mp4`, `assets/watch-demo.mp4` (copied as-is)
  - `assets/gridmind-dashboard.png` (ios.png resized to ~800 px wide), `assets/gridmind-watch.png` (~400 px wide)
  - `assets/cosmicmerge-art.png` (regenerated icon resized to ~800 px, compressed)
  - `assets/book-cover.jpg` (~600 px wide, < 150 KB)
- Accessibility: `aria-label`s on videos describing content; empty `alt` on decorative bezel frames; meaningful `alt` on cover art; text contrast on all panels ≥ 4.5:1 (light text on near-black passes).
- Update `<meta name="description">` to mention shipped products.
- README of the site repo: brief note about the assets' provenance is optional; skip changelog machinery (this repo has none).
- Add `.superpowers/` (visual-companion scratch dir) to a `.gitignore` in the site repo.

## Out of Scope

- No changes to hero, Studio, Focus, or Contact sections.
- No analytics, no cookie banners, no new pages.
- No dark-mode variant of the page itself (panels are inherently dark; site stays light).

## Testing / Verification

1. Serve locally (`python3 -m http.server`) and verify: videos loop inside both bezels with correct alignment; posters show before video load and when reduced-motion/data-saver blocks autoplay.
2. Check responsive behavior at 375 px, 768 px, 1280 px widths.
3. Verify `prefers-reduced-motion` disables reveal animations (macOS: System Settings → Accessibility → Display → Reduce Motion).
4. Confirm all three outbound links resolve (200) and open in a new tab (`target="_blank" rel="noopener"`).
5. Total added page weight budget: < 3.5 MB including both videos; images alone < 700 KB.

## Deployment

Commit to `main` of `cogniscalellc/cogniscalellc.github.io` (push as smidley — `gh auth switch --user smidley`); GitHub Pages redeploys automatically. Verify live at https://cogniscalellc.github.io after a few minutes.
