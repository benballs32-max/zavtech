# ZavTech.net — Website Build Plan

## Overview

A multi-page static/SSG website for **zavtech.net** — a personal side-hustle selling modded/retro gaming hardware (PSPs, handhelds, retro builds) and self-built software tools. Based in Norwich, UK. The aesthetic is **cyberpunk-retro terminal** — think CRT monitors, scanlines, phosphor glow, monospace fonts, dark grid backgrounds. No AI-generated slop, no corporate feel. Built by one person, sold by one person.

---

## Design System

### Fonts (Google Fonts)
- **Logo / Headings:** `Bebas Neue` or `Orbitron` — heavy, blocky
- **Body / UI:** `Rajdhani` — clean, slightly military
- **Prices / Terminal text:** `Share Tech Mono` or `VT323` — monospace CRT feel

### Colour Palette
```css
--cyan:     #00f5ff;   /* primary accent, glows */
--magenta:  #ff006e;   /* secondary accent, hover states */
--yellow:   #ffee00;   /* amber terminal accent (prices, tags) */
--dark:     #050810;   /* page background */
--darker:   #020408;   /* card/panel backgrounds */
--panel:    rgba(0,245,255,0.04);
--border:   rgba(0,245,255,0.2);
--text:     #c8e8f0;
--muted:    rgba(200,232,240,0.5);
```

### Global Visual Effects
- **Scanlines overlay:** `position: fixed`, `repeating-linear-gradient`, `pointer-events: none`, full viewport, very subtle (opacity ~0.06)
- **Grid background:** `position: fixed`, CSS grid lines via `background-image` linear gradients, `background-size: 40px 40px`
- **Glitch effect on logo:** CSS keyframe animation, colour channel offset on hover
- **Crosshair cursor:** Custom CSS cursor using an SVG data URI
- **Clip-path on buttons/cards:** `clip-path: polygon(...)` for angular corners
- **Box shadows on cards:** `0 0 20px rgba(0,245,255,0.1)` — cyan phosphor glow

### Component Patterns
```
Cards:       Dark background, cyan border, clip-path corner cuts, glow on hover
Buttons:     Uppercase, letter-spacing, clip-path, magenta or cyan fill, → arrow
Nav:         Horizontal, monospace, brackets around active item e.g. [SHOP]
Tags/badges: Monospace, yellow accent colour, uppercase
Prices:      VT323 or Share Tech Mono, yellow, slightly larger than body
Section labels: Small, muted, monospace — e.g. "// 01 — HARDWARE"
```

---

## Tech Stack

| Layer | Choice | Notes |
|---|---|---|
| Framework | **Astro** | SSG, fast, easy routing, supports vanilla JS + CSS |
| Styling | **Vanilla CSS** with CSS variables | No Tailwind — full control over the aesthetic |
| JS | Vanilla JS | Minimal interactivity, no framework needed |
| Fonts | Google Fonts (preconnect) | Bebas Neue, Rajdhani, Share Tech Mono |
| Deployment | **Cloudflare Pages** | Free, integrates with `zavtech.net` domain already on Cloudflare |
| Dev | `npm run dev` (Astro) | Hot reload |

> **Why Astro?** Static output (fast, SEO-friendly), file-based routing matches the page structure below, can drop in `.astro` components for reuse, and no backend needed initially.

---

## Site Structure

```
zavtech.net/
├── /                  → Home (hero + overview)
├── /shop              → Product listing (hardware + software)
├── /product/[slug]    → Individual product page
├── /about             → About + ethos
└── /contact           → Contact page (mailto, no form backend)
```

### File Structure (Astro)
```
zavtech/
├── public/
│   ├── favicon.ico
│   └── images/
│       └── products/     ← product images (PNG/WebP)
├── src/
│   ├── components/
│   │   ├── Nav.astro
│   │   ├── Footer.astro
│   │   ├── ProductCard.astro
│   │   ├── Hero.astro
│   │   └── ScanlineOverlay.astro
│   ├── layouts/
│   │   └── Base.astro    ← shared layout with scanlines, grid, fonts
│   ├── pages/
│   │   ├── index.astro
│   │   ├── shop.astro
│   │   ├── about.astro
│   │   ├── contact.astro
│   │   └── product/
│   │       └── [slug].astro
│   ├── data/
│   │   └── products.js   ← product catalogue (JS array/JSON)
│   └── styles/
│       └── global.css    ← CSS variables, reset, scanlines, grid, fonts
├── astro.config.mjs
└── package.json
```

---

## Pages

### 1. Home (`/`)

**Sections:**
1. **Nav** — `ZAVTECH` logo left, links right: `SHOP / ABOUT / CONTACT`
2. **Hero** — Full-width, large headline e.g. `MODDED HARDWARE. BUILT SOFTWARE. NO BULLSHIT.`, sub-copy, two CTA buttons: `[ VIEW SHOP → ]` and `[ CONTACT → ]`
3. **What I sell** — Two columns: Hardware (PSPs, retro handhelds, builds) and Software (personal tools). Icon/tag + brief description each.
4. **Featured Products** — 3 product cards pulled from `products.js`, linked to `/shop`
5. **How it works** — 4-step process: `01 Browse → 02 Enquire/Buy → 03 Built & Tested → 04 Delivered`
6. **Footer**

### 2. Shop (`/shop`)

**Layout:** Filter tabs at top (`ALL / HARDWARE / SOFTWARE`), then grid of `ProductCard` components.

**ProductCard props:**
```js
{
  slug: "modded-psp-3000",
  name: "Modded PSP 3000",
  category: "hardware",        // "hardware" | "software"
  price: "£45",                // or "POA" for commissions
  status: "available",         // "available" | "sold" | "enquire"
  tags: ["PSP", "CFW", "Retro"],
  short_desc: "PSP 3000 running CFW 6.61 PRO-C2...",
  image: "/images/products/psp-3000.png"
}
```

Filter tabs use JS to show/hide cards by `data-category` attribute. No page reload.

### 3. Product (`/product/[slug]`)

**Layout:**
- Large product image left
- Name, price (yellow monospace), status badge, tags
- Full description
- `[ ENQUIRE → ]` button → mailto with subject pre-filled
- Related products at bottom

### 4. About (`/about`)

**Content:**
- Who I am (Norwich-based, network engineer background, genuine interest in retro hardware and self-hosted tech)
- What ZavTech is (side project, not a corporation — one person, hands-on)
- Every piece of hardware is personally configured and tested before it leaves
- Direct contact, no ticket system

**Tone:** Honest, no-nonsense, first person. No buzzwords.

### 5. Contact (`/contact`)

- Email: `hello@zavtech.net` as a `mailto:` link
- Note: Norwich, UK — local collection available on hardware
- Maybe: Discord handle if relevant
- No backend form, just a mailto link styled as a button

---

## Product Data (`src/data/products.js`)

```js
export const products = [
  {
    slug: "modded-psp-3000",
    name: "Modded PSP 3000",
    category: "hardware",
    price: "£45",
    status: "available",
    tags: ["PSP", "CFW", "6.61 PRO-C2"],
    short_desc: "PSP 3000 running CFW 6.61 PRO-C2 with 32GB memory card loaded with titles.",
    full_desc: "...",
    image: "/images/products/psp-3000.png"
  },
  {
    slug: "anbernic-rg35xx",
    name: "Anbernic RG35XX Plus",
    category: "hardware",
    price: "£55",
    status: "available",
    tags: ["Retro Handheld", "Linux", "Multi-system"],
    short_desc: "Pre-configured retro handheld with custom firmware and curated game library.",
    full_desc: "...",
    image: "/images/products/rg35xx.png"
  },
  {
    slug: "custom-retro-pc",
    name: "Custom Retro Gaming PC",
    category: "hardware",
    price: "POA",
    status: "enquire",
    tags: ["PC Build", "Commission", "Retro Gaming"],
    short_desc: "Bespoke PC build tailored for retro gaming — spec and budget to you.",
    full_desc: "...",
    image: "/images/products/retro-pc.png"
  },
  {
    slug: "docgen-pro",
    name: "DocGen Pro",
    category: "software",
    price: "£10",
    status: "available",
    tags: ["Windows", "Desktop", "Documents"],
    short_desc: "Desktop tool for generating templated documents. Built in Python/CustomTkinter.",
    full_desc: "...",
    image: "/images/products/docgen-pro.png"
  }
  // Add more as stock changes
];
```

---

## Key Interactions / JS Behaviour

### Shop filter
```js
// Shop page — filter by category
document.querySelectorAll('.filter-tab').forEach(tab => {
  tab.addEventListener('click', () => {
    const cat = tab.dataset.category;
    document.querySelectorAll('.product-card').forEach(card => {
      card.style.display = (cat === 'all' || card.dataset.category === cat) ? '' : 'none';
    });
    document.querySelectorAll('.filter-tab').forEach(t => t.classList.remove('active'));
    tab.classList.add('active');
  });
});
```

### Glitch logo animation
```css
@keyframes glitch {
  0%   { clip-path: inset(0 0 95% 0); transform: translate(-2px, 0); }
  20%  { clip-path: inset(30% 0 50% 0); transform: translate(2px, 0); color: var(--magenta); }
  40%  { clip-path: inset(60% 0 20% 0); transform: translate(-1px, 0); }
  60%  { clip-path: inset(10% 0 80% 0); transform: translate(3px, 0); color: var(--cyan); }
  80%  { clip-path: inset(50% 0 30% 0); transform: translate(-2px, 0); }
  100% { clip-path: inset(0 0 0 0); transform: translate(0, 0); color: inherit; }
}
.logo:hover { animation: glitch 0.4s steps(1) forwards; }
```

### Crosshair cursor
```css
body {
  cursor: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24'%3E%3Ccircle cx='12' cy='12' r='4' fill='none' stroke='%2300f5ff' stroke-width='1.5'/%3E%3Cline x1='12' y1='0' x2='12' y2='8' stroke='%2300f5ff' stroke-width='1.5'/%3E%3Cline x1='12' y1='16' x2='12' y2='24' stroke='%2300f5ff' stroke-width='1.5'/%3E%3Cline x1='0' y1='12' x2='8' y2='12' stroke='%2300f5ff' stroke-width='1.5'/%3E%3Cline x1='16' y1='12' x2='24' y2='12' stroke='%2300f5ff' stroke-width='1.5'/%3E%3C/svg%3E") 12 12, crosshair;
}
```

---

## Deployment

1. Build with `npm run build` (outputs to `dist/`)
2. Deploy to **Cloudflare Pages** (connect GitHub repo or drag-and-drop `dist/`)
3. Custom domain: `zavtech.net` → already on Cloudflare DNS, just point CNAME to Pages

---

## Out of Scope (for now)

- Shopping cart / payment processing (Stripe etc.) — enquiry-based initially
- CMS or admin panel — products managed via `products.js` directly
- Blog / news section
- Backend / API

---

## Notes for Claude Code

- Use Astro's static site generation — `output: 'static'` in `astro.config.mjs`
- All styling in vanilla CSS — no Tailwind, no CSS-in-JS
- Keep JS minimal and vanilla — no React, no bundled framework
- `[slug].astro` should use `getStaticPaths()` to generate product pages from `products.js`
- Global styles (scanlines, grid, cursor, fonts) live in `global.css` and are imported in `Base.astro`
- Product images are placeholders initially — use styled SVG placeholders if no real images available
- Test locally with `npm run dev` before deploying
