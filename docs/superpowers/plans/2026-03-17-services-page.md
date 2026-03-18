# Services Page Implementation Plan

> **For agentic workers:** Use superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `services.html` — a complete, production-ready services page for German Calibration, covering both Division 1 (NABL Calibration Lab) and Division 2 (German Center of Excellence).

**Architecture:** Single HTML file, all styles inline via Tailwind CDN + `<style>` block, vanilla JS only. Follows the exact same shell pattern as `index.html`. Page is divided into 8 anchored sections across two divisions, with a sticky in-page sub-nav for navigation.

**Tech Stack:** HTML5, Tailwind CDN (`cdn.tailwindcss.com`), Google Fonts CDN (DM Serif Display + Inter), Vanilla JS (IntersectionObserver for sticky sub-nav active state + scroll reveal, accordion for instrument groups).

**Spec:** `docs/services-page-plan.md` + `CLAUDE.md` sections 4–11

---

## Chunk 1: Page Shell, Navigation, Hero, and Sticky Sub-Nav

### Task 1: Page Shell (head, shared CSS, Tailwind config)

**Files:**
- Create: `services.html`

- [ ] Create `services.html` with the standard `<head>` block. Copy the Tailwind config, Google Fonts imports, and all shared `<style>` CSS (base, scroll reveal, header scroll, btn-primary, nabl-badge, mobile menu) exactly from `index.html`. This is NOT optional — every page must have the identical config.

  ```html
  <!DOCTYPE html>
  <html lang="en" class="scroll-smooth">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Our Services — German Calibration | NABL Accredited Lab, Rajkot</title>
    <meta name="description" content="Full range of NABL accredited calibration services — dimensional, pressure, acoustics, on-site — plus CMM inspection, repair and training from German Center of Excellence. CC-4038." />

    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet" />

    <script src="https://cdn.tailwindcss.com"></script>
    <script>
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
    </script>
  ```

- [ ] Add the `<style>` block with all shared CSS. Copy these exactly from `index.html` — do not rewrite them:
  - `*, *::before, *::after { box-sizing: border-box; }` + body base
  - `.reveal` / `.reveal.visible` / `.reveal-delay-1` through `-4`
  - `#site-header` scroll state (`.scrolled`)
  - `.nav-link` with underline hover animation
  - `.btn-primary` with hover/active states
  - `.nabl-badge`
  - `.mobile-menu` open/close animation
  - Add two new styles specific to services page:
    ```css
    /* ── CoE badge (amber — NEVER NABL) ── */
    .coe-badge {
      display: inline-flex; align-items: center; gap: 6px;
      background: rgba(180,120,40,0.10); border: 1px solid rgba(180,120,40,0.30);
      color: #7a4f0d; font-size: 0.75rem; font-weight: 600;
      letter-spacing: 0.08em; text-transform: uppercase;
      padding: 4px 10px; border-radius: 4px;
    }

    /* ── Accordion ── */
    .accordion-content {
      max-height: 0;
      overflow: hidden;
      transition: max-height 0.35s cubic-bezier(0.22, 1, 0.36, 1);
    }
    .accordion-content.open {
      max-height: 600px;
    }
    .accordion-icon {
      transition: transform 0.3s ease;
    }
    .accordion-btn[aria-expanded="true"] .accordion-icon {
      transform: rotate(180deg);
    }

    /* ── Sticky sub-nav ── */
    #services-subnav {
      position: sticky;
      top: 64px;
      z-index: 40;
      background: rgba(251,254,253,0.97);
      backdrop-filter: blur(8px);
      border-bottom: 1px solid rgba(71,133,122,0.12);
      transition: box-shadow 0.2s;
    }
    #services-subnav.elevated {
      box-shadow: 0 2px 12px rgba(71,133,122,0.10);
    }
    .subnav-link {
      font-size: 0.8125rem; font-weight: 500;
      color: #4A6663;
      padding: 12px 14px;
      border-bottom: 2px solid transparent;
      white-space: nowrap;
      transition: color 0.2s, border-color 0.2s;
    }
    .subnav-link:hover { color: #2D5D54; }
    .subnav-link.active {
      color: #2D5D54;
      border-bottom-color: #47857A;
      font-weight: 600;
    }

    /* ── Division separator ── */
    .division-sep {
      background: linear-gradient(135deg, #1A3832 0%, #2D5D54 100%);
    }

    /* ── Dashed teaser card ── */
    .teaser-card {
      border: 2px dashed rgba(71,133,122,0.30);
      border-radius: 12px;
    }

    /* ── prefers-reduced-motion ── */
    @media (prefers-reduced-motion: reduce) {
      .reveal { opacity: 1; transform: none; transition: none; }
      .accordion-content { transition: none; }
    }
    ```

- [ ] Open `services.html` in a browser. Verify: blank white page with no console errors.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: services page shell — head, tailwind config, shared CSS"
  ```

---

### Task 2: Navigation Header

**Files:**
- Modify: `services.html`

- [ ] Copy the entire `<header id="site-header">` block from `index.html` verbatim. The nav is identical on every page. The only difference: mark "Services" as the active page by changing its `nav-link` to include `text-brand-700 font-semibold` and removing the hover animation (it's already "here").

  Active nav item class: `text-brand-700 font-semibold text-sm`
  All other nav links: `nav-link text-brand-ink-light hover:text-brand-700 text-sm font-medium`

- [ ] Open in browser. Verify: logo appears, all nav links render, mobile hamburger visible on resize below 768px.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: services page navigation header"
  ```

---

### Task 3: Compact Hero

**Files:**
- Modify: `services.html`

- [ ] Add the `<main id="main-content">` tag after the header. Inside it, build the compact hero section. This is NOT full-screen — it should be roughly 220–280px tall on desktop. Background: `#EAF3F1` with the same radial gradient + precision grid overlay as index.html.

  ```html
  <section class="hero-bg py-14 md:py-20">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">

      <!-- Breadcrumb -->
      <nav aria-label="Breadcrumb" class="mb-5">
        <ol class="flex items-center gap-2 text-sm text-brand-ink-light">
          <li><a href="index.html" class="hover:text-brand-700 transition-colors">Home</a></li>
          <li aria-hidden="true" class="text-brand-300">›</li>
          <li class="text-brand-700 font-medium" aria-current="page">Services</li>
        </ol>
      </nav>

      <!-- Heading -->
      <h1 class="font-serif text-4xl md:text-5xl text-brand-ink mb-4" style="letter-spacing: -0.03em;">
        Our Services
      </h1>
      <p class="text-brand-ink-light text-lg max-w-2xl mb-8">
        Two specialised divisions. One commitment to measurement certainty.
      </p>

      <!-- Division preview cards -->
      <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 max-w-2xl">
        <!-- Division 1 -->
        <a href="#dimensional" class="group flex items-start gap-3 bg-white rounded-xl p-4 shadow-teal-md hover:shadow-teal-lg transition-shadow border border-brand-100">
          <div class="w-9 h-9 rounded-lg bg-brand-50 flex items-center justify-center flex-shrink-0 group-hover:bg-brand-100 transition-colors">
            <svg class="w-5 h-5 text-brand-500" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M9 3H5a2 2 0 00-2 2v4m6-6h10a2 2 0 012 2v4M9 3v18m0 0h10a2 2 0 002-2V9M9 21H5a2 2 0 01-2-2V9m0 0h18"/></svg>
          </div>
          <div>
            <span class="nabl-badge mb-1">NABL · CC-4038</span>
            <p class="font-semibold text-brand-ink text-sm mt-1">German Calibration Lab LLP</p>
            <p class="text-xs text-brand-ink-light mt-0.5">Dimensional · Pressure · Acoustics · On-Site</p>
          </div>
        </a>
        <!-- Division 2 -->
        <a href="#cmm" class="group flex items-start gap-3 bg-white rounded-xl p-4 shadow-teal-md hover:shadow-teal-lg transition-shadow border border-brand-100">
          <div class="w-9 h-9 rounded-lg bg-amber-50 flex items-center justify-center flex-shrink-0 group-hover:bg-amber-100 transition-colors">
            <svg class="w-5 h-5 text-amber-600" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"/></svg>
          </div>
          <div>
            <span class="coe-badge mb-1">CoE</span>
            <p class="font-semibold text-brand-ink text-sm mt-1">German Center of Excellence</p>
            <p class="text-xs text-brand-ink-light mt-0.5">CMM · Inspection · Repair · Training</p>
          </div>
        </a>
      </div>

    </div>
  </section>
  ```

- [ ] Open in browser. Verify: compact hero fits without dominating the screen. Both division cards visible. Breadcrumb readable.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: services page compact hero with division preview cards"
  ```

---

### Task 4: Sticky In-Page Sub-Nav

**Files:**
- Modify: `services.html`

- [ ] Add the sticky sub-nav immediately after the hero `</section>`, before `<main>` content begins:

  ```html
  <nav id="services-subnav" aria-label="Services sections">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex items-center overflow-x-auto gap-1 hide-scrollbar" style="-webkit-overflow-scrolling: touch;">

        <!-- Division 1 label -->
        <span class="text-xs font-600 text-brand-500 uppercase tracking-widest pr-3 border-r border-brand-100 mr-2 whitespace-nowrap hidden sm:block">Calibration Lab</span>

        <a href="#dimensional" class="subnav-link" data-section="dimensional">Dimensional</a>
        <a href="#pressure" class="subnav-link" data-section="pressure">Pressure</a>
        <a href="#acoustics" class="subnav-link" data-section="acoustics">Acoustics</a>
        <a href="#onsite" class="subnav-link" data-section="onsite">On-Site</a>

        <!-- Divider -->
        <span class="w-px h-5 bg-brand-100 mx-2 flex-shrink-0 hidden sm:block" aria-hidden="true"></span>
        <span class="text-xs font-600 text-amber-600 uppercase tracking-widest pr-3 border-r border-brand-100 mr-2 whitespace-nowrap hidden sm:block">CoE</span>

        <a href="#cmm" class="subnav-link" data-section="cmm">CMM &amp; Inspection</a>
        <a href="#repair" class="subnav-link" data-section="repair">Repair</a>
        <a href="#training" class="subnav-link" data-section="training">Training</a>
        <a href="#coming-soon" class="subnav-link" data-section="coming-soon">Coming Soon</a>

      </div>
    </div>
  </nav>
  ```

  Add to CSS:
  ```css
  .hide-scrollbar::-webkit-scrollbar { display: none; }
  .hide-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
  ```

- [ ] Add the JS for sub-nav active state (goes in the `<script>` block at bottom of body):

  ```js
  // ── Sticky sub-nav: active section tracking ──
  const subnav = document.getElementById('services-subnav');
  const subnavLinks = document.querySelectorAll('.subnav-link[data-section]');
  const sections = document.querySelectorAll('section[id]');

  const sectionObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        const id = entry.target.getAttribute('id');
        subnavLinks.forEach(link => {
          link.classList.toggle('active', link.dataset.section === id);
        });
      }
    });
  }, { threshold: 0.25, rootMargin: '-64px 0px -40% 0px' });

  sections.forEach(sec => sectionObserver.observe(sec));

  // Elevate sub-nav shadow when scrolled past hero
  window.addEventListener('scroll', () => {
    if (subnav) subnav.classList.toggle('elevated', window.scrollY > 200);
  }, { passive: true });
  ```

- [ ] Open in browser. Verify: sub-nav sticks below the main header when scrolling. Links are all readable. On mobile, sub-nav scrolls horizontally without showing a scrollbar.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: services sticky sub-nav with IntersectionObserver active state"
  ```

---

## Chunk 2: Division 1 — Dimensional Calibration

### Task 5: Dimensional Section — Standout Highlights

**Files:**
- Modify: `services.html`

- [ ] Start the Division 1 content area. Add a `<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">` wrapper. Then add the `#dimensional` section:

  ```html
  <section id="dimensional" aria-label="Dimensional Calibration" class="py-20 bg-brand-white">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">

      <!-- Section heading -->
      <div class="mb-12 reveal">
        <span class="nabl-badge mb-3">NABL Accredited · CC-4038</span>
        <h2 class="font-serif text-3xl md:text-4xl text-brand-ink mt-3 mb-3" style="letter-spacing:-0.02em;">
          Dimensional Calibration
        </h2>
        <p class="text-brand-ink-light text-lg max-w-2xl">
          Permanent facility accreditation under ISO/IEC 17025:2017. 91+ instrument types across 5 groups — from workshop gauges to optical measurement systems.
        </p>
      </div>

      <!-- Standout highlights grid (8 cards) -->
      <div class="mb-14">
        <p class="text-xs font-semibold text-brand-700 uppercase tracking-widest mb-5">What makes our scope exceptional</p>
        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">

          <!-- Card: External Micrometers -->
          <div class="reveal reveal-delay-1 bg-white rounded-xl p-5 border border-brand-100 shadow-teal-sm hover:shadow-teal-md transition-shadow">
            <div class="text-2xl font-bold text-brand-500 mb-1">1000mm</div>
            <div class="font-semibold text-brand-ink text-sm mb-1">External Micrometers</div>
            <div class="text-xs text-brand-ink-light">Most labs max at 300–500mm. We calibrate up to 1 metre.</div>
          </div>

          <!-- Card: Calipers -->
          <div class="reveal reveal-delay-2 bg-white rounded-xl p-5 border border-brand-100 shadow-teal-sm hover:shadow-teal-md transition-shadow">
            <div class="text-2xl font-bold text-brand-500 mb-1">2000mm</div>
            <div class="font-semibold text-brand-ink text-sm mb-1">Vernier / Dial / Electronic Calipers</div>
            <div class="text-xs text-brand-ink-light">Exceptionally large range — supports heavy engineering and fabrication.</div>
          </div>

          <!-- Card: Surface Plates -->
          <div class="reveal reveal-delay-3 bg-white rounded-xl p-5 border border-brand-100 shadow-teal-sm hover:shadow-teal-md transition-shadow">
            <div class="text-2xl font-bold text-brand-500 mb-1">2000×2000mm</div>
            <div class="font-semibold text-brand-ink text-sm mb-1">Surface Plates (Granite / Cast Iron)</div>
            <div class="text-xs text-brand-ink-light">Most labs have 600×600mm at best. Ours handles full-size production plates.</div>
          </div>

          <!-- Card: Reference Spheres -->
          <div class="reveal reveal-delay-4 bg-white rounded-xl p-5 border border-brand-100 shadow-teal-sm hover:shadow-teal-md transition-shadow">
            <div class="text-2xl font-bold text-brand-500 mb-1">±0.7µm</div>
            <div class="font-semibold text-brand-ink text-sm mb-1">Reference Spheres (CMC)</div>
            <div class="text-xs text-brand-ink-light">Sub-micron CMC uncertainty. Used for CMM qualification and high-precision inspection.</div>
          </div>

          <!-- Card: Profile Projectors -->
          <div class="reveal reveal-delay-1 bg-white rounded-xl p-5 border border-brand-100 shadow-teal-sm hover:shadow-teal-md transition-shadow">
            <div class="text-2xl font-bold text-brand-500 mb-1">10×–100×</div>
            <div class="font-semibold text-brand-ink text-sm mb-1">Profile Projectors / VMM</div>
            <div class="text-xs text-brand-ink-light">Optical measurement up to 360° angular, 0–300mm linear. Rarely available locally.</div>
          </div>

          <!-- Card: Spline Gauges -->
          <div class="reveal reveal-delay-2 bg-white rounded-xl p-5 border border-brand-100 shadow-teal-sm hover:shadow-teal-md transition-shadow">
            <div class="text-2xl font-bold text-brand-500 mb-1">Spline</div>
            <div class="font-semibold text-brand-ink text-sm mb-1">Spline Plug & Ring Gauges</div>
            <div class="text-xs text-brand-ink-light">Specialised gear industry tooling — uncommon in regional calibration labs.</div>
          </div>

          <!-- Card: Lead & Form Gauges -->
          <div class="reveal reveal-delay-3 bg-white rounded-xl p-5 border border-brand-100 shadow-teal-sm hover:shadow-teal-md transition-shadow">
            <div class="text-2xl font-bold text-brand-500 mb-1">Lead &amp; Form</div>
            <div class="font-semibold text-brand-ink text-sm mb-1">Lead & Form Gauges</div>
            <div class="text-xs text-brand-ink-light">For gear and thread manufacturing — radius, distance, depth, pitch, angle.</div>
          </div>

          <!-- Card: PCD Gauges -->
          <div class="reveal reveal-delay-4 bg-white rounded-xl p-5 border border-brand-100 shadow-teal-sm hover:shadow-teal-md transition-shadow">
            <div class="text-2xl font-bold text-brand-500 mb-1">600mm</div>
            <div class="font-semibold text-brand-ink text-sm mb-1">CD / PCD Gauges</div>
            <div class="text-xs text-brand-ink-light">Pitch Circle Diameter gauges for component inspection up to 600mm diameter.</div>
          </div>

        </div>
      </div>
  ```

- [ ] Open in browser. Verify: 8 highlight cards render in a 4-column grid on desktop, 2-column on tablet, 1-column on mobile. Bold numbers stand out clearly.

---

### Task 6: Dimensional Section — Instrument Groups Accordion

**Files:**
- Modify: `services.html`

- [ ] Continue inside the `#dimensional` section. Add the 5-group accordion:

  ```html
      <!-- Instrument groups accordion -->
      <div class="mb-10 reveal">
        <p class="text-xs font-semibold text-brand-700 uppercase tracking-widest mb-4">Full scope — instrument groups</p>

        <div class="space-y-2" id="dim-accordion">

          <!-- Group 1 -->
          <div class="border border-brand-100 rounded-xl overflow-hidden">
            <button class="accordion-btn w-full flex items-center justify-between p-4 bg-white hover:bg-brand-50 transition-colors text-left" aria-expanded="false" data-target="group1">
              <span class="font-semibold text-brand-ink text-sm">Group 1 — Basic Measuring Instruments</span>
              <svg class="accordion-icon w-4 h-4 text-brand-500 flex-shrink-0" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7"/></svg>
            </button>
            <div class="accordion-content" id="group1">
              <div class="px-4 pb-4 pt-1 bg-white">
                <p class="text-sm text-brand-ink-light leading-relaxed">
                  Calipers (vernier / dial / electronic, up to 2000mm) · Micrometers (external up to 1000mm, internal, depth, thread pitch) · Height gauges · Depth gauges · Bore dial gauges · Dial comparators · Lever dial gauges · Coating thickness gauges · Feeler gauges
                </p>
              </div>
            </div>
          </div>

          <!-- Group 2 -->
          <div class="border border-brand-100 rounded-xl overflow-hidden">
            <button class="accordion-btn w-full flex items-center justify-between p-4 bg-white hover:bg-brand-50 transition-colors text-left" aria-expanded="false" data-target="group2">
              <span class="font-semibold text-brand-ink text-sm">Group 2 — Gauges</span>
              <svg class="accordion-icon w-4 h-4 text-brand-500 flex-shrink-0" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7"/></svg>
            </button>
            <div class="accordion-content" id="group2">
              <div class="px-4 pb-4 pt-1 bg-white">
                <p class="text-sm text-brand-ink-light leading-relaxed">
                  Plain plug gauges (up to 500mm) · Plain ring gauges (up to 300mm) · Plain snap gauges (up to 800mm) · Thread ring gauges (up to 500mm) · Taper plug / ring gauges · Parallel thread plug gauges · Air gauge units · Spline plug / ring gauges · Dial snap gauges
                </p>
              </div>
            </div>
          </div>

          <!-- Group 3 -->
          <div class="border border-brand-100 rounded-xl overflow-hidden">
            <button class="accordion-btn w-full flex items-center justify-between p-4 bg-white hover:bg-brand-50 transition-colors text-left" aria-expanded="false" data-target="group3">
              <span class="font-semibold text-brand-ink text-sm">Group 3 — Surface & Reference Standards</span>
              <svg class="accordion-icon w-4 h-4 text-brand-500 flex-shrink-0" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7"/></svg>
            </button>
            <div class="accordion-content" id="group3">
              <div class="px-4 pb-4 pt-1 bg-white">
                <p class="text-sm text-brand-ink-light leading-relaxed">
                  Surface plates — granite / cast iron (up to 2000×2000mm) · Straight edges · Engineer's squares · Granite squares · V-blocks · Square masters / cylinders · Measuring pins · Micrometer setting standards (up to 775mm) · Depth masters · Bevel protectors · Thread measuring wires
                </p>
              </div>
            </div>
          </div>

          <!-- Group 4 -->
          <div class="border border-brand-100 rounded-xl overflow-hidden">
            <button class="accordion-btn w-full flex items-center justify-between p-4 bg-white hover:bg-brand-50 transition-colors text-left" aria-expanded="false" data-target="group4">
              <span class="font-semibold text-brand-ink text-sm">Group 4 — Form, Thread & Inspection</span>
              <svg class="accordion-icon w-4 h-4 text-brand-500 flex-shrink-0" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7"/></svg>
            </button>
            <div class="accordion-content" id="group4">
              <div class="px-4 pb-4 pt-1 bg-white">
                <p class="text-sm text-brand-ink-light leading-relaxed">
                  Form gauges · Lead gauges (radius / distance / depth / pitch / angle) · Inspection fixtures · Relation gauges · CD / PCD gauges (up to 600mm) · Templates · Thread pitch micrometers
                </p>
              </div>
            </div>
          </div>

          <!-- Group 5 -->
          <div class="border border-brand-100 rounded-xl overflow-hidden">
            <button class="accordion-btn w-full flex items-center justify-between p-4 bg-white hover:bg-brand-50 transition-colors text-left" aria-expanded="false" data-target="group5">
              <span class="font-semibold text-brand-ink text-sm">Group 5 — Precision & Optical Instruments</span>
              <svg class="accordion-icon w-4 h-4 text-brand-500 flex-shrink-0" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7"/></svg>
            </button>
            <div class="accordion-content" id="group5">
              <div class="px-4 pb-4 pt-1 bg-white">
                <p class="text-sm text-brand-ink-light leading-relaxed">
                  Profile projectors / Video measuring machines (10×–100×, 0–300mm linear, 0°–360° angular) · Surface roughness testers + masters · Electronic height gauges (up to 600mm) · Caliper checkers (up to 670mm) · Dial calibration testers · Reference spheres (CMC ±0.7µm)
                </p>
              </div>
            </div>
          </div>

        </div><!-- end accordion -->
      </div>

      <!-- Scope PDF CTA -->
      <div class="reveal flex flex-wrap gap-4 items-center p-5 bg-brand-50 rounded-xl border border-brand-100">
        <div class="flex-1 min-w-0">
          <p class="font-semibold text-brand-ink text-sm">Complete NABL Scope of Accreditation</p>
          <p class="text-xs text-brand-ink-light mt-0.5">18 pages · 91+ instrument types · CC-4038 · ISO/IEC 17025:2017</p>
        </div>
        <div class="flex gap-3 flex-shrink-0">
          <a href="brand items/Scop 4038 .pdf" download class="btn-primary text-sm py-2.5 px-5">
            <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"/></svg>
            Download Scope PDF
          </a>
          <a href="https://www.nabl-india.org/nabl/nabl-lab-search.php" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-2 text-brand-700 font-semibold text-sm hover:text-brand-800 transition-colors">
            Verify on NABL →
          </a>
        </div>
      </div>

    </div>
  </section>
  ```

- [ ] Add accordion JS to the `<script>` block at bottom:

  ```js
  // ── Accordion ──
  document.querySelectorAll('.accordion-btn').forEach(btn => {
    btn.addEventListener('click', () => {
      const targetId = btn.dataset.target;
      const content = document.getElementById(targetId);
      const isOpen = btn.getAttribute('aria-expanded') === 'true';

      // Close all others in same accordion group
      const group = btn.closest('[id$="-accordion"]') || btn.closest('.space-y-2');
      if (group) {
        group.querySelectorAll('.accordion-btn').forEach(b => {
          if (b !== btn) {
            b.setAttribute('aria-expanded', 'false');
            const c = document.getElementById(b.dataset.target);
            if (c) c.classList.remove('open');
          }
        });
      }

      btn.setAttribute('aria-expanded', String(!isOpen));
      content.classList.toggle('open', !isOpen);
    });
  });
  ```

- [ ] Open in browser. Click each accordion group — it should open smoothly and close the previous one. Verify the Scope PDF CTA strip renders correctly.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: dimensional section — highlights grid, accordion groups, scope PDF CTA"
  ```

---

## Chunk 3: Division 1 — Pressure, Acoustics, On-Site

### Task 7: Pressure Calibration Section

**Files:**
- Modify: `services.html`

- [ ] Add the `#pressure` section after `#dimensional`. Background: `bg-brand-50` (slightly recessed, creates visual rhythm).

  ```html
  <section id="pressure" aria-label="Pressure Calibration" class="py-20 bg-brand-50">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">

      <div class="mb-12 reveal">
        <span class="nabl-badge mb-3">NABL Accredited · CC-4038</span>
        <h2 class="font-serif text-3xl md:text-4xl text-brand-ink mt-3 mb-3" style="letter-spacing:-0.02em;">
          Pressure Calibration
        </h2>
        <p class="text-brand-ink-light text-lg max-w-2xl">
          Permanent facility. Pneumatic, hydraulic, and vacuum — covering the full pressure spectrum used in manufacturing and process industries.
        </p>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-3 gap-6">

        <!-- Pneumatic -->
        <div class="reveal reveal-delay-1 bg-white rounded-2xl p-7 shadow-teal-md border border-brand-100">
          <div class="w-11 h-11 rounded-xl bg-brand-50 flex items-center justify-center mb-5">
            <svg class="w-6 h-6 text-brand-500" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><circle cx="12" cy="12" r="9"/><path stroke-linecap="round" d="M12 7v5l3 3"/></svg>
          </div>
          <h3 class="font-semibold text-brand-ink text-lg mb-1">Pneumatic</h3>
          <div class="text-brand-500 font-bold text-2xl mb-1">0 – 25 bar</div>
          <div class="text-xs text-brand-700 font-semibold uppercase tracking-wide mb-3">CMC Uncertainty: ±0.052 bar</div>
          <p class="text-sm text-brand-ink-light leading-relaxed">
            Air and gas pressure measurement. Covers the full range of pneumatic instruments used on manufacturing shop floors.
          </p>
        </div>

        <!-- Hydraulic — highlighted (standout capability) -->
        <div class="reveal reveal-delay-2 bg-brand-700 rounded-2xl p-7 shadow-teal-lg relative overflow-hidden">
          <!-- Subtle background shape -->
          <div class="absolute -top-6 -right-6 w-28 h-28 rounded-full bg-brand-600 opacity-40" aria-hidden="true"></div>
          <div class="relative">
            <div class="w-11 h-11 rounded-xl bg-brand-600 flex items-center justify-center mb-5">
              <svg class="w-6 h-6 text-brand-200" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M19.428 15.428a2 2 0 00-1.022-.547l-2.387-.477a6 6 0 00-3.86.517l-.318.158a6 6 0 01-3.86.517L6.05 15.21a2 2 0 00-1.806.547M8 4h8l-1 1v5.172a2 2 0 00.586 1.414l5 5c1.26 1.26.367 3.414-1.415 3.414H4.828c-1.782 0-2.674-2.154-1.414-3.414l5-5A2 2 0 009 10.172V5L8 4z"/></svg>
            </div>
            <span class="inline-block bg-brand-500 text-white text-xs font-bold uppercase tracking-widest px-2 py-0.5 rounded mb-3">Rare Capability</span>
            <h3 class="font-semibold text-white text-lg mb-1">Hydraulic</h3>
            <div class="text-brand-200 font-bold text-2xl mb-1">0 – 700 bar</div>
            <div class="text-xs text-brand-300 font-semibold uppercase tracking-wide mb-3">CMC Uncertainty: ±0.8 bar</div>
            <p class="text-sm text-brand-100 leading-relaxed">
              Most regional labs stop at 100–200 bar. Our 700 bar capability serves high-pressure hydraulic systems in heavy engineering, aerospace tooling, and industrial machinery.
            </p>
          </div>
        </div>

        <!-- Vacuum -->
        <div class="reveal reveal-delay-3 bg-white rounded-2xl p-7 shadow-teal-md border border-brand-100">
          <div class="w-11 h-11 rounded-xl bg-brand-50 flex items-center justify-center mb-5">
            <svg class="w-6 h-6 text-brand-500" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M20 12H4M12 4l-8 8 8 8"/></svg>
          </div>
          <h3 class="font-semibold text-brand-ink text-lg mb-1">Vacuum</h3>
          <div class="text-brand-500 font-bold text-2xl mb-1">−0.85 to 0 bar</div>
          <div class="text-xs text-brand-700 font-semibold uppercase tracking-wide mb-3">CMC Uncertainty: ±0.011 bar</div>
          <p class="text-sm text-brand-ink-light leading-relaxed">
            Below-atmospheric pressure calibration. Uncommon in regional labs. Required for vacuum forming, packaging, and process control instruments.
          </p>
        </div>

      </div>
    </div>
  </section>
  ```

- [ ] Open in browser. Verify: 3-column layout on desktop. Hydraulic card is teal-dark and visually prominent. CMC values and ranges are readable.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: pressure calibration section — 3 cards with CMC values"
  ```

---

### Task 8: Acoustics Section

**Files:**
- Modify: `services.html`

- [ ] Add the compact `#acoustics` section. This is a smaller service — treat it as a feature band, not a full section.

  ```html
  <section id="acoustics" aria-label="Acoustics Calibration" class="py-16 bg-brand-white">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex flex-col md:flex-row md:items-center gap-8 reveal">

        <!-- Left: text -->
        <div class="flex-1">
          <span class="nabl-badge mb-3">NABL Accredited · CC-4038</span>
          <h2 class="font-serif text-3xl md:text-4xl text-brand-ink mt-3 mb-3" style="letter-spacing:-0.02em;">
            Acoustics Calibration
          </h2>
          <p class="text-brand-ink-light text-base max-w-xl mb-4">
            Sound Level Meter calibration at 1 kHz reference frequency. Two calibration levels: 94 dB (Class 1) and 114 dB (Class 1). An unusual capability for a dimensional calibration lab — our accreditation covers this breadth.
          </p>
          <p class="text-sm text-brand-ink-light">
            Required by: occupational health & safety teams, environmental monitoring, factory noise compliance.
          </p>
        </div>

        <!-- Right: spec card -->
        <div class="flex-shrink-0 bg-brand-50 rounded-2xl p-6 border border-brand-100 w-full md:w-64">
          <p class="text-xs font-semibold text-brand-700 uppercase tracking-widest mb-4">Specification</p>
          <div class="space-y-3">
            <div>
              <div class="text-xs text-brand-ink-light">Instrument</div>
              <div class="font-semibold text-brand-ink text-sm">Sound Level Meter</div>
            </div>
            <div>
              <div class="text-xs text-brand-ink-light">Reference Frequency</div>
              <div class="font-semibold text-brand-ink text-sm">1 kHz</div>
            </div>
            <div>
              <div class="text-xs text-brand-ink-light">Calibration Levels</div>
              <div class="font-semibold text-brand-ink text-sm">94 dB · 114 dB</div>
            </div>
            <div>
              <div class="text-xs text-brand-ink-light">Accreditation</div>
              <div><span class="nabl-badge" style="font-size:0.65rem; padding: 2px 7px;">NABL · CC-4038</span></div>
            </div>
          </div>
        </div>

      </div>
    </div>
  </section>
  ```

- [ ] Open in browser. Verify: horizontal two-column layout on desktop, stacks on mobile. Spec card is compact and readable.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: acoustics calibration section"
  ```

---

### Task 9: On-Site Calibration Section

**Files:**
- Modify: `services.html`

- [ ] Add the `#onsite` section. This closes Division 1 — it ends with a Division 1 CTA.

  ```html
  <section id="onsite" aria-label="On-Site Calibration" class="py-20 bg-brand-50">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">

      <div class="mb-12 reveal">
        <span class="nabl-badge mb-3">NABL Accredited · CC-4038 · Site Facility</span>
        <h2 class="font-serif text-3xl md:text-4xl text-brand-ink mt-3 mb-3" style="letter-spacing:-0.02em;">
          On-Site Calibration
        </h2>
        <p class="text-brand-ink-light text-lg max-w-2xl mb-2">
          Your instruments don't always need to leave your facility. Our NABL-accredited on-site team brings the lab to you.
        </p>
        <p class="text-sm text-brand-700 font-semibold">
          On-site accreditation is rare — most labs only hold permanent facility accreditation. We hold both.
        </p>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-2 gap-10 items-start">

        <!-- Left: instruments list -->
        <div class="reveal">
          <p class="text-xs font-semibold text-brand-700 uppercase tracking-widest mb-4">What we bring to your site</p>
          <ul class="space-y-2.5">
            <li class="flex items-start gap-3 text-sm text-brand-ink">
              <svg class="w-4 h-4 text-brand-500 mt-0.5 flex-shrink-0" fill="currentColor" viewBox="0 0 20 20" aria-hidden="true"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414L8.414 15l-4.121-4.121a1 1 0 011.414-1.414L8.414 12.17l7.879-7.877a1 1 0 011.414 0z" clip-rule="evenodd"/></svg>
              Air gauge units
            </li>
            <li class="flex items-start gap-3 text-sm text-brand-ink">
              <svg class="w-4 h-4 text-brand-500 mt-0.5 flex-shrink-0" fill="currentColor" viewBox="0 0 20 20" aria-hidden="true"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414L8.414 15l-4.121-4.121a1 1 0 011.414-1.414L8.414 12.17l7.879-7.877a1 1 0 011.414 0z" clip-rule="evenodd"/></svg>
              Bench centres — parallelism / coaxiality (up to 300mm)
            </li>
            <li class="flex items-start gap-3 text-sm text-brand-ink">
              <svg class="w-4 h-4 text-brand-500 mt-0.5 flex-shrink-0" fill="currentColor" viewBox="0 0 20 20" aria-hidden="true"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414L8.414 15l-4.121-4.121a1 1 0 011.414-1.414L8.414 12.17l7.879-7.877a1 1 0 011.414 0z" clip-rule="evenodd"/></svg>
              Height gauges — vernier / dial / electronic (up to 1000mm)
            </li>
            <li class="flex items-start gap-3 text-sm text-brand-ink">
              <svg class="w-4 h-4 text-brand-500 mt-0.5 flex-shrink-0" fill="currentColor" viewBox="0 0 20 20" aria-hidden="true"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414L8.414 15l-4.121-4.121a1 1 0 011.414-1.414L8.414 12.17l7.879-7.877a1 1 0 011.414 0z" clip-rule="evenodd"/></svg>
              Straight edges
            </li>
            <li class="flex items-start gap-3 text-sm text-brand-ink">
              <svg class="w-4 h-4 text-brand-500 mt-0.5 flex-shrink-0" fill="currentColor" viewBox="0 0 20 20" aria-hidden="true"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414L8.414 15l-4.121-4.121a1 1 0 011.414-1.414L8.414 12.17l7.879-7.877a1 1 0 011.414 0z" clip-rule="evenodd"/></svg>
              Surface plates — granite / cast iron (up to 2000×2000mm)
            </li>
            <li class="flex items-start gap-3 text-sm text-brand-ink">
              <svg class="w-4 h-4 text-brand-500 mt-0.5 flex-shrink-0" fill="currentColor" viewBox="0 0 20 20" aria-hidden="true"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414L8.414 15l-4.121-4.121a1 1 0 011.414-1.414L8.414 12.17l7.879-7.877a1 1 0 011.414 0z" clip-rule="evenodd"/></svg>
              Electronic height gauges (up to 600mm)
            </li>
            <li class="flex items-start gap-3 text-sm text-brand-ink">
              <svg class="w-4 h-4 text-brand-500 mt-0.5 flex-shrink-0" fill="currentColor" viewBox="0 0 20 20" aria-hidden="true"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414L8.414 15l-4.121-4.121a1 1 0 011.414-1.414L8.414 12.17l7.879-7.877a1 1 0 011.414 0z" clip-rule="evenodd"/></svg>
              Profile projectors / Video measuring machines
            </li>
            <li class="flex items-start gap-3 text-sm text-brand-ink">
              <svg class="w-4 h-4 text-brand-500 mt-0.5 flex-shrink-0" fill="currentColor" viewBox="0 0 20 20" aria-hidden="true"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414L8.414 15l-4.121-4.121a1 1 0 011.414-1.414L8.414 12.17l7.879-7.877a1 1 0 011.414 0z" clip-rule="evenodd"/></svg>
              Surface roughness testers
            </li>
            <li class="flex items-start gap-3 text-sm text-brand-ink">
              <svg class="w-4 h-4 text-brand-500 mt-0.5 flex-shrink-0" fill="currentColor" viewBox="0 0 20 20" aria-hidden="true"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414L8.414 15l-4.121-4.121a1 1 0 011.414-1.414L8.414 12.17l7.879-7.877a1 1 0 011.414 0z" clip-rule="evenodd"/></svg>
              Pressure devices — pneumatic (0–25 bar), hydraulic (0–700 bar), vacuum
            </li>
          </ul>
        </div>

        <!-- Right: key message + CTA -->
        <div class="reveal reveal-delay-2 bg-brand-700 rounded-2xl p-8 text-white">
          <div class="w-12 h-12 rounded-xl bg-brand-600 flex items-center justify-center mb-6">
            <svg class="w-7 h-7 text-brand-200" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"/><path stroke-linecap="round" stroke-linejoin="round" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"/></svg>
          </div>
          <h3 class="font-serif text-2xl text-white mb-3" style="letter-spacing:-0.02em;">We come to your facility</h3>
          <p class="text-brand-100 text-sm leading-relaxed mb-6">
            Minimise downtime and eliminate instrument transport risk. Our NABL-accredited on-site team performs calibration at your premises, with the same standards and traceability as our permanent lab.
          </p>
          <a href="contact.html" class="inline-flex items-center gap-2 bg-white text-brand-700 font-semibold text-sm px-5 py-3 rounded-lg hover:bg-brand-50 transition-colors">
            Request On-Site Calibration →
          </a>
        </div>

      </div>

      <!-- Division 1 CTA strip -->
      <div class="mt-16 pt-10 border-t border-brand-100 reveal">
        <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4">
          <div>
            <p class="font-semibold text-brand-ink">Ready to calibrate with German Calibration Lab LLP?</p>
            <p class="text-sm text-brand-ink-light mt-0.5">NABL accredited · CC-4038 · ISO/IEC 17025:2017 · Valid till 27 August 2029</p>
          </div>
          <a href="contact.html" class="btn-primary flex-shrink-0">Request a Quote</a>
        </div>
      </div>

    </div>
  </section>
  ```

- [ ] Open in browser. Verify: instruments list renders cleanly, the teal-dark CTA card is prominent, Division 1 quote CTA appears at bottom.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: on-site calibration section with instruments list + Division 1 CTA"
  ```

---

## Chunk 4: Division Separator + Division 2 (CMM & Repair)

### Task 10: Division Separator Band

**Files:**
- Modify: `services.html`

- [ ] Add the visual separator between Division 1 and Division 2. This is a full-width dark teal band that makes the transition unmistakeable.

  ```html
  <div class="division-sep py-14">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex flex-col md:flex-row md:items-center justify-between gap-6">
        <div>
          <p class="text-brand-300 text-xs font-semibold uppercase tracking-widest mb-2">Now entering</p>
          <h2 class="font-serif text-3xl md:text-4xl text-white" style="letter-spacing:-0.02em;">
            German Center of Excellence
          </h2>
          <p class="text-brand-200 text-base mt-2 max-w-lg">
            Beyond calibration. Precision inspection, expert repair, and professional training — co-equal to our calibration division.
          </p>
        </div>
        <div class="flex-shrink-0">
          <span class="coe-badge" style="background:rgba(255,255,255,0.10); border-color:rgba(255,255,255,0.25); color:#f5d98a; font-size:0.8125rem; padding: 6px 14px;">
            German Center of Excellence
          </span>
        </div>
      </div>
    </div>
  </div>
  ```

- [ ] Open in browser. Verify: dark teal band stretches full width. Text is white and readable. Makes a clear visual break between the two divisions.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: division separator band between Lab and CoE"
  ```

---

### Task 11: CMM & Third Party Inspection Section

**Files:**
- Modify: `services.html`

- [ ] Add the `#cmm` section. ZEISS CONTURA is the headline asset — give it a split layout with a photo placeholder.

  ```html
  <section id="cmm" aria-label="CMM and Third Party Inspection" class="py-20 bg-brand-white">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">

      <div class="mb-12 reveal">
        <span class="coe-badge mb-3">German Center of Excellence</span>
        <h2 class="font-serif text-3xl md:text-4xl text-brand-ink mt-3 mb-3" style="letter-spacing:-0.02em;">
          Third Party Inspection &amp; CMM Services
        </h2>
        <p class="text-brand-ink-light text-lg max-w-2xl">
          When your component tolerances leave no room for error, our ZEISS CONTURA CMM delivers measurement certainty at the micron level.
        </p>
      </div>

      <!-- ZEISS split layout -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-10 items-center mb-14 reveal">

        <!-- Left: photo placeholder -->
        <div class="rounded-2xl overflow-hidden shadow-teal-lg">
          <img
            src="https://placehold.co/700x460/47857A/FBFEFD?text=ZEISS+CONTURA+CMM"
            alt="ZEISS CONTURA coordinate measuring machine in the German Center of Excellence lab"
            class="w-full h-full object-cover"
            loading="lazy"
            width="700" height="460"
          />
        </div>

        <!-- Right: ZEISS details -->
        <div>
          <div class="inline-flex items-center gap-2 bg-brand-ink text-white text-xs font-bold uppercase tracking-widest px-3 py-1.5 rounded-full mb-5">
            <span>ZEISS CONTURA</span>
          </div>
          <h3 class="font-serif text-2xl text-brand-ink mb-2" style="letter-spacing:-0.02em;">German-Engineered Precision</h3>
          <p class="text-brand-ink-light text-sm mb-6 leading-relaxed">
            Our ZEISS CONTURA coordinate measuring machine offers a 900×1200×800mm measurement volume — suited for medium to large precision components across automotive, gear, and heavy engineering.
          </p>

          <!-- Spec table -->
          <div class="bg-brand-50 rounded-xl overflow-hidden border border-brand-100 mb-6">
            <table class="w-full text-sm">
              <tbody>
                <tr class="border-b border-brand-100">
                  <td class="px-4 py-3 text-brand-ink-light font-medium">Measurement Volume</td>
                  <td class="px-4 py-3 font-semibold text-brand-ink">900 × 1200 × 800 mm</td>
                </tr>
                <tr class="border-b border-brand-100">
                  <td class="px-4 py-3 text-brand-ink-light font-medium">Origin</td>
                  <td class="px-4 py-3 font-semibold text-brand-ink">ZEISS, Germany</td>
                </tr>
                <tr>
                  <td class="px-4 py-3 text-brand-ink-light font-medium">Division</td>
                  <td class="px-4 py-3"><span class="coe-badge" style="font-size:0.65rem; padding: 2px 7px;">CoE</span></td>
                </tr>
              </tbody>
            </table>
          </div>

          <!-- Services list -->
          <p class="text-xs font-semibold text-brand-700 uppercase tracking-widest mb-3">Services offered</p>
          <ul class="space-y-2">
            <li class="flex items-center gap-2 text-sm text-brand-ink">
              <span class="w-1.5 h-1.5 rounded-full bg-brand-500 flex-shrink-0"></span>
              Precision component inspection
            </li>
            <li class="flex items-center gap-2 text-sm text-brand-ink">
              <span class="w-1.5 h-1.5 rounded-full bg-brand-500 flex-shrink-0"></span>
              CMM measurement (coordinate measurement)
            </li>
            <li class="flex items-center gap-2 text-sm text-brand-ink">
              <span class="w-1.5 h-1.5 rounded-full bg-brand-500 flex-shrink-0"></span>
              Contour inspection
            </li>
            <li class="flex items-center gap-2 text-sm text-brand-ink">
              <span class="w-1.5 h-1.5 rounded-full bg-brand-500 flex-shrink-0"></span>
              Surface roughness measurement
            </li>
          </ul>
        </div>
      </div>

    </div>
  </section>
  ```

- [ ] Open in browser. Verify: ZEISS card shows image placeholder on the left and specs on the right. Spec table is readable. **Critically: no NABL badge anywhere in this section.**

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: CMM and third party inspection section with ZEISS CONTURA split layout"
  ```

---

### Task 12: Repair & Maintenance Section

**Files:**
- Modify: `services.html`

- [ ] Add the `#repair` section.

  ```html
  <section id="repair" aria-label="Repair and Maintenance" class="py-20 bg-brand-50">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">

      <div class="grid grid-cols-1 lg:grid-cols-2 gap-12 items-center">

        <!-- Left: content -->
        <div class="reveal">
          <span class="coe-badge mb-3">German Center of Excellence</span>
          <h2 class="font-serif text-3xl md:text-4xl text-brand-ink mt-3 mb-3" style="letter-spacing:-0.02em;">
            Repair &amp; Maintenance
          </h2>
          <p class="text-brand-ink-light text-lg mb-6">
            Extend the life of your precision instruments. Expert repair by metrology specialists, not general technicians.
          </p>

          <p class="text-xs font-semibold text-brand-700 uppercase tracking-widest mb-4">Instruments we repair</p>
          <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
            <div class="flex items-center gap-3 bg-white rounded-xl p-4 border border-brand-100 shadow-teal-sm">
              <div class="w-8 h-8 rounded-lg bg-brand-50 flex items-center justify-center flex-shrink-0">
                <svg class="w-4 h-4 text-brand-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M9 3H5a2 2 0 00-2 2v4m6-6h10a2 2 0 012 2v4M9 3v18"/></svg>
              </div>
              <span class="text-sm font-medium text-brand-ink">Micrometers</span>
            </div>
            <div class="flex items-center gap-3 bg-white rounded-xl p-4 border border-brand-100 shadow-teal-sm">
              <div class="w-8 h-8 rounded-lg bg-brand-50 flex items-center justify-center flex-shrink-0">
                <svg class="w-4 h-4 text-brand-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><circle cx="12" cy="12" r="9"/><path stroke-linecap="round" d="M12 7v5l3 3"/></svg>
              </div>
              <span class="text-sm font-medium text-brand-ink">Dial Gauges</span>
            </div>
            <div class="flex items-center gap-3 bg-white rounded-xl p-4 border border-brand-100 shadow-teal-sm">
              <div class="w-8 h-8 rounded-lg bg-brand-50 flex items-center justify-center flex-shrink-0">
                <svg class="w-4 h-4 text-brand-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6m0 0v6m0-6h6m-6 0H6"/></svg>
              </div>
              <span class="text-sm font-medium text-brand-ink">Bore Gauges</span>
            </div>
            <div class="flex items-center gap-3 bg-white rounded-xl p-4 border border-brand-100 shadow-teal-sm">
              <div class="w-8 h-8 rounded-lg bg-brand-50 flex items-center justify-center flex-shrink-0">
                <svg class="w-4 h-4 text-brand-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M4 8V4m0 0h4M4 4l5 5m11-1V4m0 0h-4m4 0l-5 5M4 16v4m0 0h4m-4 0l5-5m11 5l-5-5m5 5v-4m0 4h-4"/></svg>
              </div>
              <span class="text-sm font-medium text-brand-ink">Vernier Calipers</span>
            </div>
            <div class="flex items-center gap-3 bg-white rounded-xl p-4 border border-brand-100 shadow-teal-sm sm:col-span-2">
              <div class="w-8 h-8 rounded-lg bg-brand-50 flex items-center justify-center flex-shrink-0">
                <svg class="w-4 h-4 text-brand-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M9 3v2m6-2v2M9 19v2m6-2v2M5 9H3m2 6H3m18-6h-2m2 6h-2M7 19h10a2 2 0 002-2V7a2 2 0 00-2-2H7a2 2 0 00-2 2v10a2 2 0 002 2z"/></svg>
              </div>
              <span class="text-sm font-medium text-brand-ink">Electronic Measuring Instruments</span>
            </div>
          </div>
        </div>

        <!-- Right: image placeholder -->
        <div class="reveal reveal-delay-2 rounded-2xl overflow-hidden shadow-teal-lg">
          <img
            src="https://placehold.co/600x440/2D5D54/FBFEFD?text=Instrument+Repair"
            alt="Metrology technician repairing a precision measuring instrument"
            class="w-full h-full object-cover"
            loading="lazy"
            width="600" height="440"
          />
        </div>

      </div>
    </div>
  </section>
  ```

- [ ] Open in browser. Verify: 5 instrument repair cards render correctly. Image placeholder is on the right. No NABL badge appears.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: repair and maintenance section"
  ```

---

## Chunk 5: Division 2 — Training, Coming Soon, CTA Strip, Footer, JS

### Task 13: Training & Education Section

**Files:**
- Modify: `services.html`

- [ ] Add the `#training` section with 4 programme cards.

  ```html
  <section id="training" aria-label="Training and Education" class="py-20 bg-brand-white">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">

      <div class="mb-12 reveal">
        <span class="coe-badge mb-3">German Center of Excellence</span>
        <h2 class="font-serif text-3xl md:text-4xl text-brand-ink mt-3 mb-3" style="letter-spacing:-0.02em;">
          Training &amp; Education
        </h2>
        <p class="text-brand-ink-light text-lg max-w-2xl">
          We don't just calibrate your tools — we help your team use them better.
        </p>
      </div>

      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-5">

        <!-- Card 1: CMM Programming -->
        <div class="reveal reveal-delay-1 bg-white rounded-2xl p-6 border border-brand-100 shadow-teal-md hover:shadow-teal-lg transition-shadow">
          <div class="w-10 h-10 rounded-xl bg-brand-50 flex items-center justify-center mb-4">
            <svg class="w-5 h-5 text-brand-500" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M10 20l4-16m4 4l4 4-4 4M6 16l-4-4 4-4"/></svg>
          </div>
          <h3 class="font-semibold text-brand-ink text-base mb-2">CMM Programming Training</h3>
          <p class="text-sm text-brand-ink-light leading-relaxed">
            Hands-on training for engineers on CMM operation and programming using the ZEISS CONTURA.
          </p>
        </div>

        <!-- Card 2: Offline CMM Programming -->
        <div class="reveal reveal-delay-2 bg-white rounded-2xl p-6 border border-brand-100 shadow-teal-md hover:shadow-teal-lg transition-shadow">
          <div class="w-10 h-10 rounded-xl bg-brand-50 flex items-center justify-center mb-4">
            <svg class="w-5 h-5 text-brand-500" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M9 17V7m0 10a2 2 0 01-2 2H5a2 2 0 01-2-2V7a2 2 0 012-2h2a2 2 0 012 2m0 10a2 2 0 002 2h2a2 2 0 002-2M9 7a2 2 0 012-2h2a2 2 0 012 2m0 10V7"/></svg>
          </div>
          <h3 class="font-semibold text-brand-ink text-base mb-2">Offline CMM Programming</h3>
          <p class="text-sm text-brand-ink-light leading-relaxed">
            We program your CMM offline — saves machine time on the production floor and reduces downtime.
          </p>
        </div>

        <!-- Card 3: Metrology Training -->
        <div class="reveal reveal-delay-3 bg-white rounded-2xl p-6 border border-brand-100 shadow-teal-md hover:shadow-teal-lg transition-shadow">
          <div class="w-10 h-10 rounded-xl bg-brand-50 flex items-center justify-center mb-4">
            <svg class="w-5 h-5 text-brand-500" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"/></svg>
          </div>
          <h3 class="font-semibold text-brand-ink text-base mb-2">Metrology Training &amp; Seminars</h3>
          <p class="text-sm text-brand-ink-light leading-relaxed">
            Broader measurement awareness training for QC teams — GD&T, MSA, measurement uncertainty.
          </p>
        </div>

        <!-- Card 4: ZEISS Demo -->
        <div class="reveal reveal-delay-4 bg-white rounded-2xl p-6 border border-brand-100 shadow-teal-md hover:shadow-teal-lg transition-shadow">
          <div class="w-10 h-10 rounded-xl bg-brand-50 flex items-center justify-center mb-4">
            <svg class="w-5 h-5 text-brand-500" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M15 10l4.553-2.069A1 1 0 0121 8.82v6.36a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z"/></svg>
          </div>
          <h3 class="font-semibold text-brand-ink text-base mb-2">ZEISS CMM Product Demonstration</h3>
          <p class="text-sm text-brand-ink-light leading-relaxed">
            See the ZEISS CONTURA in action before you decide. Schedule a live demonstration at our CoE.
          </p>
        </div>

      </div>
    </div>
  </section>
  ```

- [ ] Open in browser. Verify: 4 training cards in a grid. Amber CoE badge present. No NABL badge.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: training and education section — 4 programme cards"
  ```

---

### Task 14: Coming Soon Section

**Files:**
- Modify: `services.html`

- [ ] Add the `#coming-soon` section with 2 dashed teaser cards.

  ```html
  <section id="coming-soon" aria-label="Coming soon services" class="py-16 bg-brand-50">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">

      <div class="mb-8 reveal">
        <span class="coe-badge mb-3">Coming Soon · German Center of Excellence</span>
        <h2 class="font-serif text-2xl md:text-3xl text-brand-ink mt-3 mb-2" style="letter-spacing:-0.02em;">
          Expanding Our Capabilities
        </h2>
        <p class="text-brand-ink-light text-base max-w-xl">
          German CoE continues to grow. These capabilities are in development.
        </p>
      </div>

      <div class="grid grid-cols-1 sm:grid-cols-2 gap-5 max-w-2xl">

        <!-- 3D Printing -->
        <div class="reveal reveal-delay-1 teaser-card p-6 bg-white/60">
          <div class="w-10 h-10 rounded-xl bg-brand-50 flex items-center justify-center mb-4">
            <svg class="w-5 h-5 text-brand-400" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"/></svg>
          </div>
          <div class="inline-block bg-brand-100 text-brand-700 text-xs font-bold uppercase tracking-widest px-2 py-0.5 rounded mb-3">Coming Soon</div>
          <h3 class="font-semibold text-brand-ink text-base mb-2">3D Printing</h3>
          <p class="text-sm text-brand-ink-light leading-relaxed">
            Rapid prototyping and additive manufacturing capability coming to German CoE.
          </p>
        </div>

        <!-- Handheld ZEISS Scanner -->
        <div class="reveal reveal-delay-2 teaser-card p-6 bg-white/60">
          <div class="w-10 h-10 rounded-xl bg-brand-50 flex items-center justify-center mb-4">
            <svg class="w-5 h-5 text-brand-400" fill="none" stroke="currentColor" stroke-width="1.75" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" d="M9 3H5a2 2 0 00-2 2v4m6-6h10a2 2 0 012 2v4M9 3v18m0 0h10a2 2 0 002-2V9M9 21H5a2 2 0 01-2-2V9m0 0h18"/></svg>
          </div>
          <div class="inline-block bg-brand-100 text-brand-700 text-xs font-bold uppercase tracking-widest px-2 py-0.5 rounded mb-3">Coming Soon</div>
          <h3 class="font-semibold text-brand-ink text-base mb-2">Handheld ZEISS 3D Scanning</h3>
          <p class="text-sm text-brand-ink-light leading-relaxed">
            Portable 3D scanning and inspection — bringing ZEISS precision to any location, any component.
          </p>
        </div>

      </div>
    </div>
  </section>
  ```

- [ ] Open in browser. Verify: dashed border cards render correctly. Both show "Coming Soon" badge.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: coming soon section — 3D printing and handheld ZEISS scanner teasers"
  ```

---

### Task 15: Dual CTA Strip

**Files:**
- Modify: `services.html`

- [ ] Add the final CTA strip that closes Division 2 and the whole page.

  ```html
  <!-- Dual CTA Strip -->
  <div class="bg-brand-700 py-14">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-8">

        <!-- Division 1 CTA -->
        <div class="reveal flex flex-col gap-4">
          <span class="nabl-badge w-fit">NABL Accredited · CC-4038</span>
          <h3 class="font-serif text-2xl text-white" style="letter-spacing:-0.02em;">Need calibration services?</h3>
          <p class="text-brand-200 text-sm">Dimensional, pressure, acoustics, or on-site — get a quote from our NABL-accredited lab.</p>
          <a href="contact.html" class="btn-primary w-fit" style="background:#FBFEFD; color:#2D5D54;">
            Request a Quote →
          </a>
        </div>

        <!-- Division 2 CTA -->
        <div class="reveal reveal-delay-2 flex flex-col gap-4 md:border-l md:border-brand-600 md:pl-8">
          <span class="coe-badge w-fit" style="background:rgba(255,255,255,0.10); border-color:rgba(255,255,255,0.20); color:#f5d98a;">German Center of Excellence</span>
          <h3 class="font-serif text-2xl text-white" style="letter-spacing:-0.02em;">Inspection, repair or training?</h3>
          <p class="text-brand-200 text-sm">CMM inspection, instrument repair, or CMM programming training — speak to our CoE team.</p>
          <a href="contact.html" class="btn-primary w-fit" style="background:transparent; border: 2px solid rgba(251,254,253,0.4); color:#FBFEFD;">
            Contact CoE →
          </a>
        </div>

      </div>
    </div>
  </div>
  ```

- [ ] Open in browser. Verify: dark teal background, two CTAs side by side on desktop. Left has NABL badge, right has CoE badge.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: dual CTA strip — Division 1 and Division 2 contacts"
  ```

---

### Task 16: Footer

**Files:**
- Modify: `services.html`

- [ ] Copy the entire `<footer>` block from `index.html` verbatim. Close `</main>` before the footer. No changes needed — footer is identical on all pages.

- [ ] Open in browser. Verify: footer renders with logo, links, contact info, and NABL certificate details.

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: footer — copied from index.html"
  ```

---

### Task 17: JavaScript Block

**Files:**
- Modify: `services.html`

- [ ] Add the full `<script>` block before `</body>`. Combine all JS from tasks above into one block, in this order:

  1. Mobile menu toggle (copy exactly from `index.html`)
  2. Header scroll effect (copy exactly from `index.html`)
  3. Scroll reveal observer (copy exactly from `index.html`)
  4. Hero reveal on DOMContentLoaded (copy exactly from `index.html`)
  5. Accordion JS (from Task 6)
  6. Sticky sub-nav active state (from Task 4)

- [ ] Open in browser and test all interactions:
  - [ ] Mobile hamburger opens/closes nav
  - [ ] Header gets shadow after scrolling 60px
  - [ ] Elements with `.reveal` animate in as you scroll down
  - [ ] Accordion groups open/close with arrow rotation
  - [ ] Clicking accordion while another is open closes the previous one
  - [ ] Sub-nav link highlights as you scroll to each section
  - [ ] Sub-nav gets shadow after scrolling past the hero

- [ ] Commit:
  ```bash
  git add services.html
  git commit -m "feat: complete JS block — menu, scroll, reveal, accordion, sticky subnav"
  ```

---

## Chunk 6: Final Checks

### Task 18: Responsive Check

**Files:**
- Modify: `services.html` (fixes only)

- [ ] Open browser DevTools. Check at these widths:
  - **375px** (iPhone SE) — all sections stack to 1 column, no horizontal overflow
  - **768px** (iPad) — 2-column layouts appear where expected
  - **1280px** (desktop) — 4-column highlights grid, full sub-nav visible

  Fix any layout issues found.

- [ ] Specifically verify the sticky sub-nav at mobile: it should scroll horizontally without showing a scrollbar, and all 8 links must be reachable.

- [ ] Commit any fixes:
  ```bash
  git add services.html
  git commit -m "fix: responsive layout adjustments for services page"
  ```

---

### Task 19: Contrast & Accessibility Audit

**Files:**
- Modify: `services.html` (fixes only)

- [ ] Check every text element against CLAUDE.md Section 5 rules:
  - All small text (labels, captions, badges) must use `text-brand-700` not `text-brand-500`
  - CoE badge text must be `#7a4f0d` on amber background — verify contrast passes 4.5:1
  - Footer links must be `text-brand-ink-light hover:text-brand-700`
  - White text on `bg-brand-700` (#2D5D54): contrast ratio is 6.62:1 — passes ✅

- [ ] Check all images have meaningful `alt` text describing what the real photo should show.

- [ ] Verify all `<section>` tags have `aria-label` attributes.

- [ ] Commit any fixes:
  ```bash
  git add services.html
  git commit -m "fix: accessibility and contrast fixes on services page"
  ```

---

### Task 20: Final Verification

**Files:**
- `services.html` (read-only check)

- [ ] Open `services.html` in browser. Do a complete scroll from top to bottom. Verify:
  - [ ] Hero compact — not full screen
  - [ ] Sub-nav sticks at correct position (64px from top)
  - [ ] 8 section anchors all work from sub-nav links
  - [ ] NABL badge on all 4 Division 1 service headings
  - [ ] CoE badge on all 4 Division 2 service headings
  - [ ] No NABL badge appears anywhere in Division 2
  - [ ] Division separator band is prominent
  - [ ] ZEISS CONTURA image placeholder visible
  - [ ] Accordion in dimensional section works
  - [ ] Scope PDF download link present
  - [ ] "Verify on NABL →" link present
  - [ ] All CTAs link to `contact.html`
  - [ ] Footer present and complete

- [ ] Final commit:
  ```bash
  git add services.html
  git commit -m "feat: services.html complete — both divisions, all 8 sections, sticky subnav, accordion"
  ```
