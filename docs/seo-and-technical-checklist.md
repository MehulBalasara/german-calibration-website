# SEO & Technical Checklist

Things to add after core pages are built. Prioritized by impact.

---

## Priority 1 — High Impact, Do Before Launch

### Schema.org JSON-LD (Structured Data)
Add inside `<head>` of every page. Tells Google exactly what this business is.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "German Calibration Lab LLP",
  "description": "NABL accredited calibration laboratory in Rajkot, Gujarat. ISO/IEC 17025:2017. Certificate CC-4038.",
  "url": "https://germancalibration.com",
  "telephone": "+91-9276551009",
  "email": "lab@germancalibration.com",
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Rajkot",
    "addressRegion": "Gujarat",
    "addressCountry": "IN"
  },
  "areaServed": "India",
  "accreditation": "NABL CC-4038 · ISO/IEC 17025:2017"
}
</script>
```

**Why:** Enables rich results in Google Search (star ratings, address, phone visible directly in search results). Biggest single SEO win.

---

### Open Graph Tags
Add inside `<head>` of every page. Controls how the link looks when shared on WhatsApp, LinkedIn, Twitter.

```html
<meta property="og:title" content="German Calibration — NABL Accredited Calibration Lab, Rajkot" />
<meta property="og:description" content="NABL accredited calibration lab in Rajkot. ISO/IEC 17025:2017 · CC-4038. Dimensional, pressure, acoustics & on-site calibration." />
<meta property="og:type" content="website" />
<meta property="og:url" content="https://germancalibration.com" />
<meta property="og:image" content="https://germancalibration.com/brand items/og-preview.jpg" />
<meta name="twitter:card" content="summary_large_image" />
```

**Note:** Need to create a 1200×630px `og-preview.jpg` image (logo on brand background). Update URL once domain is confirmed.

---

### Canonical URL Tag
Add inside `<head>` of every page (update URL per page).

```html
<link rel="canonical" href="https://germancalibration.com/" />
```

**Why:** Prevents duplicate content penalties if the site is ever accessible at multiple URLs (www vs non-www, http vs https).

---

### sitemap.xml
Create at root: `German Website/sitemap.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url><loc>https://germancalibration.com/</loc><priority>1.0</priority></url>
  <url><loc>https://germancalibration.com/services.html</loc><priority>0.9</priority></url>
  <url><loc>https://germancalibration.com/accreditation.html</loc><priority>0.8</priority></url>
  <url><loc>https://germancalibration.com/about.html</loc><priority>0.7</priority></url>
  <url><loc>https://germancalibration.com/industries.html</loc><priority>0.7</priority></url>
  <url><loc>https://germancalibration.com/contact.html</loc><priority>0.8</priority></url>
</urlset>
```

**After creating:** Submit to Google Search Console.

---

### robots.txt
Create at root: `German Website/robots.txt`

```
User-agent: *
Allow: /

Sitemap: https://germancalibration.com/sitemap.xml
```

---

## Priority 2 — Do When Real Photos Are Added

### Image Lazy Loading
Add `loading="lazy"` to all `<img>` tags that are below the fold (not the hero).

```html
<img src="..." alt="..." loading="lazy" />
```

### Image Compression
- Convert photos to WebP format before adding to the site
- Target: under 150KB per image
- Tool: squoosh.app (free, browser-based)

---

## Priority 3 — Accessibility Improvements

### Skip to Main Content Link
Add as the very first element inside `<body>` on every page.

```html
<a href="#main-content" class="sr-only focus:not-sr-only focus:absolute focus:top-4 focus:left-4 bg-brand-700 text-white px-4 py-2 rounded z-50">
  Skip to main content
</a>
```
And add `id="main-content"` to the `<main>` tag.

**Why:** Keyboard-only users (and screen reader users) can skip the nav without tabbing through every link.

---

### Respect `prefers-reduced-motion`
Add to the `<style>` block on every page that has scroll reveal animations.

```css
@media (prefers-reduced-motion: reduce) {
  .reveal {
    opacity: 1;
    transform: none;
    transition: none;
  }
}
```

**Why:** Some users have vestibular disorders or motion sensitivity. This OS-level setting should be honored.

---

## Priority 4 — Security (Minor for Static Site)

### External Links
Any `<a>` that links to an external site should have:
```html
<a href="https://external.com" target="_blank" rel="noopener noreferrer">...</a>
```
**Why:** Prevents the linked page from accessing your page's context via `window.opener`.

---

## Outside the Website (But Critical)

- **Google Business Profile** — claim and fill out at business.google.com. Free. Huge for local search ("calibration lab near Rajkot").
- **Google Search Console** — submit sitemap.xml here after launch. Monitors indexing and search performance.
- **NAP consistency** — Name, Address, Phone must be identical everywhere: website, Google Business Profile, Justdial, IndiaMart, etc.

---

## Status

| Item | Done |
|---|---|
| Schema.org JSON-LD | ☐ |
| Open Graph tags | ☐ |
| Canonical URLs | ☐ |
| sitemap.xml | ☐ |
| robots.txt | ☐ |
| Image lazy loading | ☐ |
| Image compression | ☐ |
| Skip to main content | ☐ |
| prefers-reduced-motion | ☐ |
| External link rel= | ☐ |
| Google Business Profile | ☐ |
| Google Search Console | ☐ |
