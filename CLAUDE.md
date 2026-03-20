# CLAUDE.md — German Calibration Website

> Auto-loaded by Claude Code at every session start.
> This project: public website for German Calibration Lab LLP (NABL-accredited calibration lab, Rajkot).
> Ground-truth brand facts: `brand_knowledge.md` | Full site plan: `website_plan.md` | Services spec: `docs/services-page-plan.md`

---

## 1. Project Identity

| Field | Value |
|---|---|
| Legal name | GERMAN CALIBRATION LAB LLP |
| Display name | German Calibration |
| Second division | German Center of Excellence (CoE) |
| NABL Certificate | CC-4038 · ISO/IEC 17025:2017 · Valid till 27 August 2029 |
| Location | Rajkot, Gujarat, India |
| Phone | +91 9276551009 |
| Email | lab@germancalibration.com |
| Portal | https://portal.germancalibration.com |

---

## 2. Tech Stack — Non-Negotiable

- **Single `.html` file per page** — all styles inline via Tailwind CDN + `<style>` block. No separate CSS files.
- **Tailwind CDN** via `<script src="https://cdn.tailwindcss.com">` — NOT npm Tailwind.
- **Tailwind config** extended inside a `<script>` tag (see Section 5 for exact config).
- **Fonts**: DM Serif Display (display/headings) + Inter (body/UI) — loaded from Google Fonts CDN.
- **No JS frameworks** — vanilla JS only. No React, Vue, Alpine.js, etc.
- **No build tools** — files open directly in browser. No webpack, vite, parcel.
- **Placeholder images**: `https://placehold.co/WIDTHxHEIGHT/47857A/FBFEFD?text=Description`

---

## 3. File Structure

```
German Website/
├── index.html              ✅ DONE (light theme, master branch)
├── services.html           ✅ DONE
├── accreditation.html      ← after services
├── about.html
├── industries.html
├── contact.html
├── brand items/
│   ├── german_logo.svg     ← use this logo, never placeholders for logo
│   ├── Certificate CC-4038.pdf.pdf
│   └── Scop 4038 .pdf
├── docs/
│   └── services-page-plan.md  ← services.html build spec
├── brand_knowledge.md      ← brand facts ground truth
├── website_plan.md         ← full site plan + design tokens
└── RESEARCH_Calibration_Website_Analysis.md
```

**Git branches:**
- `master` — live site, light theme. Always work here.
- `dark-theme` — preserved for reference only. Do NOT delete. Do NOT merge into master.

---

## 4. Design System — Colors

```js
// Exact Tailwind config — copy this into every new page's <script> tag
tailwind.config = {
  theme: {
    extend: {
      colors: {
        brand: {
          50:  '#E6F2F0',
          100: '#D8EAE7',
          200: '#C2DDD9',
          300: '#8BBDB7',
          400: '#5E9E97',
          500: '#47857A',
          600: '#3A6E64',
          700: '#2D5D54',
          800: '#224845',
          900: '#1A3832',
          ink: '#0F1E1C',
          'ink-light': '#4A6663',
          white: '#FBFEFD',
        },
      },
      fontFamily: {
        serif: ['"DM Serif Display"', 'Georgia', 'serif'],
        sans:  ['Inter', 'system-ui', 'sans-serif'],
      },
      boxShadow: {
        'teal-sm': '0 1px 2px rgba(71,133,122,0.06)',
        'teal-md': '0 4px 6px rgba(71,133,122,0.07), 0 2px 4px rgba(71,133,122,0.04)',
        'teal-lg': '0 10px 15px rgba(71,133,122,0.08), 0 4px 6px rgba(71,133,122,0.05)',
        'teal-xl': '0 20px 25px rgba(71,133,122,0.10), 0 8px 10px rgba(71,133,122,0.06)',
      },
      letterSpacing: {
        display: '-0.03em',
        heading: '-0.02em',
      },
    },
  },
}
```

**Surface layers (light theme):**

| Layer | Background | Use |
|---|---|---|
| Base | `#FBFEFD` (`brand-white`) | Page background, navbar |
| Elevated | `#FFFFFF` | Cards, form inputs |
| Recessed | `#E6F2F0` (`brand-50`) | Footer, alt sections, portal section |
| Alt-section | `#F0F7F5` | Trust bar, subtle alternating sections |
| Accent-dark | `#47857A` (`brand-500`) | CTA cards, accent blocks, ZEISS card |
| Hero | `#EAF3F1` | Hero background only (slightly lighter than brand-50) |

---

## 5. Design System — WCAG Contrast Rules ⚠️ CRITICAL

**brand-500 (#47857A) contrast ratios:**
- On white (#FBFEFD): **4.22:1** — FAILS WCAG AA for normal text (needs 4.5:1)
- On white: PASSES for large text only (≥24px regular serif, or ≥18.67px bold — needs 3:1)

**brand-700 (#2D5D54) contrast ratios:**
- On white (#FBFEFD): **6.62:1** — PASSES WCAG AA for all text sizes ✅

**Rules to follow every time:**

| Use case | Correct class | Wrong class |
|---|---|---|
| Small text links, captions, labels, badges | `text-brand-700` | ~~`text-brand-500`~~ |
| Eyebrow/section labels (uppercase small) | `text-brand-700` | ~~`text-brand-500`~~ |
| "Learn more" inline links (small) | `text-brand-700` | ~~`text-brand-500`~~ |
| Footer links | `text-brand-ink-light hover:text-brand-700` | ~~`hover:text-brand-500`~~ |
| Required asterisks `*` in forms | `text-brand-700` | ~~`text-brand-500`~~ |
| Icon colors (small icons, <24px) | `text-brand-500` is OK (non-text element) | — |
| Large headings ≥40px | `text-brand-500` OK (passes large text 3:1) | — |
| btn-primary background | `background: #2D5D54` | ~~`background: #47857A`~~ |
| btn-primary hover | `background: #224845` | — |
| Hover states on small text | `hover:text-brand-700` or `hover:text-brand-800` | ~~`hover:text-brand-500`~~ |

**Never use opacity-blended teal on light backgrounds for small text:**
`text-brand-500/70`, `text-brand-ink-light/70` with teal hover — compute actual contrast before using.

---

## 6. Design System — Typography

| Role | Font | Size | Weight | Tracking |
|---|---|---|---|---|
| Hero headline | DM Serif Display | clamp(2.5rem, 5.5vw, 4rem) | 400 | -0.03em |
| Section heading | DM Serif Display or Inter 700 | 2rem–2.5rem | 700 | -0.02em |
| Card heading | Inter | 1.125rem–1.25rem | 600 | -0.01em |
| Body text | Inter | 1rem–1.125rem | 400 | normal |
| Caption/meta | Inter | 0.75rem–0.875rem | 400–500 | 0.01em |
| Eyebrow label | Inter | 0.75rem | 600 | 0.08em, uppercase |

---

## 7. Shared Components — Copy These Exactly

### Google Fonts import (in `<head>`)
```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet" />
```

### btn-primary CSS
```css
.btn-primary {
  display: inline-flex; align-items: center; gap: 8px;
  background: #2D5D54; color: #FBFEFD;
  font-weight: 600; font-size: 0.9375rem;
  padding: 14px 28px; border-radius: 6px;
  letter-spacing: -0.01em;
  transition: background 0.2s, transform 0.15s, box-shadow 0.2s;
  text-decoration: none;
}
.btn-primary:hover { background: #224845; transform: translateY(-1px); box-shadow: 0 8px 20px rgba(71,133,122,0.28); }
.btn-primary:active { transform: translateY(0); }
```

### NABL badge
```html
<span class="nabl-badge">
  <svg class="w-3 h-3" viewBox="0 0 12 12" fill="currentColor" aria-hidden="true">
    <path d="M6 0L7.854 4.146 12 4.618 9 7.54 9.708 12 6 9.854 2.292 12 3 7.54 0 4.618 4.146 4.146z"/>
  </svg>
  NABL Accredited · CC-4038
</span>
```
```css
.nabl-badge {
  display: inline-flex; align-items: center; gap: 6px;
  background: rgba(71,133,122,0.12); border: 1px solid rgba(71,133,122,0.35);
  color: #2D5D54; font-size: 0.75rem; font-weight: 600;
  letter-spacing: 0.08em; text-transform: uppercase;
  padding: 4px 10px; border-radius: 4px;
}
```

### Scroll reveal (CSS + JS pattern)
```css
.reveal { opacity: 0; transform: translateY(28px); transition: opacity 0.65s cubic-bezier(0.22, 1, 0.36, 1), transform 0.65s cubic-bezier(0.22, 1, 0.36, 1); }
.reveal.visible { opacity: 1; transform: translateY(0); }
.reveal-delay-1 { transition-delay: 0.1s; }
.reveal-delay-2 { transition-delay: 0.2s; }
.reveal-delay-3 { transition-delay: 0.3s; }
.reveal-delay-4 { transition-delay: 0.4s; }
```
```js
const revealObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => { if (entry.isIntersecting) { entry.target.classList.add('visible'); revealObserver.unobserve(entry.target); } });
}, { threshold: 0.12, rootMargin: '0px 0px -40px 0px' });
document.querySelectorAll('.reveal').forEach(el => revealObserver.observe(el));
```

### Header scroll effect (JS)
```js
const header = document.getElementById('site-header');
window.addEventListener('scroll', () => { header.classList.toggle('scrolled', window.scrollY > 60); }, { passive: true });
```
```css
#site-header { transition: background-color 0.3s ease, box-shadow 0.3s ease; }
#site-header.scrolled { background-color: rgba(251,254,253,0.97); box-shadow: 0 1px 16px rgba(71,133,122,0.10); backdrop-filter: blur(8px); }
```

---

## 8. Content Rules — NEVER Violate

1. **NEVER publish client names** on any public page — no Tata, L&T, SKF, Reliance, Mahindra, Atlas Copco, Legrand, Anchor, Adani, Faurecia, Sony, FAG, Bharat Gear, Atlas Corporation. Owner's explicit privacy preference.

2. **NEVER label CoE services as NABL accredited.** German Center of Excellence (Third Party Inspection, CMM, Training, Repair) is NOT NABL. Never use "NABL" near Division 2 content. Use "CoE" badge instead.

3. **NABL badge rules** (NABL 133 + ILAC-R7): NABL symbol must be B&W only. ILAC-MRA mark B&W or ILAC-approved blue only. Static, not animated, not rotated. CC-4038 must accompany both marks.

4. **Two co-equal divisions**: Present German Calibration Lab LLP (NABL) and German Center of Excellence (CoE) with equal visual prominence. CoE is NOT secondary.

5. **Homepage client strip**: The scrolling company name marquee is marked for replacement with a static NABL/ILAC accreditation marks strip (pending logo files from NABL). Do not re-add company names there.

6. **Certificate number**: CC-4038 (not "4038" alone, not "CC4038" without hyphen).

7. **Standard**: ISO/IEC 17025:2017 (always include the year).

8. **Valid until**: 27 August 2029 (not "2029" alone where precision matters).

---

## 9. Page Build State

| Page | Status | Notes |
|---|---|---|
| `index.html` | ✅ Done | Light theme, all contrast fixes applied |
| `services.html` | ✅ Done | Both divisions, sticky sub-nav with logo, all sections |
| `accreditation.html` | 🔲 Pending | |
| `about.html` | 🔲 Pending | |
| `industries.html` | 🔲 Pending | |
| `contact.html` | ✅ Done | Contact hub, quote form, panel, Google Maps embed |

**Homepage pending** (waiting for NABL logo files — do not block services page on this):
- Remove client names from meta description + hero subheadline
- Rewrite Bearing Manufacturing card ("for SKF, FAG") and Electrical Equipment card
- Replace scrolling marquee (Section 6) with static accreditation marks strip

---

## 10. Services Page Architecture (`services.html`)

Two divisions. Full spec in `docs/services-page-plan.md`.

**Page structure:**
1. Compact hero (NOT full-screen) — heading, breadcrumb, two division preview cards, stats bar
2. Sticky in-page sub-nav (top: 64px) — 8 anchor links across both divisions, IntersectionObserver active state
3. **Division 1 — German Calibration Lab LLP** (teal accent, NABL badge on every heading)
   - `#dimensional` — 8 standout highlights + 5-group accordion + scope PDF CTA
   - `#pressure` — 3 cards: pneumatic / hydraulic (700 bar!) / vacuum with CMC values
   - `#acoustics` — sound level meter at 1kHz (94 dB + 114 dB)
   - `#onsite` — also NABL accredited (rare), instruments list, key message
4. Division separator — dark teal band
5. **Division 2 — German Center of Excellence** (amber/gold accent, CoE badge — NEVER NABL)
   - `#cmm` — ZEISS CONTURA split layout, 900×1200×800mm
   - `#repair` — 5 instrument types
   - `#training` — 4 programme cards
   - `#coming-soon` — 2 dashed teaser cards (3D Printing, Handheld ZEISS Scanner)
6. Dual CTA strip

**Services page design principles** (different from homepage):
- Compact hero — get to content fast, no full-screen hero
- Dense information layout — specs, CMC values, instrument groups matter here
- Sticky sub-nav is critical — page is long, users need to jump to specific services
- Contextual CTAs at end of each division, not just page bottom

---

## 11. Key Differentiators to Highlight (Use in Copy)

- External Micrometers up to **1000mm** (most labs max at 300–500mm)
- Calipers up to **2000mm** (exceptionally large)
- Surface Plates up to **2000×2000mm**
- **700 bar** hydraulic pressure (rare — most regional labs stop at 100–200 bar)
- **On-site calibration is also NABL accredited** (most labs only hold permanent facility accreditation)
- **ZEISS CONTURA CMM** (900×1200×800mm) — German-engineered precision
- Lab environment: **20°C ± 0.1°C**, **Class 10,000** cleanroom
- **ILAC-MRA** recognition — international mutual recognition arrangement

---

## 12. Placeholder Images

Always use brand-colored placeholders until real photos are available:
```
https://placehold.co/800x450/47857A/FBFEFD?text=Lab+Photo
https://placehold.co/600x400/47857A/FBFEFD?text=ZEISS+CMM
https://placehold.co/400x300/2D5D54/FBFEFD?text=Equipment
```

Logo is always real: `src="brand items/german_logo.svg"` (with fallback span for when SVG doesn't load).

---

## 13. Accessibility Baseline

- All interactive elements: `aria-label` on icon-only buttons
- Images: meaningful `alt` text (describe what the real photo should show)
- Forms: `<label for="">` linked to inputs, required fields marked with `aria-required="true"`
- Semantic HTML: `<header>`, `<nav>`, `<main>`, `<section aria-label="">`, `<footer>`
- Focus states: visible (Tailwind's `focus-visible:ring-2 focus-visible:ring-brand-500`)
- Contrast: all text ≥ 4.5:1 (AA). See Section 5 for specific rules.
