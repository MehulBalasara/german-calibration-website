# Sub-Nav Redesign Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the grouped pill-container sub-nav in `services.html` with a single flat row using pill hover, teal/amber colour coding, backdrop blur, and mobile auto-center JS.

**Architecture:** All changes are confined to one file — `services.html`. Three areas change: the CSS block (replace old subnav rules), the HTML nav element (flatten two pill-groups into one row), and the JavaScript (update IntersectionObserver + add auto-scroll helper).

**Tech Stack:** Vanilla HTML/CSS/JS, Tailwind CDN (used for layout outside the nav; nav itself uses custom CSS in `<style>` block), Python HTTP server for local preview.

**Spec:** `docs/superpowers/specs/2026-03-18-subnav-redesign-design.md`

---

## File Map

| File | Change |
|---|---|
| `services.html` | CSS: replace subnav rules. HTML: flatten nav. JS: update observer + add auto-center. |

No new files created.

---

## Task 1 — Update the CSS

**Files:** Modify `services.html` (the `<style>` block, lines ~229–290)

The old CSS has `.subnav-group-lab`, `.subnav-group-coe`, and `.subnav-link` rules. Replace all of them with the new flat-row rules.

- [ ] **Step 1: Locate the subnav CSS block**

  In `services.html`, find the comment `/* ── Sticky sub-nav ── */` (around line 229). The block ends around line 290 with `.hide-scrollbar`.

- [ ] **Step 2: Replace the subnav CSS rules**

  Remove all rules from `/* ── Sticky sub-nav ── */` down to (but NOT including) `.hide-scrollbar`. Replace with:

  ```css
  /* ── Sticky sub-nav ── */
  #services-subnav {
    position: sticky;
    top: 64px;
    z-index: 40;
    background: rgba(251,254,253,0.92);
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
    border-bottom: 1px solid rgba(71,133,122,0.12);
    transition: box-shadow 0.2s;
  }
  #services-subnav.elevated {
    box-shadow: 0 2px 12px rgba(71,133,122,0.10);
  }

  /* ── Sub-nav inner scroll container ── */
  .subnav-inner {
    display: flex;
    align-items: center;
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
    scrollbar-width: none;
    scroll-snap-type: x proximity;
    height: 48px;
    padding: 0 16px;
    gap: 2px;
  }
  .subnav-inner::-webkit-scrollbar { display: none; }

  /* ── Individual nav links ── */
  .subnav-link {
    flex-shrink: 0;
    scroll-snap-align: start;
    white-space: nowrap;
    font-size: 13.5px;
    font-weight: 500;
    color: #4A6663;
    text-decoration: none;
    padding: 6px 13px;
    border-radius: 999px;
    transition: background 0.15s ease, color 0.15s ease;
    display: inline-flex;
    align-items: center;
  }

  /* GCL hover + active (teal) */
  .subnav-link:hover {
    background: rgba(71,133,122,0.08);
    color: #2D5D54;
  }
  .subnav-link.active {
    background: rgba(71,133,122,0.14);
    color: #2D5D54;
    font-weight: 600;
  }

  /* CoE hover + active (amber) */
  .subnav-link[data-group="coe"]:hover {
    background: rgba(180,119,40,0.08);
    color: #92400e;
  }
  .subnav-link[data-group="coe"].active {
    background: rgba(180,119,40,0.14);
    color: #92400e;
    font-weight: 600;
  }

  /* ── Group separator ── */
  .subnav-sep {
    width: 1px;
    height: 14px;
    background: rgba(71,133,122,0.22);
    margin: 0 8px;
    flex-shrink: 0;
    align-self: center;
  }
  ```

- [ ] **Step 3: Confirm `.hide-scrollbar` is retained**

  The `.hide-scrollbar` utility class (hides scrollbar on the old nav container) is still referenced by the new `.subnav-inner` — do NOT delete it. Confirm it still exists in the CSS after your edits. It should read:
  ```css
  .hide-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
  ```

- [ ] **Step 4: Verify `section[id]` scroll-margin-top is still 120px**

  Find `section[id]` rule (around line 291). It should read:
  ```css
  section[id] { scroll-margin-top: 120px; }
  ```
  If it says anything other than `120px`, update it. (120 = 64px header + 48px subnav + 8px.)

- [ ] **Step 4: Visual check in browser**

  Open `http://localhost:8765/services.html`. The sub-nav area will be broken (no HTML changed yet) — that is expected. Confirm no CSS parse errors in the browser console.

---

## Task 2 — Update the HTML

**Files:** Modify `services.html` (the `<nav id="services-subnav">` block, around lines 446–476)

- [ ] **Step 1: Locate the nav HTML**

  Find `<nav id="services-subnav"` in `services.html`. The current structure has an outer `<div class="max-w-7xl...">`, then an inner `<div class="flex flex-col md:flex-row...">`, then two `.subnav-group-lab` and `.subnav-group-coe` divs.

- [ ] **Step 2: Replace the entire nav inner content**

  Replace everything inside `<nav id="services-subnav" aria-label="Services sections">` with:

  ```html
  <nav id="services-subnav" aria-label="Services sections">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="subnav-inner">

        <!-- GCL links -->
        <a href="#dimensional" class="subnav-link" data-section="dimensional">Dimensional</a>
        <a href="#pressure"    class="subnav-link" data-section="pressure">Pressure</a>
        <a href="#acoustics"   class="subnav-link" data-section="acoustics">Acoustics</a>
        <a href="#onsite"      class="subnav-link" data-section="onsite">On-Site</a>

        <!-- Group separator -->
        <span class="subnav-sep" aria-hidden="true"></span>

        <!-- CoE links -->
        <a href="#cmm"          class="subnav-link" data-section="cmm"          data-group="coe">CMM &amp; Inspection</a>
        <a href="#repair"       class="subnav-link" data-section="repair"       data-group="coe">Repair</a>
        <a href="#training"     class="subnav-link" data-section="training"     data-group="coe">Training</a>
        <a href="#coming-soon"  class="subnav-link" data-section="coming-soon"  data-group="coe">Coming Soon</a>

      </div>
    </div>
  </nav>
  ```

- [ ] **Step 3: Visual check — desktop**

  Open `http://localhost:8765/services.html`. The sub-nav should show as a single flat row: `Dimensional · Pressure · Acoustics · On-Site | CMM & Inspection · Repair · Training · Coming Soon`. Hover GCL links — teal pill. No active state yet (JS not updated).

- [ ] **Step 4: Visual check — mobile**

  Resize browser to 390px wide (or use DevTools). Sub-nav should be a single scrollable row, no two rows, no scrollbar visible.

---

## Task 3 — Update the JavaScript

**Files:** Modify `services.html` (the JS block at bottom, around lines 1333–1356)

- [ ] **Step 1: Locate the subnav JS block**

  Find the comment `// ── Sticky sub-nav: active section tracking ──` near the bottom of the `<script>` tag.

- [ ] **Step 2: Replace the subnav JS block**

  Replace from the comment `// ── Sticky sub-nav: active section tracking ──` all the way through the closing `}, { passive: true });` of the scroll elevation listener (the last line of subnav JS). The replacement block below includes the elevation listener — do not keep the old one. With:

  ```js
  // ── Sticky sub-nav: active section tracking ──
  const subnav     = document.getElementById('services-subnav');
  const subnavInner = subnav ? subnav.querySelector('.subnav-inner') : null;
  const subnavLinks = document.querySelectorAll('.subnav-link[data-section]');
  const sections    = document.querySelectorAll('section[id]');

  function scrollActiveTabIntoView(activeTab) {
    if (!subnavInner) return;
    const tabLeft  = activeTab.offsetLeft;
    const tabWidth = activeTab.offsetWidth;
    const navWidth = subnavInner.offsetWidth;
    subnavInner.scrollTo({
      left: tabLeft - (navWidth / 2) + (tabWidth / 2),
      behavior: 'smooth'
    });
  }

  const sectionObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        const id = entry.target.getAttribute('id');
        subnavLinks.forEach(link => link.classList.remove('active'));
        const activeLink = document.querySelector(`.subnav-link[data-section="${id}"]`);
        if (activeLink) {
          activeLink.classList.add('active');
          scrollActiveTabIntoView(activeLink);
        }
      }
    });
  }, {
    rootMargin: '-112px 0px -50% 0px',
    threshold: 0
  });

  sections.forEach(s => sectionObserver.observe(s));

  // Sub-nav elevation on scroll
  window.addEventListener('scroll', () => {
    if (subnav) subnav.classList.toggle('elevated', window.scrollY > 200);
  }, { passive: true });
  ```

- [ ] **Step 3: Verify active state on desktop**

  Reload `http://localhost:8765/services.html`. Scroll slowly through the page. Each section heading should trigger its corresponding link to show the pill active state. GCL links = teal pill. CoE links = amber pill.

- [ ] **Step 4: Verify auto-center on mobile**

  Resize to 390px. Scroll to the CoE sections. The "CMM & Inspection", "Repair", "Training" links should auto-scroll into the center of the nav bar as you enter each section.

- [ ] **Step 5: Verify scroll-to-section works**

  Click "Pressure" in the sub-nav. Page should scroll so the Pressure section heading is visible below the sub-nav (not hidden behind it). If it scrolls behind the bar, re-check `scroll-margin-top: 120px` on `section[id]`.

---

## Task 4 — Final QA & Commit

- [ ] **Step 1: Full desktop pass**

  At 1280px wide: scroll the entire page top to bottom. Confirm:
  - Active state updates for all 8 sections
  - GCL = teal pill, CoE = amber pill
  - Backdrop blur visible when scrolling over content
  - Hairline border visible (no shadow)
  - No layout breaks

- [ ] **Step 2: Full mobile pass**

  At 390px wide: scroll the entire page top to bottom. Confirm:
  - Single scroll row (no wrapping, no two rows)
  - Active tab auto-centers
  - No horizontal overflow on the page body itself

- [ ] **Step 3: Commit**

  ```bash
  git add services.html
  git commit -m "feat: redesign sub-nav — flat row, pill hover, teal/amber colour coding"
  ```

- [ ] **Step 4: Push** *(only after Steps 1–2 both pass)*

  ```bash
  git push
  ```
