# Sticky In-Page Sub-Navigation — Research & Design Spec

> Research doc for redesigning `services.html` sub-nav.
> Status: Research complete. Implementation pending.

---

## Executive Summary

The best sticky in-page sub-navs share four non-negotiable traits:
1. **Thin** — 44–48px total height. Every pixel of nav height is a pixel of content hidden.
2. **Single visual metaphor** — sliding underline OR pill background, never both.
3. **Mobile = horizontal scroll + CSS snap**, not dropdowns or two rows.
4. **Active state at 150–200ms** — feels instant, not animated.

---

## 1. How Top Sites Do It

### Apple (iPhone, MacBook product pages)
- **Height**: 48px
- **Background**: `rgba(255,255,255,0.80)` + `backdrop-filter: blur(20px)` — frosted glass
- **Border**: `border-bottom: 1px solid rgba(0,0,0,0.12)` — hairline, not a shadow
- **Typography**: 14px, weight 400 inactive → weight 600 active. Color doesn't change much — weight is the signal.
- **Active indicator**: 2px solid border-bottom on active tab. Appears at 150ms, no slide animation.
- **Mobile**: Single horizontal scroll, no scrollbar, no indicators. Apple trusts users to swipe.
- **Key insight**: No pill backgrounds. Active state is purely typographic. No visual noise = premium.

### Airbnb (listing pages)
- **Height**: 48px
- **Background**: `#fff` (no blur — their hero has a carousel, blur would conflict)
- **Border**: `border-bottom: 1px solid rgba(0,0,0,0.08)`
- **Active state**: font-weight 600 + 2px near-black underline, fades in at 150ms
- **Mobile**: `overflow-x: auto` + `scrollbar-width: none` + `-webkit-overflow-scrolling: touch`. Adds `scroll-snap-align: start` with `scroll-padding-left: 16px`. Does NOT auto-center active item.

### Vercel Docs (feature/landing pages)
- **Active indicator**: Animated **sliding underline** — a single `div.indicator` that translates left/right via CSS `transform: translateX()` + `width` change.
- **Transition**: `200ms cubic-bezier(0.4, 0, 0.2, 1)` — Material Design "standard easing".
- **Key insight**: Sliding underline is the definitive premium animation for **click-driven** nav. For **scroll-driven** active states (IntersectionObserver), the slide can look jittery — use a simple opacity fade instead.

### Stripe (docs sidebar / product pages)
- **Sidebar scrollspy**: 2px left-border accent on active item. Color brand accent. Background `rgba(accent, 0.06)` behind active row.
- **Transition**: border color at `200ms ease`. Uses `transform: scaleY()` grow-in — extremely subtle.
- **Typography**: 13–14px, 500 weight → 600 weight active.

### Linear (linear.app)
- **Height**: 40px
- **Background**: `rgba(255,255,255,0.92)` + `backdrop-filter: saturate(180%) blur(12px)`
- **Active**: Only font-weight changes (400 → 600). No underline. Works because they have only 4–5 items.
- **Rule**: No underline needed if ≤5 items and weight contrast is strong. More than 6 = needs visual indicator.

### Google / YouTube (category tabs, home tabs)
- Mobile: single horizontal scroll with `scrollbar-width: none` + **JS auto-scroll active item to center**.
- This is the single most important mobile enhancement: when scroll changes the active section, JS smoothly centers the corresponding tab.

---

## 2. Visual Design Patterns — Taxonomy

### Pattern A: Underline-Only (Apple, Airbnb, GitHub) ⭐ Best for us
```
[Dimensional]  [Pressure]  [Acoustics]  [On-Site]
               ___________
               (2px teal)
```
- Active indicator: 2px solid absolute or border-bottom. Fade in at 150ms.
- Inactive text: `brand-ink-light` (#4A6663)
- Active text: `brand-ink` (#0F1E1C) + weight 600
- Bar: `border-bottom: 1px solid rgba(71,133,122,0.12)`
- **When to use**: 5–8 items, product/service/content pages. The web's native vocabulary.

### Pattern B: Sliding Underline Indicator (Vercel, SaaS tools)
A single `div` translates from item to item via JS measuring `offsetLeft` + `offsetWidth`.
```css
.indicator {
  position: absolute;
  bottom: 0; height: 2px;
  transition: left 220ms cubic-bezier(0.4, 0, 0.2, 1),
              width 220ms cubic-bezier(0.4, 0, 0.2, 1);
}
```
**Only animate on click** — disable slide transition on scroll-driven changes.

### Pattern C: Pill/Chip Background (Material, MDN)
Active item gets semi-transparent background pill. More "UI control" than navigation. Best for 3–6 items. **Avoid on premium product/service pages.**

### Pattern D: Tab Line (Bootstrap-style)
Each item is a block tab with full bottom border. **2015-era. Never use for premium.**

---

## 3. Mobile — What Actually Works

### ✅ Winner: Horizontal Scroll + Hidden Scrollbar + CSS Snap
Used by Apple, Airbnb, Google, YouTube, Twitter/X, LinkedIn.

```css
.subnav-scroll {
  display: flex;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
  scrollbar-width: none;
  scroll-snap-type: x proximity;  /* proximity not mandatory — gentler */
  scroll-padding-left: 16px;
}
.subnav-scroll::-webkit-scrollbar { display: none; }

.subnav-item {
  flex-shrink: 0;            /* CRITICAL — prevents wrapping */
  scroll-snap-align: start;
  white-space: nowrap;
}
```

Rules:
- First item: `padding-left: 16px` (or set on container). Last item: `padding-right: 16px` so it clears the edge.
- Use `scroll-snap-type: x proximity`, not `mandatory`. Proximity allows free scroll between snap points without being yanked.
- Do NOT add gradient fade or arrow indicators unless you have 10+ items. Trust users — horizontal scroll is universally understood.

### ✅ JS: Auto-Scroll Active Tab to Center
When IntersectionObserver fires and active tab changes, scroll the nav:
```js
function scrollActiveTabIntoView(activeTab) {
  const nav = activeTab.closest('.subnav-scroll-container');
  const tabLeft = activeTab.offsetLeft;
  const tabWidth = activeTab.offsetWidth;
  const navWidth = nav.offsetWidth;
  nav.scrollTo({
    left: tabLeft - (navWidth / 2) + (tabWidth / 2),
    behavior: 'smooth'
  });
}
```
This is what Google and YouTube do. Active tab centers itself as you scroll the page.

### ❌ Don't Use: Hamburger / "Sections" Button Collapse
User loses visibility of all options. Defeats the entire purpose.

### ⚠️ Two-Row Layout (our current mobile fix)
Viable only if exactly 2 groups with ≤4 items each, and both groups are always meaningful at the same time. It doubles sub-nav height (~88px instead of 44px) — significant cost on mobile.

---

## 4. Active State — Exact Specs

### For scroll-driven (IntersectionObserver):
```css
.subnav-link { color: #4A6663; font-weight: 500; }
.subnav-link.active { color: #0F1E1C; font-weight: 600; }

.subnav-link::after {
  content: '';
  position: absolute;
  bottom: 0; left: 0; right: 0;
  height: 2px;
  background: #47857A;
  opacity: 0;
  transition: opacity 150ms ease;
}
.subnav-link.active::after { opacity: 1; }
```

**Do NOT slide the indicator on scroll** — it jumps if user fast-scrolls. Just fade the underline.

### IntersectionObserver — Best Algorithm
```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      navLinks.forEach(l => l.classList.remove('active'));
      const link = document.querySelector(`.subnav-link[href="#${entry.target.id}"]`);
      if (link) {
        link.classList.add('active');
        scrollActiveTabIntoView(link);
      }
    }
  });
}, {
  rootMargin: '-112px 0px -50% 0px',  /* top: below both navs; bottom: stop at 50% viewport */
  threshold: 0
});
```

`rootMargin: '-112px 0px -50% 0px'` means: only sections in the **upper half of visible content** (below both sticky navs) count as active. Prevents the bottom half of the page from firing incorrectly.

Reference: [Smooth Scrolling Sticky ScrollSpy — bram.us](https://www.bram.us/2020/01/10/smooth-scrolling-sticky-scrollspy-navigation/)

---

## 5. The Grouped Nav Problem (GCL + CoE)

This is our specific design challenge: 2 groups, each with 4 links.

### Option A: Single row with standalone separator + micro labels
```
[NABL Calibration ▸] Dimensional · Pressure · Acoustics · On-Site  |  [CoE ▸] CMM · Repair · Training
```
- "NABL Calibration" and "CoE" are non-clickable eyebrow labels: 9–10px, uppercase, `letter-spacing: 0.1em`, `brand-700`
- Separator: standalone `<span aria-hidden="true">` element: `width: 1px; height: 14px; background: rgba(71,133,122,0.25); margin: 0 12px; align-self: center`
- On mobile: labels are hidden (they exist in the page sections already), separator becomes part of the flow, single scroll row

### Option B: Color-coded items, no explicit labels
All 8 links in one row. GCL links = teal accent color when active. CoE links = amber accent color when active. The color alone communicates the group boundary.
- No separator, no labels — totally clean
- Risk: users may not immediately understand the grouping

### Option C: Two-row grouped (current mobile implementation, desktop single row)
- Cleanest grouping signal on mobile
- Expensive in vertical space (~88px vs 44px)
- The current fix — acceptable but not premium

### Recommendation
**Desktop**: Option A (single row, micro labels, separator).
**Mobile**: Single scroll row with color coding (Option B) — labels consume too much width on mobile. The active amber vs teal color is sufficient grouping cue at mobile scale.

---

## 6. Transition Reference from Real Sites

| Site | Property | Duration | Easing |
|---|---|---|---|
| Apple | border-bottom opacity | 150ms | ease |
| Airbnb | border-bottom opacity | 150ms | ease-out |
| Vercel (click) | indicator translateX + width | 200ms | cubic-bezier(0.4, 0, 0.2, 1) |
| Stripe sidebar | border-left color | 200ms | ease |
| Linear | color | 150ms | ease |
| Google tabs | indicator translate | 300ms | cubic-bezier(0.2, 0, 0, 1) |

Rule: 150–200ms for color/opacity, 200–300ms for translate (covers visible distance).

---

## 7. The 10 Details That Separate Premium from Generic

| # | Generic | Premium |
|---|---|---|
| 1 | 56–64px height | 44–48px height |
| 2 | `box-shadow` on scroll | `border-bottom: 1px solid rgba(...)` hairline |
| 3 | Opaque white background | `rgba(251,254,253,0.92)` + `backdrop-filter: blur(12px)` |
| 4 | Active text = `brand-500` color | Active text = `brand-ink` (#0F1E1C). Accent color only in underline. |
| 5 | Inactive text = `#333` (too dark) | Inactive text = `brand-ink-light` (#4A6663) — deemphasized |
| 6 | Text area only tap target | `height: 44px` tap target (Apple minimum), flexbox-centered label |
| 7 | `border-radius: 4px` on underline | Sharp 2px or `border-radius: 1px 1px 0 0` only |
| 8 | Color change on active | Weight change (400→600) + color darkening. Font weight is the primary signal. |
| 9 | Group separator = `border-right` on item | Standalone separator `<span>` with its own margins |
| 10 | Auto-centering active tab missing | JS scrolls active tab to center of bar on mobile |

---

## 8. Full Design Spec for Services Page Sub-Nav Redesign

### Desktop (≥768px)
```
Height:           48px
Background:       rgba(251,254,253,0.92) + backdrop-filter: blur(12px) saturate(150%)
Border:           border-bottom: 1px solid rgba(71,133,122,0.12)
Position:         sticky; top: 64px; z-index: 40

Layout:           flex; align-items: center; justify-content: center; gap: 0
Padding:          0 24px

Group label:      font-size: 10px; font-weight: 600; letter-spacing: 0.1em; text-transform: uppercase;
                  color: #2D5D54 (GCL) / #92400e (CoE); margin-right: 4px; not clickable

Link:             font-size: 13–14px; font-weight: 500; color: #4A6663; padding: 0 14px; height: 48px
Link hover:       color: #2D5D54 (GCL) / amber-700 (CoE); transition: 120ms
Link active:      color: #0F1E1C; font-weight: 600; + 2px underline opacity 1

Separator:        <span aria-hidden> width:1px; height:14px; background:rgba(71,133,122,0.25); margin:0 12px; align-self:center
```

### Mobile (< 768px)
```
Height:           44px
Layout:           single horizontal scroll row
overflow-x:       auto; scrollbar-width: none; -webkit-overflow-scrolling: touch
scroll-snap-type: x proximity
Padding:          0 0 0 16px (first item starts at 16px; last item needs right padding via pseudo-element)

Group labels:     HIDE on mobile
Separator:        keep (narrow margins: 0 6px)
Links:            flex-shrink: 0; scroll-snap-align: start; white-space: nowrap; padding: 0 12px
Active tab:       JS scrolls it to center of bar
```

### scroll-margin-top on sections
```css
section[id] {
  scroll-margin-top: 120px;  /* 64px main nav + 48px sub-nav + 8px breathing room */
}
/* On mobile with two-row sub-nav: */
@media (max-width: 767px) {
  section[id] { scroll-margin-top: 156px; }  /* 64 + 88 + 4 */
}
```

---

## Sources

- [Smashing Magazine — Designing Sticky Menus: UX Guidelines](https://www.smashingmagazine.com/2023/05/sticky-menus-ux-guidelines/)
- [bram.us — Smooth Scrolling Sticky ScrollSpy Navigation](https://www.bram.us/2020/01/10/smooth-scrolling-sticky-scrollspy-navigation/)
- [web.dev — Building a Tabs component](https://web.dev/articles/building/a-tabs-component)
- [CSS-Tricks — Sticky Table of Contents with Scrolling Active States](https://css-tricks.com/sticky-table-of-contents-with-scrolling-active-states/)
- [NN/g — Tabs, Used Right](https://www.nngroup.com/articles/tabs-used-right/)
- [Apple Mac Pro page deconstruction](https://ihatetomatoes.net/apple-mac-pro-page-deconstructed/)
- [Airbnb sticky widget analysis — Appcues](https://goodux.appcues.com/blog/airbnbs-sticky-widget)
