# Building an Interactive Global Locations Map with Claude Code

## Session Summary

A step-by-step walkthrough of creating a pure HTML/CSS interactive world map component for the A-dec "About Us" webpage, built collaboratively with Claude Code.

---

## Phase 1: Initial Concept & Requirements

**Goal:** Recreate an existing static map image as a dynamic, embeddable HTML/CSS component showing A-dec's 5 global locations.

**Requirements defined:**
- Modern, simple, dynamic UX
- Mobile and responsive
- Polished visual design
- Accessibility concerns addressed
- Pure HTML/CSS (no JavaScript frameworks)

**Locations to plot:**
1. **Newberg, Oregon** — Global Headquarters
2. **Nashville, Tennessee** — Education & Showroom
3. **Nuneaton, United Kingdom** — Subsidiary
4. **Paris, France** — Exclusive Importer & Showroom
5. **Sydney, Australia** — Subsidiary

> Screenshot opportunity: The original reference map image

---

## Phase 2: The Hilarious First Attempt

Claude attempted to hand-draw SVG continent paths from scratch. The result was... creative. Unrecognizable blob-shaped continents that looked nothing like Earth.

**Lesson learned:** AI-generated SVG map paths are not the way to go. A real map asset is essential.

> Screenshot opportunity: The laughably bad hand-drawn SVG map (v1)

---

## Phase 3: Sourcing a Map Asset

**Challenge:** Finding a usable SVG world map.

- Claude researched public domain SVG world maps (Wikimedia Commons, FreeSVG, GitHub repos)
- First attempt: Wikimedia Simple World Map — had country borders, not ideal
- User provided their own SVG map asset (`World.svg`) but it was 1.5 MB — too large for Claude to read directly
- A second version was also too large
- **Solution:** Reference the SVG as an `<img>` tag instead of inlining it, using CSS `filter: brightness(0) invert(1)` to convert any fill color to white

> Screenshot opportunity: N/A (file management step)

---

## Phase 4: Version 1 — Solid Map with Badge Labels

The first working version used the SVG map at full opacity with colored badge-style labels matching the original reference image.

**Features:**
- Map displayed at near-full opacity
- Labels with colored type badges (gray for HQ, blue for Education, teal for Subsidiary)
- City name in bold blue pill
- White dots with pulse animation
- Hover tooltips with location details
- Keyboard-accessible (tabindex + focus states)

> Screenshot opportunity: V1 with solid map and badge-style labels

---

## Phase 5: Version 2 — Editorial Redesign

A complete visual overhaul with a more modern, editorial aesthetic.

**Key design changes:**
- **Ghost map** — SVG at 12% opacity for a subtle backdrop feel
- **Split typography** — City, country, and type badge as separate layers (bold city name, light country, small colored badge)
- **Glassmorphism tooltips** — Frosted glass info panels on hover
- **Double ping animation** — Radar-style ripple rings on each dot, staggered timing
- **Labels always visible** — Staggered fade-in on page load
- **Dark blue (#003B5C) background** matching A-dec brand

> Screenshot opportunity: V2 with ghost map and editorial labels

---

## Phase 6: The Pin Placement Saga

**The problem:** Pin positions were completely wrong. Oregon was in Canada, other cities were misplaced.

**Attempt 1 — Math:** Claude tried calculating positions from latitude/longitude using an assumed equirectangular projection. The SVG used a different projection, so the math was wrong.

**Attempt 2 — Console script:** Claude suggested a JavaScript snippet for the browser console to click-and-log coordinates. It didn't work in the user's environment.

**Attempt 3 — Built-in click helper (the winner):** Claude added a click helper directly into the HTML page:
- Crosshair cursor on the map
- Click anywhere to drop a red dot
- Green overlay in the top-right shows exact `left: X%; top: Y%` coordinates
- User clicked each city location and reported coordinates back

**Final pin positions:**
| City | Left | Top |
|------|------|-----|
| Newberg, OR | 13.7% | 49.3% |
| Nashville, TN | 23.8% | 54% |
| Nuneaton, UK | 46.9% | 43.5% |
| Paris, France | 48.5% | 47.4% |
| Sydney, Australia | 89.2% | 83.2% |

> Screenshot opportunity: The click helper in action with red dots and coordinate overlay

---

## Phase 7: Label Collision & Layout Tuning

**Problem:** UK and France labels were overlapping since the cities are geographically close.

**Solutions tried:**
1. Moved UK label above with vertical connector line — line ran parallel to label, looked disconnected
2. L-shaped connector line (vertical + horizontal bend) — better but messy
3. **Final approach:** Removed all connector lines. UK label placed above-right of dot, Paris label offset further right. Clean separation.

**Additional fine-tuning (pixel nudges from user feedback):**
- Sydney label: up 10px
- Paris label: left 10px
- Nashville label: down 5px
- Oregon label: up 10px
- UK label: mirrored to right side of dot

> Screenshot opportunity: Before/after of UK + France label collision fix

---

## Phase 8: Height & Cropping

**Problem:** The map was too tall for a webpage section. Lots of empty space at the top (Arctic region).

**Approach:** Crop the top by shifting the inner container (image + pins together) upward, with the outer wrapper clipping overflow. Gradient fades on both top and bottom edges blend into the dark blue background.

**Key insight:** The image and pins must move together inside a shared container. Earlier attempts moved only the image, breaking pin alignment.

**CSS technique:**
```css
.map__wrapper {
  overflow: hidden;
  max-height: 700px;
}
.map__inner {
  margin-top: -10%; /* shifts everything up */
}
/* Pseudo-elements for gradient fades */
.map__wrapper::before { /* top fade */ }
.map__wrapper::after  { /* bottom fade */ }
```

> Screenshot opportunity: Final map with gradient fades on edges

---

## Phase 9: Cleanup for Production

Removed all interactive/development elements for the final embeddable component:

- Hover tooltips / info panels — removed
- Hover scale effect on dots — removed
- Cursor pointer — removed
- Click coordinate helper — removed
- Tabindex / focus states — removed
- All CSS scoped under `.adec-map` to prevent conflicts with host page styles
- Keyframe animations prefixed with `adec-` to avoid collisions

**Final output:** `global-map-embed.html` — a single file with `<style>` block + HTML, ready to paste into any rich text editor or raw HTML component.

> Screenshot opportunity: The final polished map component

---

## Technical Decisions & Takeaways

| Decision | Why |
|----------|-----|
| SVG as `<img>` not inline | File too large to inline; `filter` CSS handles color |
| CSS-only animations | No JS dependency, works in any CMS |
| Percentage-based pin positions | Scales responsively with map |
| Inner container for crop | Keeps pins aligned when shifting the map |
| Scoped CSS class `.adec-map` | Safe to embed without style conflicts |
| Click helper for pin placement | Most reliable way to map coordinates to a custom projection |

---

## Files

| File | Purpose |
|------|---------|
| `World.svg` | Source world map asset |
| `global-map.html` | V1 — solid map with badge labels |
| `global-map-v2.html` | V2 — development/preview version |
| `global-map-embed.html` | Final production-ready embed |
