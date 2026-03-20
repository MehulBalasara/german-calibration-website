# Contact Page Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `contact.html` — a full contact hub with a quote request form and contact details panel, as the dedicated destination for the site's "Get a Quote" CTA.

**Architecture:** Single static HTML file. No build tools. All CSS inline via Tailwind CDN + `<style>` block. Header and footer copied verbatim from `services.html`. Two-column main section (form left, contact panel right), compact page header, Google Maps embed at bottom.

**Tech Stack:** HTML5, Tailwind CDN, DM Serif Display + Inter (Google Fonts), vanilla JS

**Spec:** `docs/superpowers/specs/2026-03-20-contact-page-design.md`
**Reference files:** `index.html` (form pattern lines 1307–1674), `services.html` (header/footer/CSS patterns)

---

## File Map

| File | Action | Responsibility |
|---|---|---|
| `contact.html` | Create | Entire contact page |

No other files are modified. All assets (logo, fonts, Tailwind CDN) are already available.

---

### Task 1: HTML skeleton — `<head>`, header, footer, `<main>` wrapper

**Files:**
- Create: `contact.html`

- [ ] **Step 1: Create the file with `<head>` block**

Create `contact.html` with the following `<head>` (meta tags, fonts, Tailwind CDN, full Tailwind config from CLAUDE.md Section 4):

```html
<!DOCTYPE html>
<html lang="en" class="scroll-smooth">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Contact — German Calibration | NABL Accredited Lab, Rajkot</title>
  <meta name="description" content="Get in touch with German Calibration Lab LLP — NABL accredited calibration lab in Rajkot. Request a quote, call us, or visit at Plot No. 10B, Udhyog Nagar 2." />

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

  <style>
    /* placeholder — CSS added in Task 2 */
  </style>
</head>
<body class="font-sans bg-brand-white text-brand-ink antialiased">

  <!-- HEADER — copy verbatim from services.html (the full <header id="site-header"> block) -->

  <main>
    <!-- sections added in Tasks 3–6 -->
  </main>

  <!-- FOOTER — copy verbatim from services.html -->

  <script>
    /* JS added in Task 7 */
  </script>
</body>
</html>
```

- [ ] **Step 2: Copy header from `services.html`**

Open `services.html`. Copy the entire `<header id="site-header">` block (search for `<header id="site-header"` — everything from that opening tag to its closing `</header>`). Paste it into `contact.html` replacing the `<!-- HEADER -->` comment.

Two adjustments to the copied header:
1. No nav link is active on the contact page — leave all `.nav-link` elements without an active state
2. The "Get a Quote" CTA button links to `contact.html` in the copied header. On the contact page itself, change this to an in-page anchor: `href="#quote-form"` so it scrolls to the form instead of reloading the same page

- [ ] **Step 3: Copy footer from `services.html`**

Open `services.html`. Copy the entire `<footer>` block. Paste it into `contact.html` replacing the `<!-- FOOTER -->` comment.

- [ ] **Step 4: Verify skeleton in browser**

Open `contact.html` directly in a browser. Verify:
- Header renders with logo, nav links, phone, Client Portal button, "Get a Quote" button
- Footer renders with address and links
- No console errors
- Page background is brand-white (off-white, not pure white)

- [ ] **Step 5: Commit skeleton**

```bash
git add contact.html
git commit -m "feat: add contact.html skeleton — head, header, footer"
```

---

### Task 2: CSS `<style>` block

**Files:**
- Modify: `contact.html` — replace `<style>` placeholder

- [ ] **Step 1: Add full CSS block**

Replace the `/* placeholder */` comment inside `<style>` with the following (copy exact CSS from `index.html` `<style>` block — sections listed below):

```css
/* ── Base resets ── */
*, *::before, *::after { box-sizing: border-box; }
html { font-family: 'Inter', system-ui, sans-serif; }
body { background-color: #FBFEFD; color: #0F1E1C; -webkit-font-smoothing: antialiased; }

/* ── btn-primary ── */
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

/* ── btn-outline ── */
.btn-outline {
  display: inline-flex; align-items: center; gap: 8px;
  background: transparent; color: #2D5D54;
  font-weight: 600; font-size: 0.9375rem;
  padding: 13px 27px; border-radius: 6px;
  border: 1.5px solid #2D5D54;
  letter-spacing: -0.01em;
  transition: background 0.2s, color 0.2s, transform 0.15s;
  text-decoration: none;
}
.btn-outline:hover { background: #2D5D54; color: #FBFEFD; transform: translateY(-1px); }
.btn-outline:active { transform: translateY(0); }

/* ── Reveal animation ── */
.reveal { opacity: 0; transform: translateY(28px); transition: opacity 0.65s cubic-bezier(0.22, 1, 0.36, 1), transform 0.65s cubic-bezier(0.22, 1, 0.36, 1); }
.reveal.visible { opacity: 1; transform: translateY(0); }
.reveal-delay-1 { transition-delay: 0.1s; }
.reveal-delay-2 { transition-delay: 0.2s; }
.reveal-delay-3 { transition-delay: 0.3s; }
.reveal-delay-4 { transition-delay: 0.4s; }

/* ── Header scroll state ── */
#site-header { transition: background-color 0.3s ease, box-shadow 0.3s ease; }
#site-header.scrolled { background-color: rgba(251,254,253,0.97); box-shadow: 0 1px 16px rgba(71,133,122,0.10); backdrop-filter: blur(8px); }

/* ── Mobile menu ── */
#mobile-menu { display: none; }
#mobile-menu.open { display: block; }

/* ── nav-link hover underline ── */
.nav-link { position: relative; }
.nav-link::after { content: ''; position: absolute; bottom: -2px; left: 0; width: 0; height: 1.5px; background: #2D5D54; transition: width 0.2s ease; }
.nav-link:hover::after { width: 100%; }

/* ── Accessibility: respect reduced-motion preference ── */
@media (prefers-reduced-motion: reduce) {
  .reveal { opacity: 1; transform: none; transition: none; }
}
```

- [ ] **Step 2: Verify in browser**

Open `contact.html`. Verify:
- "Get a Quote" button is dark teal (not blue — that would mean btn-primary CSS is missing)
- Scroll down slightly — header should gain a subtle shadow (`.scrolled` state)

- [ ] **Step 3: Commit**

```bash
git add contact.html
git commit -m "feat: add contact page CSS — btn-primary, reveal, header scroll"
```

---

### Task 3: Section 1 — Compact page header

**Files:**
- Modify: `contact.html` — add inside `<main>`

- [ ] **Step 1: Add the page header section**

Inside `<main>`, add:

```html
<!-- ═══════════════════════════════════════════════
     SECTION 1 — PAGE HEADER
════════════════════════════════════════════════ -->
<section class="bg-brand-50 py-14 lg:py-20" aria-label="Page header">
  <div class="max-w-7xl mx-auto px-5 lg:px-8">

    <!-- Breadcrumb -->
    <nav aria-label="Breadcrumb" class="mb-6">
      <ol class="flex items-center gap-2 text-sm">
        <li><a href="index.html" class="text-brand-ink-light hover:text-brand-700 transition-colors">Home</a></li>
        <li class="text-brand-ink-light/40" aria-hidden="true">/</li>
        <li class="text-brand-ink font-medium" aria-current="page">Contact</li>
      </ol>
    </nav>

    <!-- Heading -->
    <h1 class="reveal font-serif text-brand-ink leading-tight mb-4" style="font-size: clamp(2rem, 4vw, 2.75rem); letter-spacing: -0.03em;">
      Get in Touch
    </h1>

    <!-- Subheading -->
    <p class="reveal reveal-delay-1 text-brand-ink-light text-lg leading-relaxed max-w-2xl">
      Request a calibration quote, ask a question, or plan a visit to our lab in Rajkot.
    </p>

  </div>
</section>
```

- [ ] **Step 2: Verify in browser**

Open `contact.html`. Verify:
- Page header has a soft teal background (`#E6F2F0`) — distinct from the white body
- Breadcrumb shows "Home / Contact" with Home as a clickable link
- Heading is in DM Serif Display (serif font, not sans-serif)
- No CTA buttons below the subheading
- **Note:** The heading and subheading will appear invisible (opacity: 0) at this stage because the `.reveal` JS is not added until Task 7 — this is expected behaviour, not a bug

- [ ] **Step 3: Commit**

```bash
git add contact.html
git commit -m "feat: add contact page header with breadcrumb"
```

---

### Task 4: Section 2 left — Quote request form

**Files:**
- Modify: `contact.html` — add section with two-column grid, left column only

- [ ] **Step 1: Add the two-column section wrapper and form**

After Section 1, add:

```html
<!-- ═══════════════════════════════════════════════
     SECTION 2 — FORM + CONTACT PANEL
════════════════════════════════════════════════ -->
<section class="bg-brand-white py-16 lg:py-24" aria-label="Contact and quote request">
  <div class="max-w-7xl mx-auto px-5 lg:px-8">
    <div class="grid lg:grid-cols-5 gap-10 lg:gap-16">

      <!-- LEFT: Quote form (3/5) -->
      <div class="lg:col-span-3">

        <p class="text-brand-700 text-xs font-semibold uppercase tracking-widest mb-3">Request a Quote</p>

        <form novalidate id="quote-form" name="quote-request" class="reveal space-y-5">

          <!-- Row 1: Company + Contact Person -->
          <div class="grid sm:grid-cols-2 gap-5">
            <div>
              <label for="company" class="block text-brand-ink text-sm font-semibold mb-2">Company Name <span class="text-brand-700">*</span></label>
              <input type="text" id="company" name="company" required aria-required="true"
                placeholder="e.g. Acme Industries Ltd."
                class="w-full px-4 py-3 bg-white border border-brand-200 rounded-xl text-brand-ink placeholder-brand-ink-light/50 text-sm focus:outline-none focus:border-brand-500 focus:ring-2 focus:ring-brand-500/15 transition-all" />
            </div>
            <div>
              <label for="contact" class="block text-brand-ink text-sm font-semibold mb-2">Contact Person <span class="text-brand-700">*</span></label>
              <input type="text" id="contact" name="contact" required aria-required="true"
                placeholder="Your name"
                class="w-full px-4 py-3 bg-white border border-brand-200 rounded-xl text-brand-ink placeholder-brand-ink-light/50 text-sm focus:outline-none focus:border-brand-500 focus:ring-2 focus:ring-brand-500/15 transition-all" />
            </div>
          </div>

          <!-- Row 2: Email + Phone -->
          <div class="grid sm:grid-cols-2 gap-5">
            <div>
              <label for="email" class="block text-brand-ink text-sm font-semibold mb-2">Email Address <span class="text-brand-700">*</span></label>
              <input type="email" id="email" name="email" required aria-required="true"
                placeholder="you@company.com"
                class="w-full px-4 py-3 bg-white border border-brand-200 rounded-xl text-brand-ink placeholder-brand-ink-light/50 text-sm focus:outline-none focus:border-brand-500 focus:ring-2 focus:ring-brand-500/15 transition-all" />
            </div>
            <div>
              <label for="phone" class="block text-brand-ink text-sm font-semibold mb-2">Phone Number <span class="text-brand-700">*</span></label>
              <input type="tel" id="phone" name="phone" required aria-required="true"
                placeholder="+91 98765 43210"
                class="w-full px-4 py-3 bg-white border border-brand-200 rounded-xl text-brand-ink placeholder-brand-ink-light/50 text-sm focus:outline-none focus:border-brand-500 focus:ring-2 focus:ring-brand-500/15 transition-all" />
            </div>
          </div>

          <!-- Row 3: Instrument Type + Quantity (optional) -->
          <div class="grid sm:grid-cols-2 gap-5">
            <div>
              <label for="instrument" class="block text-brand-ink text-sm font-semibold mb-2">Instrument Type(s) <span class="text-brand-ink-light font-normal">(optional)</span></label>
              <input type="text" id="instrument" name="instrument"
                placeholder="e.g. Micrometers, Calipers"
                class="w-full px-4 py-3 bg-white border border-brand-200 rounded-xl text-brand-ink placeholder-brand-ink-light/50 text-sm focus:outline-none focus:border-brand-500 focus:ring-2 focus:ring-brand-500/15 transition-all" />
            </div>
            <div>
              <label for="quantity" class="block text-brand-ink text-sm font-semibold mb-2">Approx. Quantity <span class="text-brand-ink-light font-normal">(optional)</span></label>
              <input type="text" id="quantity" name="quantity"
                placeholder="e.g. 25 instruments"
                class="w-full px-4 py-3 bg-white border border-brand-200 rounded-xl text-brand-ink placeholder-brand-ink-light/50 text-sm focus:outline-none focus:border-brand-500 focus:ring-2 focus:ring-brand-500/15 transition-all" />
            </div>
          </div>

          <!-- Row 4: Preferred Service (optional select) -->
          <div>
            <label for="service" class="block text-brand-ink text-sm font-semibold mb-2">Preferred Service <span class="text-brand-ink-light font-normal">(optional)</span></label>
            <select id="service" name="service"
              class="w-full px-4 py-3 bg-white border border-brand-200 rounded-xl text-brand-ink text-sm focus:outline-none focus:border-brand-500 focus:ring-2 focus:ring-brand-500/15 transition-all appearance-none"
              style="background-image: url(&quot;data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 24 24' stroke='%234A6663'%3E%3Cpath stroke-linecap='round' stroke-linejoin='round' stroke-width='2' d='M19 9l-7 7-7-7'/%3E%3C/svg%3E&quot;); background-repeat: no-repeat; background-position: right 14px center; background-size: 16px;">
              <option value="">Select a service...</option>
              <option value="lab">Lab Calibration</option>
              <option value="onsite">On-Site Calibration</option>
              <option value="cmm">Third Party Inspection / CMM</option>
              <option value="repair">Repair &amp; Maintenance</option>
              <option value="training">CMM Training</option>
              <option value="other">Other / Not sure</option>
            </select>
          </div>

          <!-- Row 5: Additional Notes (optional textarea) -->
          <div>
            <label for="message" class="block text-brand-ink text-sm font-semibold mb-2">Additional Notes <span class="text-brand-ink-light font-normal">(optional)</span></label>
            <textarea id="message" name="message" rows="4"
              placeholder="Any specific requirements, urgency, or questions..."
              class="w-full px-4 py-3 bg-white border border-brand-200 rounded-xl text-brand-ink placeholder-brand-ink-light/50 text-sm focus:outline-none focus:border-brand-500 focus:ring-2 focus:ring-brand-500/15 transition-all resize-none"></textarea>
          </div>

          <!-- Submit -->
          <button type="submit" class="w-full btn-primary justify-center py-3.5 text-base rounded-xl">
            Send Quote Request
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"/>
            </svg>
          </button>

          <!-- Success message (hidden by default) -->
          <div id="form-success" class="hidden bg-brand-50 border border-brand-200 rounded-xl px-5 py-4 text-brand-700 text-sm font-medium text-center">
            Thank you! We've received your request and will be in touch within one business day.
          </div>

        </form>
      </div>

      <!-- RIGHT: Contact panel placeholder — added in Task 5 -->
      <div class="lg:col-span-2">
        <!-- contact panel -->
      </div>

    </div>
  </div>
</section>
```

- [ ] **Step 2: Verify form in browser**

Open `contact.html`. Verify:
- Form renders with 8 fields in the correct row layout (2-col pairs for rows 1–3)
- Required asterisks are dark teal, not red
- Service dropdown has a custom chevron (not browser default arrow)
- Optional fields show "(optional)" in lighter weight next to the label
- Submit button is full-width, dark teal background
- Inspect HTML: the 4 required inputs (`company`, `contact`, `email`, `phone`) each have `aria-required="true"`

- [ ] **Step 3: Commit**

```bash
git add contact.html
git commit -m "feat: add quote request form — 8 fields matching homepage pattern"
```

---

### Task 5: Section 2 right — Contact panel

**Files:**
- Modify: `contact.html` — replace the contact panel placeholder

- [ ] **Step 1: Replace the right column placeholder with the contact panel**

Replace `<!-- contact panel -->` (inside `<div class="lg:col-span-2">`) with:

```html
<div class="reveal reveal-delay-1">
  <div class="bg-brand-50 rounded-2xl p-6 border border-brand-100">

    <!-- h3 matches homepage pattern (index.html line 1410); h2 is reserved for section-level headings -->
    <h3 class="font-semibold text-brand-ink text-sm uppercase tracking-widest mb-6">Contact Information</h3>

    <!-- Block 1: Address -->
    <div class="flex items-start gap-3 mb-5">
      <div class="w-8 h-8 rounded-lg bg-brand-500/15 flex items-center justify-center flex-shrink-0 mt-0.5">
        <svg class="w-4 h-4 text-brand-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"/>
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"/>
        </svg>
      </div>
      <div>
        <div class="text-brand-ink font-semibold text-sm mb-0.5">Address</div>
        <div class="text-brand-ink-light text-sm leading-relaxed">Plot No. 10B, Umakant Pandit,<br/>Udhyog Nagar 2, Rajkot,<br/>Gujarat, India</div>
        <a href="https://maps.app.goo.gl/doezCLm5DbpFEQcc7?g_st=ac" target="_blank" rel="noopener"
          class="inline-flex items-center gap-1 text-brand-700 text-xs font-semibold mt-2 hover:text-brand-800 transition-colors"
          aria-label="Open lab address in Google Maps">
          Open in Maps
          <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14"/></svg>
        </a>
      </div>
    </div>

    <!-- Block 2: Phone -->
    <div class="flex items-start gap-3 mb-5">
      <div class="w-8 h-8 rounded-lg bg-brand-500/15 flex items-center justify-center flex-shrink-0 mt-0.5">
        <svg class="w-4 h-4 text-brand-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 8V5z"/>
        </svg>
      </div>
      <div>
        <div class="text-brand-ink font-semibold text-sm mb-0.5">Phone</div>
        <a href="tel:+919276551009" class="text-brand-700 text-sm font-medium hover:text-brand-800 transition-colors">+91 92765 51009</a>
      </div>
    </div>

    <!-- Block 3: WhatsApp -->
    <div class="flex items-start gap-3 mb-5">
      <div class="w-8 h-8 rounded-lg bg-brand-500/15 flex items-center justify-center flex-shrink-0 mt-0.5">
        <svg class="w-4 h-4 text-brand-500" fill="currentColor" viewBox="0 0 24 24" aria-hidden="true">
          <path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893a11.821 11.821 0 00-3.48-8.413z"/>
        </svg>
      </div>
      <div>
        <div class="text-brand-ink font-semibold text-sm mb-0.5">WhatsApp</div>
        <a href="https://wa.me/919276551009" target="_blank" rel="noopener"
          class="text-brand-700 text-sm font-medium hover:text-brand-800 transition-colors"
          aria-label="Chat on WhatsApp">+91 92765 51009</a>
      </div>
    </div>

    <!-- Block 4: Email -->
    <div class="flex items-start gap-3 mb-5">
      <div class="w-8 h-8 rounded-lg bg-brand-500/15 flex items-center justify-center flex-shrink-0 mt-0.5">
        <svg class="w-4 h-4 text-brand-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"/>
        </svg>
      </div>
      <div>
        <div class="text-brand-ink font-semibold text-sm mb-0.5">Email</div>
        <a href="mailto:lab@germancalibration.com" class="text-brand-700 text-sm font-medium hover:text-brand-800 transition-colors">lab@germancalibration.com</a>
      </div>
    </div>

    <!-- Block 5: Working Hours -->
    <div class="flex items-start gap-3 pt-5 border-t border-brand-100">
      <div class="w-8 h-8 rounded-lg bg-brand-500/15 flex items-center justify-center flex-shrink-0 mt-0.5">
        <svg class="w-4 h-4 text-brand-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"/>
        </svg>
      </div>
      <div>
        <div class="text-brand-ink font-semibold text-sm mb-0.5">Working Hours</div>
        <div class="text-brand-ink-light text-sm">Monday – Saturday</div>
        <div class="text-brand-ink-light text-sm">9:00 AM – 6:00 PM</div>
        <p class="text-brand-ink-light text-xs mt-1">Instruments can be dropped off directly at the lab.</p>
      </div>
    </div>

  </div>
</div>
```

- [ ] **Step 2: Verify contact panel in browser**

Open `contact.html`. Verify:
- Contact panel sits to the right of the form on desktop (≥1024px)
- On mobile, contact panel appears below the form
- All 5 blocks are visible: Address (with "Open in Maps" link), Phone, WhatsApp, Email, Working Hours
- Working Hours has a top border separator
- All icon containers are the same teal-tinted style
- No green WhatsApp icon — it should be teal like the rest

- [ ] **Step 3: Commit**

```bash
git add contact.html
git commit -m "feat: add contact panel — address, phone, whatsapp, email, hours"
```

---

### Task 6: Section 3 — Google Maps embed

**Files:**
- Modify: `contact.html` — add after Section 2

- [ ] **Step 1: Add the map section**

After the closing `</section>` of Section 2, add:

```html
<!-- ═══════════════════════════════════════════════
     SECTION 3 — GOOGLE MAPS EMBED
════════════════════════════════════════════════ -->
<div class="border-t border-brand-100">
  <iframe
    class="w-full h-64 lg:h-96"
    style="border:0;"
    loading="lazy"
    referrerpolicy="no-referrer-when-downgrade"
    title="German Calibration Lab location — Plot No. 10B, Udhyog Nagar 2, Rajkot"
    aria-label="Map showing German Calibration Lab location in Rajkot"
    src="">
    <!-- TODO: replace src="" with the Google Maps embed URL once provided by owner -->
  </iframe>
</div>
```

**Getting the embed URL (owner action required):**
1. Go to Google Maps
2. Search: `Plot 10B Udhyog Nagar 2 Rajkot Gujarat India`
3. Click Share → Embed a map
4. Copy the `src="..."` URL from the iframe code shown
5. Replace the `<!-- TODO -->` comment with the copied URL

Until the owner provides the URL, the map section will be empty. This is acceptable — the rest of the page is fully functional.

- [ ] **Step 2: Verify in browser**

Once the embed URL is added: open `contact.html`, scroll to the bottom. Verify:
- Map renders at full width
- Height is shorter on mobile (256px), taller on desktop (384px)
- Thin teal border separates it from Section 2

- [ ] **Step 3: Commit**

```bash
git add contact.html
git commit -m "feat: add maps embed section — awaiting embed URL from owner"
```

---

### Task 7: JavaScript — mobile menu, header scroll, reveal, form validation

**Files:**
- Modify: `contact.html` — replace `/* JS added in Task 7 */` comment in the `<script>` tag

- [ ] **Step 1: Add the complete JS block**

```javascript
// ── Mobile menu toggle ──
const menuToggle = document.getElementById('menu-toggle');
const mobileMenu = document.getElementById('mobile-menu');
const iconMenu   = document.getElementById('icon-menu');
const iconClose  = document.getElementById('icon-close');

if (menuToggle && mobileMenu) {
  menuToggle.addEventListener('click', () => {
    const isOpen = mobileMenu.classList.toggle('open');
    menuToggle.setAttribute('aria-expanded', isOpen);
    iconMenu.classList.toggle('hidden', isOpen);
    iconClose.classList.toggle('hidden', !isOpen);
  });

  document.addEventListener('click', (e) => {
    if (!menuToggle.contains(e.target) && !mobileMenu.contains(e.target)) {
      mobileMenu.classList.remove('open');
      menuToggle.setAttribute('aria-expanded', 'false');
      iconMenu.classList.remove('hidden');
      iconClose.classList.add('hidden');
    }
  });
}

// ── Header scroll effect ──
const header = document.getElementById('site-header');
window.addEventListener('scroll', () => {
  header.classList.toggle('scrolled', window.scrollY > 60);
}, { passive: true });

// ── Scroll reveal ──
const revealObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible');
      revealObserver.unobserve(entry.target);
    }
  });
}, { threshold: 0.12, rootMargin: '0px 0px -40px 0px' });
document.querySelectorAll('.reveal').forEach(el => revealObserver.observe(el));

// ── Form validation ──
const form       = document.getElementById('quote-form');
const successMsg = document.getElementById('form-success');

if (form) {
  form.addEventListener('submit', (e) => {
    e.preventDefault();

    // Clear previous error states
    form.querySelectorAll('.border-red-400').forEach(el => {
      el.classList.remove('border-red-400', 'ring-2', 'ring-red-400/20');
    });

    // Validate required fields (non-empty)
    const required = form.querySelectorAll('[required]');
    let valid = true;
    required.forEach(field => {
      if (!field.value.trim()) {
        field.classList.add('border-red-400', 'ring-2', 'ring-red-400/20');
        valid = false;
      }
    });

    // Validate email format
    const emailField = document.getElementById('email');
    if (emailField && emailField.value.trim() && !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(emailField.value.trim())) {
      emailField.classList.add('border-red-400', 'ring-2', 'ring-red-400/20');
      valid = false;
    }

    if (!valid) return;

    // Show success — fade form, show message
    form.style.opacity = '0.4';
    form.style.pointerEvents = 'none';
    successMsg.classList.remove('hidden');
    successMsg.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
  });
}
```

- [ ] **Step 2: Verify mobile menu**

Open `contact.html` at mobile width (375px). Verify:
- Hamburger icon shows; clicking it opens the mobile menu
- Clicking outside the menu closes it
- Clicking the hamburger again closes it

- [ ] **Step 3: Verify form validation**

Open `contact.html`. Click "Send Quote Request" without filling anything. Verify:
- Company Name, Contact Person, Email Address, Phone Number inputs get a red border
- Optional fields (Instrument Type, Quantity, Service, Notes) do NOT get red borders
- No success message appears

Then fill all 4 required fields correctly. Click Submit. Verify:
- Form fades to 0.4 opacity
- Teal/brand-coloured success message appears below the submit button: "Thank you! We've received your request..."

Also test email validation: fill all required fields but enter `notanemail` in the Email field. Submit. The email field should get a red border, form should not submit.

- [ ] **Step 4: Commit**

```bash
git add contact.html
git commit -m "feat: add contact page JS — menu toggle, reveal, form validation"
```

---

### Task 8: Final review and cross-check

**Files:**
- Modify: `contact.html` — fixes only, no new features

- [ ] **Step 1: Desktop layout check (1280px+)**

Open `contact.html` at full desktop width. Verify:
- Form occupies roughly 60% of the content width (3/5 columns)
- Contact panel sits alongside the form on the right (2/5 columns)
- Both are vertically aligned from the top
- Page header background (`#E6F2F0`) is clearly distinct from the form section background (white)

- [ ] **Step 2: Mobile layout check (375px)**

Open at 375px width. Verify:
- Form appears first (full width)
- Contact panel appears below the form (full width)
- No horizontal overflow
- Form fields stack to single column on small screens

- [ ] **Step 3: Link check**

Manually click each of the following:
- Logo → should go to `index.html`
- "Home" breadcrumb → should go to `index.html`
- "Open in Maps" → should open Google Maps in new tab
- Phone number → should trigger phone dialler
- WhatsApp link → should open WhatsApp (or wa.me)
- Email link → should open mail client
- Footer links → verify they point to correct pages

- [ ] **Step 4: Contrast spot-check**

Verify visually:
- All label text and body text in the contact panel is clearly readable (not faded/grey)
- Working Hours sub-note ("Instruments can be dropped off...") is full opacity, not washed out
- Required asterisks are dark teal, not orange/red

- [ ] **Step 5: Final commit**

```bash
git add contact.html
git commit -m "feat: complete contact.html — full contact hub with form, panel, and map"
```

---

## Notes for Owner

- **Google Maps embed URL**: Once you have the embed URL from Google Maps → Share → Embed a map, paste it as the `src` of the `<iframe>` in Section 3 and commit. The rest of the page is fully functional without it.
- **Form backend**: The form currently shows a client-side success message only (no data is sent anywhere). When ready to receive live inquiries, connect to Formspree, Netlify Forms, or a backend endpoint by adding `action="..."` to the form and removing the JS submit handler.
