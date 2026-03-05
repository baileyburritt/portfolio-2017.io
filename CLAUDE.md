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

The active site is a **fully self-contained single file**: `index.html`. All CSS lives in an inline `<style>` block (lines ~48–790), all JavaScript is inline at the bottom, and all page sections (nav, hero, services/bento grid, about, contact, footer) are in that one file. No external CSS or JS files are loaded.

A `css/` and `js/` directory exist in the repo with legacy files from an older version of the site (jQuery, Skeleton grid, FlexSlider, etc.) — these are **not used** by the current `index.html` and can be ignored.

**Page sections:**

- **Nav** — fixed, transitions to frosted glass on scroll (`.scrolled` class toggled via Intersection Observer / scroll listener)
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

**Responsive breakpoints** (inline `<style>` block):

- `max-width: 1200px` — bento grid collapses to 2-col
- `max-width: 968px` — stacked layout, nav links hidden, font sizes reduced
- `max-width: 640px` — mobile padding, stacked hero buttons, single-col skills

## Inline JavaScript Behaviors

- **Nav scroll**: Adds `.scrolled` class to `#nav` after 100px scroll
- **Contact form**: POSTs to Formspree (`https://formspree.io/f/xqebgoyr`) via `fetch()` with `Accept: application/json`. Shows `.form-message.success` or `.form-message.error` div
- **Character counter**: Live count on `#message` textarea (max 5000)
- **Smooth scroll**: `querySelectorAll('a[href^="#"]')` with `scrollIntoView({ behavior: 'smooth' })`
- **Fade-in animations**: IntersectionObserver on `.bento-item`, `.about-content > *`, `.contact-item` — sets opacity/translateY on intersection

## Known Issues

- The JSON-LD structured data block (`<script type="application/ld+json">`) has a trailing comma after the last array item in `hasOfferCatalog.itemListElement` — technically invalid JSON but browsers typically ignore it
- The hero `<video src="hero-showreel.mp4">` file does not exist in the repo; the video element falls back to a dark overlay gracefully
