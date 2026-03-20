# Contact Page Design Spec — contact.html
> Created: 2026-03-20
> Project: German Calibration website
> Page: contact.html

---

## Purpose

Dedicated destination for the primary "Get a Quote" CTA in the site header. Serves two types of visitors: those who want to fill a quote request form, and those who want to call, WhatsApp, or physically visit the lab.

---

## Page Type

Full contact hub — form and contact details are both first-class. Not a focused conversion-only page. Rationale: regional B2B calibration lab customers frequently call or visit in person (dropping off instruments), so contact details are as important as the form.

---

## Tech Stack

Matches all other pages:
- Single `.html` file, all styles inline via Tailwind CDN + `<style>` block
- Tailwind CDN (`https://cdn.tailwindcss.com`)
- Tailwind config extended in `<script>` tag — exact config from CLAUDE.md Section 4
- Fonts: DM Serif Display + Inter from Google Fonts CDN (exact import from CLAUDE.md Section 7)
- No JS frameworks, no build tools, vanilla JS only
- Files open directly in browser

---

## `<head>` Meta Tags

```html
<title>Contact — German Calibration | NABL Accredited Lab, Rajkot</title>
<meta name="description" content="Get in touch with German Calibration Lab LLP — NABL accredited calibration lab in Rajkot. Request a quote, call us, or visit at Plot No. 10B, Udhyog Nagar 2." />
```

---

## Page Structure

### Header & Footer
Identical to `services.html` and `index.html` — copy exactly:
- Same fixed navbar: logo, nav links (Services, Industries, About), phone, Client Portal button, "Get a Quote" CTA
- Same footer: address, links, contact details, copyright

No sticky sub-nav on this page (single-focus page, no sections to jump between).

---

### Section 1 — Compact Page Header

- Background: `bg-brand-50` (`#E6F2F0`) — matches services page hero
- `py-14 lg:py-20`
- Breadcrumb: `Home > Contact`
  - Home → `index.html`, aria-current not set
  - Contact → current page, `aria-current="page"`
  - `aria-label="Breadcrumb"` on the `<nav>` wrapping it
- Heading: *"Get in Touch"* — `font-serif`, `clamp(2rem, 4vw, 2.75rem)`, `letter-spacing: -0.03em`, DM Serif Display
- Subheading: *"Request a calibration quote, ask a question, or plan a visit to our lab in Rajkot."* — Inter, `text-brand-ink-light`, `text-lg leading-relaxed`
- No CTA buttons — the form below is the action
- Reveal animation on heading and subheading

---

### Section 2 — Two-Column Main Body

- Background: `bg-brand-white` (`#FBFEFD`)
- `py-16 lg:py-24`
- Grid: `grid lg:grid-cols-5 gap-10 lg:gap-16`
- **Mobile stacking order: form first (top), contact panel below.** Rationale: primary action first on mobile.

---

#### Left Column — `lg:col-span-3` — Quote Request Form

Eyebrow: `text-brand-700 text-xs font-semibold uppercase tracking-widest mb-3` — "Request a Quote"

Form element: `<form novalidate id="quote-form" name="quote-request">` — `novalidate` suppresses browser-native validation; all validation is handled by JS (same pattern as homepage).

**Fields — exact same as homepage Section 9:**

Row 1 (2-col grid `sm:grid-cols-2 gap-5`):
- **Company Name** `id="company"` `required` — `type="text"`, placeholder: `e.g. Acme Industries Ltd.`
- **Contact Person** `id="contact"` `required` — `type="text"`, placeholder: `Your name`

Row 2 (2-col grid):
- **Email Address** `id="email"` `required` — `type="email"`, placeholder: `you@company.com`
- **Phone Number** `id="phone"` `required` — `type="tel"`, placeholder: `+91 98765 43210`

Row 3 (2-col grid):
- **Instrument Type(s)** `id="instrument"` *(optional, no asterisk)* — `type="text"`, placeholder: `e.g. Micrometers, Calipers`
- **Approx. Quantity** `id="quantity"` *(optional)* — `type="text"`, placeholder: `e.g. 25 instruments`

Row 4 (full width):
- **Preferred Service** `id="service"` *(optional select)*
  - Options: `Select a service...` (default, value=""), `Lab Calibration`, `On-Site Calibration`, `Third Party Inspection / CMM`, `Repair & Maintenance`, `CMM Training`, `Other / Not sure`
  - `appearance-none` + custom SVG chevron background-image (same inline style as homepage):
    ```
    style="background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 24 24' stroke='%234A6663'%3E%3Cpath stroke-linecap='round' stroke-linejoin='round' stroke-width='2' d='M19 9l-7 7-7-7'/%3E%3C/svg%3E"); background-repeat: no-repeat; background-position: right 14px center; background-size: 16px;"
    ```

Row 5 (full width):
- **Additional Notes** `id="message"` *(optional)* — `<textarea rows="4">`, placeholder: `Any specific requirements, urgency, or questions...`

**All input/select/textarea styling:**
```
w-full px-4 py-3 bg-white border border-brand-200 rounded-xl text-brand-ink
placeholder-brand-ink-light/50 text-sm focus:outline-none focus:border-brand-500
focus:ring-2 focus:ring-brand-500/15 transition-all
```
Note: `focus:border-brand-500` uses brand-500 (#47857A) on a white border — this is a non-text UI element; WCAG 1.4.11 non-text contrast (3:1 minimum) is met. This matches homepage exactly.

**Labels:** `block text-brand-ink text-sm font-semibold mb-2`
**Required asterisks:** `<span class="text-brand-700">*</span>` — brand-700 (#2D5D54) passes WCAG AA ✅
**Optional labels:** `<span class="text-brand-ink-light font-normal">(optional)</span>`
**`aria-required="true"`** on all required inputs.

**Submit button:**
```html
<button type="submit" class="w-full btn-primary justify-center py-3.5 text-base rounded-xl">
  Send Quote Request
  <svg><!-- arrow icon --></svg>
</button>
```

**Success state** (hidden by default, shown after JS validation passes and form is "submitted"):
```html
<div id="form-success" class="hidden bg-brand-50 border border-brand-200 rounded-xl px-5 py-4 text-brand-700 text-sm font-medium text-center">
  Thank you! We've received your request and will be in touch within one business day.
</div>
```
JS behaviour on submit: validate required fields + email format. If valid: show `#form-success` (remove `hidden`), form stays visible. If invalid: show per-field error messages, do not show success state.

**Field error state:**
- Input border: `border-red-400`
- Focus ring: `focus:ring-red-500/15`
- Error message below field: `<p class="text-red-600 text-xs mt-1">...</p>`

---

#### Right Column — `lg:col-span-2` — Contact Panel

Card: `bg-brand-50 rounded-2xl p-6 border border-brand-100`

Four contact blocks, each using the homepage pattern:
- Icon container: `w-8 h-8 rounded-lg bg-brand-500/15 flex items-center justify-center flex-shrink-0 mt-0.5`
- Icon: `w-4 h-4 text-brand-500`, `aria-hidden="true"`
- Label: `text-brand-ink font-semibold text-sm mb-0.5`
- Value: `text-brand-ink-light text-sm leading-relaxed` or link in `text-brand-700 hover:text-brand-800`

**Block 1 — Address**
- Icon: map-pin SVG (same as homepage)
- Label: "Address"
- Value: `Plot No. 10B, Umakant Pandit,`br`Udhyog Nagar 2, Rajkot,`br`Gujarat, India`
- "Open in Maps" link: `href="https://maps.app.goo.gl/doezCLm5DbpFEQcc7?g_st=ac"` `target="_blank" rel="noopener"` — `text-brand-700 text-xs font-semibold hover:text-brand-800`

**Block 2 — Phone**
- Icon: phone SVG
- Label: "Phone"
- Value: `+91 92765 51009` — `href="tel:+919276551009"`

**Block 3 — WhatsApp** *(new addition vs. homepage)*
- Icon: WhatsApp SVG — `w-4 h-4 text-brand-500 aria-hidden="true"` (same as all other icons — no brand green, consistency over recognition; the "WhatsApp" label identifies the channel)
- Label: "WhatsApp"
- Value: `+91 92765 51009` — `href="https://wa.me/919276551009"` `target="_blank" rel="noopener"` `aria-label="Chat on WhatsApp"`

**Block 4 — Email**
- Icon: envelope SVG
- Label: "Email"
- Value: `lab@germancalibration.com` — `href="mailto:lab@germancalibration.com"`

**Block 5 — Hours** (info box, not a link)
- Icon: clock SVG, `aria-hidden="true"`
- Label: "Working Hours"
- Value: `Monday – Saturday`br`9:00 AM – 6:00 PM`
- Sub-note in `text-brand-ink-light text-xs mt-1`: *"Instruments can be dropped off directly at the lab."* — full opacity required; `/70` opacity on `brand-ink-light` over `bg-brand-50` fails WCAG AA (≈3.2:1)

---

### Section 3 — Google Maps Embed

- `border-t border-brand-100`
- Free embed, no API key — `<iframe>` pointing to `maps.google.com/embed`
- Search query: `Plot 10B Udhyog Nagar 2 Rajkot Gujarat India`
- Height: `h-96` (384px) desktop, `h-64` (256px) mobile — use `class="w-full h-64 lg:h-96"`
- `style="border:0;"` `loading="lazy"` `referrerpolicy="no-referrer-when-downgrade"`
- `title="German Calibration Lab location — Plot No. 10B, Udhyog Nagar 2, Rajkot"`
- `aria-label="Map showing German Calibration Lab location in Rajkot"`
- No heading above the map — self-explanatory in context

**Embed URL format:**
```
https://www.google.com/maps/embed?pb=!1m18!...
```
⚠️ **OWNER TO PROVIDE** — Generate via Google Maps → search the lab address → Share → Embed a map → copy the `src` URL from the iframe code. Use the exact URL — do not construct manually. Until provided, use a placeholder comment in the HTML: `<!-- TODO: paste Google Maps embed src URL here -->`

---

## JS — Form Validation

Same pattern as homepage. Required fields: Company Name, Contact Person, Email Address, Phone Number.

```js
document.getElementById('quote-form').addEventListener('submit', function(e) {
  e.preventDefault();
  // validate required fields
  // validate email format
  // if valid: show #form-success
  // if invalid: show per-field errors
});
```

---

## Scroll & Animation

- `.reveal` + IntersectionObserver pattern — same CSS and JS as homepage/services
- Header scroll effect — same as other pages (`#site-header.scrolled`)
- No sub-nav

---

## Accessibility Checklist

- `<form novalidate>` with all required inputs having `aria-required="true"`
- All `<label for="">` linked to matching input `id`
- Required asterisks in `text-brand-700` (6.62:1 contrast ✅)
- All decorative icons: `aria-hidden="true"`
- External links (WhatsApp, Maps): `target="_blank" rel="noopener"` + descriptive `aria-label`
- Map iframe: `title` and `aria-label` set
- Focus states: `focus-visible:ring-2 focus-visible:ring-brand-500` on interactive elements
- Breadcrumb `<nav aria-label="Breadcrumb">` with `aria-current="page"` on current item
- Semantic structure: `<header>`, `<main>`, `<section aria-label="">`, `<footer>`

---

## WCAG Contrast — Critical Rules

Follows CLAUDE.md Section 5:
- All small text, labels, eyebrows: `text-brand-700` (6.62:1 ✅)
- No `text-brand-500` on small body text
- Required asterisks: `text-brand-700`
- `btn-primary` background: `#2D5D54`, hover: `#224845`
- `focus:border-brand-500` on form inputs is acceptable — non-text UI element, WCAG 1.4.11 (3:1) met

---

## Content Rules (CLAUDE.md Section 8)

- No client names anywhere
- No NABL badge on this page (not a services page)
- Certificate number not needed here

---

## Form Submission Handling

**Static only** — no backend connection. Same as homepage: JS validates and shows success message. Owner will connect to email service or backend in a future phase. The `action` attribute is omitted from the `<form>` element.

---

## Files

| File | Notes |
|---|---|
| `contact.html` | New file |
| No new assets needed | Logo, fonts, Tailwind CDN already available |
