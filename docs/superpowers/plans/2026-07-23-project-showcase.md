# Cogniscale Project Showcase Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the empty "03 — Selected work" section on cogniscalellc.github.io with three themed project panels (GridMind, CosmicMerge, the book *Agentic AI & DevOps*) per the approved spec at `docs/superpowers/specs/2026-07-23-showcase-projects-design.md`.

**Architecture:** Static single-page site (plain HTML + one CSS file, no build step). We add an `assets/` directory of optimized media, one new markup block in `index.html`, a namespaced `.project-panel` style group in `styles.css`, and a ~15-line inline IntersectionObserver script. GitHub Pages deploys on push to `main`.

**Tech Stack:** HTML, CSS, vanilla JS, `sips` (macOS) for image processing, `git`/`gh` for deploy.

## Global Constraints

- Repo: `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io` (clone of `cogniscalellc/cogniscalellc.github.io`, branch `main`). All paths below are relative to this repo root unless absolute.
- Git identity: Scott Brant / smidley@gmail.com (already set via `git config` in this clone). Before pushing: `gh auth switch --user smidley`.
- Only section `#work` in `index.html` (plus `<meta name="description">` and the new inline script before `</body>`) may change. Hero, Studio, Focus, Contact, header, footer stay byte-identical.
- Outbound links (exact): GridMind → `https://gridmindpower.com` · CosmicMerge → `https://apps.apple.com/us/app/cosmic-merge-game/id6761395215` · Book → `https://www.amazon.com/Agentic-AI-DevOps-Architecture-Partnership/dp/B0FLWVRDV5`. All use `target="_blank" rel="noopener"`.
- Accent colors: GridMind `#FFB85C` (amber), CosmicMerge `#A58CFF` (violet), Book `#F59E0B` (orange). Panel text color `#F2F0EA`.
- Poster/art rasters ship as JPEG (`.jpg`), not PNG as the spec's illustrative names suggested — required to hit the < 700 KB image budget. Bezel frames stay WebP (need alpha). Videos copied unmodified.
- Page-weight budget: everything under `assets/` ≤ 3.5 MB total; images alone ≤ 700 KB.
- Reveal animation must be fully inert under `prefers-reduced-motion: reduce` and content must be visible when JS never runs (no-JS = panels simply shown).
- Source asset locations (absolute, read-only):
  - `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/gridmind-cloud/packages/marketing-site/public/images/iphone-17-pro-max-cosmic-orange.webp`
  - `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/gridmind-cloud/packages/marketing-site/public/images/apple-watch-ultra-3-terra-cotta.webp`
  - `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/gridmind-cloud/packages/marketing-site/public/videos/iphone-demo.mp4`
  - `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/gridmind-cloud/packages/marketing-site/public/videos/watch-demo.mp4`
  - `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/gridmind-cloud/packages/marketing-site/public/images/ios.png` (phone poster source, 1320×2868)
  - `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/gridmind-cloud/packages/ios/screenshots/watch/1_dashboard.png` (watch poster source, 416×496)
  - `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cosmicmerge/generate_icon.swift` (run from that repo's root to regenerate the art)
  - `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/Agentic AI & DevOps/Apple Book/Agentic AI & Devops (Cover).jpg` (book cover source, 3489×5691)

---

### Task 1: Optimized media assets

**Files:**
- Create: `assets/iphone-frame.webp`, `assets/watch-frame.webp`, `assets/iphone-demo.mp4`, `assets/watch-demo.mp4`, `assets/gridmind-dashboard.jpg`, `assets/gridmind-watch.jpg`, `assets/cosmicmerge-art.jpg`, `assets/book-cover.jpg`

**Interfaces:**
- Produces: the eight asset files above, referenced by exact filename in Task 2's HTML.

- [ ] **Step 1: Create `assets/` and copy the pass-through files**

```bash
SITE="/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io"
GM="/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/gridmind-cloud/packages/marketing-site/public"
mkdir -p "$SITE/assets"
cp "$GM/images/iphone-17-pro-max-cosmic-orange.webp" "$SITE/assets/iphone-frame.webp"
cp "$GM/images/apple-watch-ultra-3-terra-cotta.webp" "$SITE/assets/watch-frame.webp"
cp "$GM/videos/iphone-demo.mp4" "$SITE/assets/iphone-demo.mp4"
cp "$GM/videos/watch-demo.mp4" "$SITE/assets/watch-demo.mp4"
```

- [ ] **Step 2: Regenerate the CosmicMerge art (text-free procedural icon)**

```bash
cd "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cosmicmerge"
swift generate_icon.swift /tmp/cm-icon.png
```

Expected output: `Icon saved to /tmp/cm-icon.png` (1024×1024).

- [ ] **Step 3: Resize + JPEG-convert the four rasters**

```bash
SITE="/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io"
GMIMG="/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/gridmind-cloud/packages/marketing-site/public/images"
WATCH="/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/gridmind-cloud/packages/ios/screenshots/watch/1_dashboard.png"
BOOK="/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/Agentic AI & DevOps/Apple Book/Agentic AI & Devops (Cover).jpg"
sips --resampleWidth 800 -s format jpeg -s formatOptions 85 "$GMIMG/ios.png" --out "$SITE/assets/gridmind-dashboard.jpg"
sips --resampleWidth 400 -s format jpeg -s formatOptions 85 "$WATCH" --out "$SITE/assets/gridmind-watch.jpg"
sips --resampleWidth 800 -s format jpeg -s formatOptions 85 /tmp/cm-icon.png --out "$SITE/assets/cosmicmerge-art.jpg"
sips --resampleWidth 600 -s format jpeg -s formatOptions 78 "$BOOK" --out "$SITE/assets/book-cover.jpg"
```

- [ ] **Step 4: Verify dimensions and budget**

```bash
SITE="/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io"
for f in gridmind-dashboard.jpg gridmind-watch.jpg cosmicmerge-art.jpg book-cover.jpg; do
  sips -g pixelWidth -g pixelHeight "$SITE/assets/$f" | tail -2
done
du -ch "$SITE/assets/"*.jpg | tail -1
du -ch "$SITE/assets/"* | tail -1
```

Expected: widths 800 / 400 / 800 / 600; the `.jpg` total ≤ 700 KB; the all-assets total ≤ 3.5 MB (videos are ~2.6 MB + ~234 KB; frames ~28 KB + ~90 KB). If the `.jpg` total exceeds 700 KB, re-run the offending `sips` line with `formatOptions` lowered by 10 and re-check.

- [ ] **Step 5: Commit**

```bash
cd "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io"
git add assets/
git commit -m "feat: add optimized showcase media assets

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 2: Showcase markup in index.html

**Files:**
- Modify: `index.html` (the `#work` section, currently lines 68–75; the `<meta name="description">` on line 6)

**Interfaces:**
- Consumes: Task 1's asset filenames.
- Produces: DOM structure — `.project-panels` container with three `article.project-panel` children (`.panel-gridmind`, `.panel-cosmicmerge`, `.panel-book`) — that Task 3's CSS and Task 4's JS select by these exact class names.

- [ ] **Step 1: Update the meta description**

Replace line 6 of `index.html`:

```html
    <meta name="description" content="Cogniscale is an independent software product studio." />
```

with:

```html
    <meta name="description" content="Cogniscale is an independent software product studio — maker of GridMind, CosmicMerge, and the book Agentic AI &amp; DevOps." />
```

- [ ] **Step 2: Replace the placeholder work section**

Replace this block in `index.html`:

```html
      <section id="work" class="work section-grid" aria-labelledby="work-title">
        <p class="section-label">03 — Selected work</p>
        <div class="section-content work-content">
          <h2 id="work-title">What’s next</h2>
          <p class="empty-state">New products are taking shape.</p>
          <p>Check back soon for the work in progress.</p>
        </div>
      </section>
```

with:

```html
      <section id="work" class="work section-grid" aria-labelledby="work-title">
        <p class="section-label">03 — Selected work</p>
        <div class="section-content work-content">
          <h2 id="work-title">Built here.</h2>

          <div class="project-panels">
            <article class="project-panel panel-gridmind" aria-labelledby="gridmind-title">
              <div class="panel-media panel-devices">
                <div class="device-iphone">
                  <video autoplay loop muted playsinline preload="metadata" poster="assets/gridmind-dashboard.jpg"
                    aria-label="GridMind iOS app showing the live power-flow dashboard">
                    <source src="assets/iphone-demo.mp4" type="video/mp4" />
                  </video>
                  <img class="device-frame" src="assets/iphone-frame.webp" alt="" width="780" height="1592" loading="lazy" />
                </div>
                <div class="device-watch">
                  <video autoplay loop muted playsinline preload="metadata" poster="assets/gridmind-watch.jpg"
                    aria-label="GridMind Apple Watch app showing live solar, home, battery, grid, and EV stats">
                    <source src="assets/watch-demo.mp4" type="video/mp4" />
                  </video>
                  <img class="device-frame" src="assets/watch-frame.webp" alt="" width="600" height="960" loading="lazy" />
                </div>
              </div>
              <div class="panel-text">
                <p class="panel-tag">01 · Energy — iOS + watchOS</p>
                <h3 id="gridmind-title">GridMind</h3>
                <p>Solar and battery monitoring &amp; automation for your home. Live power flow on your phone, goal rings on your wrist, and smart charging that works around utility rates and grid events.</p>
                <a class="panel-cta" href="https://gridmindpower.com" target="_blank" rel="noopener">Visit gridmindpower.com <span aria-hidden="true">→</span></a>
              </div>
            </article>

            <article class="project-panel panel-cosmicmerge" aria-labelledby="cosmicmerge-title">
              <div class="panel-media cm-visual">
                <img src="assets/cosmicmerge-art.jpg" alt="Two planets colliding in a burst of light" width="800" height="800" loading="lazy" />
                <p class="cm-wordmark" aria-hidden="true"><span class="cm-word">Cosmic Merge</span><span class="cm-sub">Moonlet → Black Hole</span></p>
              </div>
              <div class="panel-text">
                <p class="panel-tag">02 · Game — iOS</p>
                <h3 id="cosmicmerge-title">CosmicMerge</h3>
                <p>A cosmic drop-and-merge physics puzzle. Eleven tiers of celestial bodies — moonlet to black hole — with every visual procedurally generated. Chain combos, climb the leaderboard.</p>
                <a class="panel-cta" href="https://apps.apple.com/us/app/cosmic-merge-game/id6761395215" target="_blank" rel="noopener">Download on the App Store <span aria-hidden="true">→</span></a>
              </div>
            </article>

            <article class="project-panel panel-book" aria-labelledby="book-title">
              <div class="panel-media book-visual">
                <img src="assets/book-cover.jpg" alt="Agentic AI &amp; DevOps book cover" width="600" height="979" loading="lazy" />
              </div>
              <div class="panel-text">
                <p class="panel-tag">03 · Book</p>
                <h3 id="book-title">Agentic AI &amp; DevOps</h3>
                <p>A book on putting AI agents to work in real engineering practice — architecture, guardrails, and the human–AI partnership. Available in paperback, hardcover, and Kindle.</p>
                <a class="panel-cta" href="https://www.amazon.com/Agentic-AI-DevOps-Architecture-Partnership/dp/B0FLWVRDV5" target="_blank" rel="noopener">Read on Amazon <span aria-hidden="true">→</span></a>
              </div>
            </article>
          </div>
        </div>
      </section>
```

- [ ] **Step 3: Verify structure**

```bash
cd "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io"
grep -c "project-panel " index.html          # expect 3
grep -c 'rel="noopener"' index.html           # expect 3
grep -c "empty-state" index.html              # expect 0
grep -c "Built here." index.html              # expect 1
```

Also confirm the hero/studio/focus/contact sections are untouched: `git diff index.html` must show changes only at the meta line and inside the `#work` section.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: showcase GridMind, CosmicMerge, and Agentic AI & DevOps

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 3: Panel styles

**Files:**
- Modify: `styles.css` — insert the block below immediately after the `.empty-state` rule (which stays; it's now unused but harmless to remove — delete it while you're there) and before the `.contact` rule. Also extend the existing `@media (max-width: 48rem)` block.

**Interfaces:**
- Consumes: class names from Task 2.
- Produces: `html.js .project-panel` hidden/reveal states that Task 4's script drives via the `is-visible` class.

- [ ] **Step 1: Delete the now-unused `.empty-state` rule**

Remove from `styles.css`:

```css
.empty-state {
  margin-bottom: 0.45rem !important;
  color: var(--blue);
  font-weight: 750;
}
```

- [ ] **Step 2: Add the panel styles in its place**

```css
/* Selected work — project panels */
.work .section-content {
  max-width: none;
}

.project-panels {
  display: grid;
  gap: clamp(1.25rem, 2.5vw, 2rem);
  margin-top: clamp(2.5rem, 5vw, 4.5rem);
}

.project-panel {
  display: flex;
  align-items: center;
  gap: clamp(1.75rem, 4vw, 3.5rem);
  padding: clamp(1.75rem, 4vw, 3.25rem);
  border-radius: 1rem;
  color: #F2F0EA;
  transition: transform 0.35s ease, box-shadow 0.35s ease, opacity 0.6s ease, translate 0.6s ease;
}

.project-panel:hover {
  transform: translateY(-4px);
  box-shadow: 0 24px 60px rgba(0, 0, 0, 0.25);
}

.panel-gridmind {
  background-color: #14120E;
  background-image: radial-gradient(ellipse at 18% 45%, rgba(224, 123, 0, 0.26), transparent 55%);
}

.panel-cosmicmerge {
  flex-direction: row-reverse;
  background-image: linear-gradient(135deg, #0D0A1E 0%, #151038 60%, #1D1147 100%);
}

.panel-book {
  background-color: #0A0A0A;
}

.panel-tag {
  margin: 0;
  font-size: 0.72rem;
  font-weight: 800;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.panel-gridmind .panel-tag,
.panel-gridmind .panel-cta {
  color: #FFB85C;
}

.panel-cosmicmerge .panel-tag,
.panel-cosmicmerge .panel-cta {
  color: #A58CFF;
}

.panel-book .panel-tag,
.panel-book .panel-cta {
  color: #F59E0B;
}

.project-panel h3 {
  margin: 0.5rem 0 0.9rem;
  font-size: clamp(1.9rem, 3.2vw, 3rem);
}

.panel-text > p:not(.panel-tag) {
  max-width: 34rem;
  margin-bottom: 0;
  font-size: clamp(1rem, 1.3vw, 1.13rem);
  line-height: 1.55;
  opacity: 0.85;
}

.panel-cta {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  min-height: 44px;
  margin-top: 0.9rem;
  font-weight: 700;
  text-underline-offset: 0.28em;
}

.panel-cta span {
  transition: transform 0.25s ease;
}

.panel-cta:hover span {
  transform: translateX(4px);
}

.panel-cosmicmerge .panel-text {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  text-align: right;
}

.panel-devices {
  display: flex;
  flex-shrink: 0;
  align-items: flex-end;
  gap: 1.25rem;
}

.device-iphone {
  position: relative;
  width: clamp(11rem, 16vw, 15rem);
  aspect-ratio: 1470 / 3000;
  filter: drop-shadow(0 25px 50px rgba(0, 0, 0, 0.6));
}

.device-iphone video {
  position: absolute;
  left: 5.102%;
  top: 2.267%;
  width: 89.796%;
  height: 95.5%;
  object-fit: cover;
  background: #000;
  border-radius: 6.5% / 3.1%;
}

.device-frame {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.device-watch {
  position: relative;
  width: clamp(8.5rem, 12vw, 11.5rem);
  aspect-ratio: 600 / 960;
  filter: drop-shadow(0 20px 40px rgba(0, 0, 0, 0.6));
}

.device-watch video {
  position: absolute;
  left: 14.833%;
  top: 23.229%;
  width: 70.333%;
  height: 53.542%;
  object-fit: cover;
  background: #000;
  border-radius: 28.4% / 23.3%;
}

.cm-visual {
  position: relative;
  flex-shrink: 0;
  width: clamp(14rem, 30vw, 22rem);
}

.cm-visual > img {
  display: block;
  width: 100%;
  height: auto;
  border-radius: 0.85rem;
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.55);
}

.cm-wordmark {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-end;
  margin: 0;
  padding-bottom: 10%;
}

.cm-word {
  background-image: linear-gradient(90deg, #9EE8FF, #E6D9FF 45%, #FFC37A);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  filter: drop-shadow(0 2px 14px rgba(0, 0, 0, 0.9));
  font-size: clamp(1rem, 2vw, 1.5rem);
  font-weight: 200;
  letter-spacing: 0.42em;
  text-indent: 0.42em;
  text-transform: uppercase;
}

.cm-sub {
  margin-top: 0.4rem;
  color: rgba(240, 238, 255, 0.75);
  font-size: 0.6rem;
  letter-spacing: 0.34em;
  text-indent: 0.34em;
  text-transform: uppercase;
}

.book-visual {
  flex-shrink: 0;
  width: clamp(10rem, 18vw, 14rem);
}

.book-visual img {
  display: block;
  width: 100%;
  height: auto;
  border-radius: 0.5rem;
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.6);
}

/* Scroll reveal — only when JS is running AND motion is welcome.
   `translate` (not `transform`) so the hover lift composes independently. */
@media (prefers-reduced-motion: no-preference) {
  html.js .project-panel {
    opacity: 0;
    translate: 0 1.5rem;
  }

  html.js .project-panel.is-visible {
    opacity: 1;
    translate: none;
  }
}
```

- [ ] **Step 3: Extend the mobile breakpoint**

Inside the existing `@media (max-width: 48rem)` block, after the `.focus-card` rule, add:

```css
  .project-panel,
  .panel-cosmicmerge {
    flex-direction: column;
    align-items: flex-start;
  }

  .panel-devices,
  .cm-visual,
  .book-visual {
    align-self: center;
  }

  .cm-visual,
  .book-visual {
    width: min(100%, 18rem);
  }

  .panel-cosmicmerge .panel-text {
    align-items: flex-start;
    text-align: left;
  }
```

- [ ] **Step 4: Verify**

```bash
cd "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io"
grep -c "project-panel" styles.css     # expect ≥ 8
grep -c "empty-state" styles.css       # expect 0
python3 -m http.server 8123 &
sleep 1
curl -s http://localhost:8123/styles.css | grep -c "cm-wordmark"   # expect 2
kill %1
```

Then open `http://localhost:8123` in a browser (restart the server) and eyeball: three dark panels under "Built here.", devices left / art right / cover left, wordmark legible over the art. (Panels will all be invisible-then-revealed only after Task 4 adds the script; without JS they must be fully visible.)

- [ ] **Step 5: Commit**

```bash
git add styles.css
git commit -m "feat: themed panel styles for project showcase

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 4: Scroll-reveal script

**Files:**
- Modify: `index.html` — insert the script directly before `</body>`.

**Interfaces:**
- Consumes: `.project-panel` elements (Task 2), `html.js` / `.is-visible` CSS hooks (Task 3).

- [ ] **Step 1: Add the script**

Insert before `</body>` in `index.html`:

```html
    <script>
      document.documentElement.classList.add("js");
      const panels = document.querySelectorAll(".project-panel");
      if ("IntersectionObserver" in window) {
        const io = new IntersectionObserver(
          (entries) => {
            for (const entry of entries) {
              if (entry.isIntersecting) {
                entry.target.classList.add("is-visible");
                io.unobserve(entry.target);
              }
            }
          },
          { threshold: 0.15 }
        );
        panels.forEach((panel) => io.observe(panel));
      } else {
        panels.forEach((panel) => panel.classList.add("is-visible"));
      }
    </script>
```

- [ ] **Step 2: Verify reveal behavior in a browser**

```bash
cd "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io"
python3 -m http.server 8123
```

In the browser at `http://localhost:8123`:
1. Scroll to the work section — each panel fades/rises in once, staggered by scroll position.
2. Both device screens play looping video inside the bezels (posters flash first on slow load).
3. macOS System Settings → Accessibility → Display → Reduce Motion ON → reload → panels are simply visible, no fade.
4. DevTools → disable JavaScript → reload → panels visible immediately.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: scroll-reveal for project panels

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 5: Full verification pass

**Files:** none (verification only)

- [ ] **Step 1: Outbound links resolve**

```bash
for url in "https://gridmindpower.com" "https://apps.apple.com/us/app/cosmic-merge-game/id6761395215" "https://www.amazon.com/Agentic-AI-DevOps-Architecture-Partnership/dp/B0FLWVRDV5"; do
  code=$(curl -s -o /dev/null -w "%{http_code}" -L -A "Mozilla/5.0" "$url")
  echo "$code $url"
done
```

Expected: `200` for the first two; Amazon may return `503`/`403` to curl's bot detection — if so, verify the Amazon link manually in a browser instead.

- [ ] **Step 2: Weight budget**

```bash
du -ch "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io/assets/"* | tail -1
```

Expected: total ≤ 3.5 MB.

- [ ] **Step 3: Responsive check**

With the local server running, use browser DevTools responsive mode at 375 px, 768 px, 1280 px:
- 375 px: panels stack (media above text), devices centered, CosmicMerge text left-aligned, no horizontal scroll.
- 768 px: same stacked layout (breakpoint is 48rem = 768px, so at exactly 768 px expect the desktop row layout; at 767 px expect stacked).
- 1280 px: rows alternate left/right; text column ≤ 34rem wide.

- [ ] **Step 4: Confirm untouched sections**

```bash
cd "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io"
git diff HEAD~4 -- index.html | grep -E "^[-+].*(hero|studio|focus|contact)" | grep -v work
```

Expected: no output (only the meta description, work section, and script changed across the four commits — HEAD~4 is the pre-implementation state).

---

### Task 6: Deploy and verify live

**Files:** none (git push only)

- [ ] **Step 1: Push**

```bash
gh auth switch --user smidley
cd "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/cogniscalellc.github.io"
git push origin main
```

- [ ] **Step 2: Wait for Pages deploy and verify**

```bash
for i in 1 2 3 4 5 6 7 8 9 10; do
  sleep 30
  if curl -s https://cogniscalellc.github.io | grep -q "Built here."; then echo DEPLOYED; break; fi
  echo "waiting ($i)"
done
curl -s -o /dev/null -w "%{http_code}\n" https://cogniscalellc.github.io/assets/iphone-demo.mp4   # expect 200
```

(Loop is written with a literal list, not `$var` expansion — the Bash tool runs zsh, which does not word-split unquoted variables.)

- [ ] **Step 3: Final visual check**

Open https://cogniscalellc.github.io in a browser: videos loop in both bezels, panels reveal on scroll, all three CTAs open the right destinations in new tabs.
