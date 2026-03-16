# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a static single-page portfolio website for Bailey Burritt — a video editor and audio engineer based in Rochester, NY. It is hosted on GitHub Pages at `baileyburritt.github.io/portfolio-2017.io/`.

There is no build system, package manager, or compilation step. Development is done by editing files directly and previewing in a browser.

## Running Locally

Open `index.html` directly in a browser, or use any static file server:

```bash
# Python 3
python -m http.server 8080

# Node (if npx available)
npx serve .
```

## Architecture

The active site is a **fully self-contained single file**: `index.html`. All CSS lives in an inline `<style>` block, all JavaScript is inline at the bottom, and all page sections (nav, hero, services/bento grid, about, contact, footer) are in that one file. No external CSS or JS files are loaded.

A `css/` and `js/` directory exist in the repo with legacy files from an older version of the site (jQuery, Skeleton grid, FlexSlider, etc.) — these are **not used** by the current `index.html` and can be ignored.

**Page sections:**

- **Nav** — fixed, transitions to frosted glass on scroll (`.scrolled` class toggled via scroll listener). Includes a hamburger button (`#hamburger`) that toggles a `#mobileNav` dropdown at ≤968px. Nav links highlight the active section via IntersectionObserver.
- **Hero** — fullscreen with `<video>` background, overlay, and a frosted glass panel (`hero-glass`)
- **Services** — CSS bento grid (4-col, 3-row on desktop) showcasing: Video Editing, Motion Graphics, Audio Engineering, DVD Authoring
- **About** — two-column grid (image + text + skill tags)
- **Contact** — two-column: Formspree form on left, contact info + social links on right
- **Footer** — single-line copyright

**Images** (`images/`):

- `profileImages/` — profile photo used in About section

## Inline CSS Design System

All styles use CSS custom properties defined in `:root`. **Do not hardcode colors or fonts** — always reference variables.

**Color palette (cinematic dark / ice-frost):**

```css
--bg-dark: #080c14;
--bg-mid: #0d1520;
--bg-light: #152030;
--text-primary: #e8f4ff;        /* ice white */
--text-secondary: #8ab0c8;      /* cool muted blue */
--text-muted: #4a6478;
--accent-cyan: #b0d8f0;         /* soft ice blue — NOT electric neon */
--accent-blue: #5a9fc0;         /* cool slate blue */
--glass-bg: rgba(220, 240, 255, 0.06);
--glass-border: rgba(180, 220, 255, 0.12);
--glass-shadow: rgba(0, 0, 0, 0.4);
```

**Typography:**

```css
--font-display: 'DM Serif Display', Georgia, serif;   /* headings, hero h1, large bento h3 */
--font-body: 'DM Sans', system-ui, sans-serif;         /* all body text, nav, buttons, forms */
```

Google Fonts loaded via `<link>` in `<head>`: `DM Serif Display` (ital variants) + `DM Sans` (300–600 weight range).

**Glassmorphism pattern:**

- Backgrounds: semi-transparent `rgba` with `backdrop-filter: blur()`
- Borders: `rgba(180,220,255,0.12)` frost lines
- Hover states: border brightening + inner glow (`box-shadow: inset ...`) — no translateY lift on bento items
- Icons/buttons use frosted glass fill (`rgba(176,216,240,0.08–0.12)`) not gradients

**Button variants:**

- `.cta-button` — frosted glass fill with frost border (default)
- `.cta-button--outline` — transparent background, 2px solid `--accent-cyan` border. Use this instead of inline `style` overrides for outline buttons.

**About section typography hierarchy:**

- `h2` — large serif (48px, `--font-display`) for the main headline
- `h3` — eyebrow/label style (13px, uppercase, 2px letter-spacing, `--accent-cyan`). Appears below h2 as a subtitle descriptor, not a competing heading.

**Fade-in animation pattern:**

Elements that should animate in on scroll use the `.fade-observe` CSS class (pre-hidden via CSS, not JS) and receive a `.visible` class from IntersectionObserver. Do **not** set `opacity: 0` or `transform` inline via JavaScript — use the class system to avoid flash-of-content.

```css
.fade-observe { opacity: 0; transform: translateY(30px); transition: ... }
.fade-observe.visible { opacity: 1; transform: translateY(0); }
```

**Social icons:** All social links use inline SVG icons (`currentColor`) — do **not** use text characters, Unicode symbols, or emoji as icon substitutes.

**Responsive breakpoints** (inline `<style>` block):

- `max-width: 1200px` — bento grid collapses to 2-col
- `max-width: 968px` — stacked layout, nav links hidden, hamburger shown, font sizes reduced, `.bento-item.large` gets `min-height: 280px`
- `max-width: 640px` — mobile padding, stacked hero buttons, single-col skills

## Inline JavaScript Behaviors

- **Nav scroll**: Adds `.scrolled` class to `#nav` after 100px scroll
- **Hamburger menu**: `#hamburger` toggles `.open` on both itself and `#mobileNav`. Closes on any mobile nav link click. Updates `aria-expanded` attribute.
- **Active nav links**: IntersectionObserver on `section[id]` elements. Toggles `.active` on matching `.nav-links a` when a section is ≥40% visible.
- **Contact form**: POSTs to Formspree (`https://formspree.io/f/xqebgoyr`) via `fetch()` with `Accept: application/json`. Shows `.form-message.success` or `.form-message.error` div. Submit button shows a CSS spinner (`.submit-spinner`) and gains `.loading` class during submission; disabled with `cursor: wait` while pending.
- **Character counter**: Live count on `#message` textarea (max 5000)
- **Smooth scroll**: `querySelectorAll('a[href^="#"]')` with `scrollIntoView({ behavior: 'smooth' })`. The CSS `html { scroll-behavior }` property is intentionally absent — JS is the single scroll handler.
- **Fade-in animations**: IntersectionObserver on `.fade-observe` elements — adds `.visible` class on intersection, then unobserves.
