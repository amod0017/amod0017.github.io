# Frontend Redesign — Design Spec

**Date:** 2026-05-10
**Status:** Approved

---

## Goals

- Establish a polished personal brand that reflects Amod as a software engineer
- Create a distinctive, memorable design (creative expression over convention)
- Keep authoring friction minimal — adding a post or updating a profile should require one file touch
- Readership growth is explicitly out of scope for this iteration

---

## Approach

Keep Jekyll and GitHub Pages. Remove Bootstrap 4, jQuery, Mediumish theme CSS, and all associated JS entirely. Replace with:

- One hand-written `assets/css/main.css` (~300–400 lines, no build step)
- One small `assets/js/theme.js` (~30 lines) for the dark mode toggle
- Google Fonts: **Inter** (weights 400, 600, 700, 900) loaded via `<link>` in `<head>`
- No other dependencies

---

## Visual Design

### Aesthetic

**Typographic Bold** — the design steps aside for the writing. Bold, heavy type creates personality without decoration. Clean whitespace, strong structural borders, no accent color.

### Color Palette

**Light mode**

| Token | Value | Usage |
|---|---|---|
| Background | `#ffffff` | Page background |
| Surface | `#f7f7f7` | Author card, code blocks |
| Border strong | `#000000` | Nav border, footer border, h2 rule |
| Border subtle | `#e8e8e8` | Post list separators |
| Text primary | `#000000` | Titles, logo, footer name |
| Text body | `#1a1a1a` | Post body copy |
| Text secondary | `#555555` | Tagline, byline |
| Text muted | `#999999` | Category labels, dates |
| Text faint | `#bbbbbb` | Post dates in list |

**Dark mode**

| Token | Value | Usage |
|---|---|---|
| Background | `#111111` | Page background |
| Surface | `#1a1a1a` | Author card, code blocks |
| Border strong | `#f0f0f0` | Nav border, footer border |
| Border subtle | `#222222` | Post list separators |
| Text primary | `#f0f0f0` | Titles, logo |
| Text body | `#e8e8e8` | Post body copy |
| Text secondary | `#888888` | Tagline, byline |
| Text muted | `#555555` | Category labels |
| Text faint | `#444444` | Post dates in list |

### Typography

- **Font:** Inter (Google Fonts)
- **Post titles on homepage:** 17px / weight 800 / letter-spacing -0.4px
- **Hero post title:** 26–36px / weight 900 / letter-spacing -1px+
- **Body copy:** 17px / weight 400 / line-height 1.75
- **Category labels:** 10px / weight 700 / uppercase / letter-spacing 2px
- **Nav links:** 10–11px / weight 700 / uppercase / letter-spacing 1.5px
- **H2 in posts:** 22px / weight 900 / letter-spacing -0.6px / 2px solid top border

---

## Pages

### Homepage (`/`)

1. **Sticky nav** — `AMOD` logo left, `Blog · Projects · About` links + theme toggle right. 2px solid bottom border.
2. **Site header** — muted label ("Amod's Blog") + tagline below.
3. **Hero post** — latest post rendered as a full-width image with gradient overlay; category + title + meta overlaid at bottom. If no image set, renders a solid dark block.
4. **Post list** — all remaining posts as rows: `[thumbnail] [category / title] [date]`. Thumbnail is optional — rows without one render cleanly without it.
5. **Footer** — 2px solid top border. Name left, social links center, copyright right.

### Post page (`/post-title/`)

1. **Nav** (same as homepage)
2. **Post header** — category label → large bold title → avatar + author name + date + read time
3. **Hero image** — full-width below header (optional)
4. **Body** — 17px / 1.75 line-height. H2s have 2px top border. Blockquotes have left bar. Code blocks have left border + monospace + surface background.
5. **Post footer** — tags as bordered labels → author card (avatar + name + bio) → prev/next navigation
6. **Footer** (same as homepage)

No sidebar. No comments. No social share buttons.

### About page (`/about`)

Profile page: large avatar, name, bio, social links. Pulled from `_config.yml` author block.

### Projects page (`/projects`)

Simple list of projects. Minimal — title + one-line description + optional link.

---

## Authoring Experience

### Adding a post

Create `_posts/YYYY-MM-DD-title.md` with minimal front matter:

```yaml
---
title: My Post Title
category: Engineering
image: assets/images/my-image.jpg   # optional
---
```

No author field required (single-author blog). No featured flag. No rating. No toc.

### Profile updates

All author data lives in `_config.yml`:

```yaml
author:
  name: Amod
  bio: Software engineer. I write about building things.
  avatar: assets/images/author.png
  twitter: https://twitter.com/yourhandle
  github: https://github.com/amod0017
```

Changing avatar: drop new image in `assets/images/`, update one line. Changing bio: update one line.

---

## Dark Mode

**Default:** system preference via `prefers-color-scheme: dark` (Chrome/Safari/Firefox all support this).

**Override:** pill toggle button in the nav bar. Empty circle = light; filled circle shifted right = dark.

**Implementation:**
- On page load, read `localStorage.getItem('theme')`. If set, apply `data-theme` attribute to `<html>`. If not set, check `window.matchMedia('(prefers-color-scheme: dark)')`.
- All dark mode CSS uses `[data-theme="dark"]` selectors (not `@media`) so the manual choice always wins.
- Toggle click flips the attribute and saves to `localStorage` — persists across all pages and visits.

---

## Files to Create / Replace

| Action | File |
|---|---|
| Replace | `_layouts/default.html` |
| Replace | `_layouts/post.html` |
| Replace | `_layouts/page.html` |
| Create | `_layouts/home.html` |
| Replace | `assets/css/main.css` (new hand-written CSS) |
| Delete | `assets/css/screen.css` |
| Create | `assets/js/theme.js` |
| Delete | `assets/js/mediumish.js` |
| Delete | `assets/js/jquery.min.js` |
| Delete | `assets/js/ie10-viewport-bug-workaround.js` |
| Delete | `assets/js/lazyload.js` |
| Replace | `_includes/postbox.html` |
| Delete | `_includes/featuredbox.html` (hero logic moves into `_layouts/home.html`) |
| Replace | `_includes/pagination.html` |
| Delete | `_includes/share.html` |
| Delete | `_includes/disqus.html` |
| Delete | `_includes/adsense-under-header.html` |
| Replace | `_config.yml` (add author block, clean up unused config) |
| Replace | `index.html` |
| Replace | `_pages/about.md` |
| Keep | `_pages/techblog.md` (apply new page layout, no content changes) |
| Keep | `_pages/categories.md` (out of scope, keep as-is) |
| Keep | `_pages/tags.md` (out of scope, keep as-is) |

---

## Out of Scope

- Newsletter signup (Mailchimp bar removed)
- Comments (Disqus removed)
- Ads (Adsense removed)
- Social share buttons
- Search (Lunr removed)
- Star ratings
- Category/tag archive pages (kept as-is for now)
- Readership/SEO optimisation (next iteration)
