# Sub-Nav Redesign — Design Spec

**Date:** 2026-03-18
**Status:** Approved by owner

---

## Goal

Replace the current grouped pill-container sub-nav on `services.html` with a single flat row using pill hover interactions, teal/amber colour coding by division, backdrop blur bar, and JS auto-centering of the active tab on mobile.

---

## Design Decisions

### Visual Style
- **Single horizontal row** — all 8 links in one flat row, no separate containers per group
- **Pill hover** — links get a rounded pill background on hover and active
- **No group labels** — "GCL" / "CoE" text labels removed; colour alone signals the group
- **Separator** — standalone 1px × 14px vertical line between GCL and CoE groups

### Bar
| Property | Value |
|---|---|
| Height | 48px |
| Background | `rgba(251,254,253,0.92)` + `backdrop-filter: blur(12px)` |
| Border | `border-bottom: 1px solid rgba(71,133,122,0.12)` |
| Position | `sticky; top: 64px; z-index: 40` |

No box-shadow. Hairline border only.

### Link States

| State | GCL links | CoE links |
|---|---|---|
| Inactive | `#4A6663` weight 500 | same |
| Hover | pill `rgba(71,133,122,0.08)` + `color #2D5D54` | pill `rgba(180,119,40,0.08)` + `color #92400e` |
| Active | pill `rgba(71,133,122,0.14)` + `color #2D5D54` weight 600 | pill `rgba(180,119,40,0.14)` + `color #92400e` weight 600 |

Pill shape: `border-radius: 999px`, padding `6px 13px`, transition `150ms ease`.

### Separator Element
```html
<span class="subnav-sep" aria-hidden="true"></span>
```
```css
.subnav-sep {
  width: 1px; height: 14px;
  background: rgba(71,133,122,0.22);
  margin: 0 8px;
  flex-shrink: 0;
  align-self: center;
}
```

### Mobile Behaviour
- Single horizontal scroll row (no two-row layout)
- `overflow-x: auto`, `scrollbar-width: none`, `-webkit-overflow-scrolling: touch`
- `scroll-snap-type: x proximity`
- Each link: `flex-shrink: 0`, `scroll-snap-align: start`
- First link has left padding 16px; last link right padding 16px
- **JS auto-center**: when active tab changes, the nav scrolls to center the active link

### IntersectionObserver
```
rootMargin: '-112px 0px -50% 0px'
```
Only sections in the upper half of visible content (below both navs) register as active.

### scroll-margin-top on sections
```css
section[id] { scroll-margin-top: 120px; }
/* 64px header + 48px subnav + 8px breathing room */
```

---

## What Changes in `services.html`

1. **CSS block** — replace `.subnav-link`, `.subnav-group-lab`, `.subnav-group-coe` rules with new flat-row rules
2. **HTML** — replace the two pill-group `<div>`s with 8 individual `<a>` tags + one `<span class="subnav-sep">`
3. **JS** — update IntersectionObserver `rootMargin`, add `scrollActiveTabIntoView()` helper, mark CoE links with `data-group="coe"` for colour switching

---

## Out of Scope
- No changes to desktop main nav
- No changes to any other page
- No changes to section content
