# Frontend Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the generic Mediumish/Bootstrap theme with a custom Typographic Bold B&W design: fresh vanilla CSS, dark mode toggle, hero+thumbnail homepage, minimal post front matter.

**Architecture:** All styles live in one file (`assets/css/main.scss` — kept as SCSS with plain CSS inside, Jekyll compiles it). CSS custom properties drive the entire light/dark theme. Dark mode is toggled by setting `data-theme="dark"` on `<html>` via a tiny inline `<head>` script (prevents flash) + a button click handler at end of `<body>`. Four layouts handle every page type: `default` (shell), `home` (hero + list), `post` (article), `page` (generic), `about` (profile card + content).

**Tech Stack:** Jekyll, GitHub Pages, Inter (Google Fonts), plain CSS (~400 lines inside main.scss), ~20 lines of vanilla JS inline in default.html, no other dependencies.

**Local dev:** `bundle exec jekyll serve --livereload` or `docker-compose up`

**Verification:** Since this is a static Jekyll site with no JS test framework, each task is verified by running `bundle exec jekyll build` (no errors) and then visually inspecting `http://localhost:4000` for the changed pages.

---

## File Map

| Action | File | Responsibility |
|---|---|---|
| Replace content | `assets/css/main.scss` | All CSS: reset, variables, nav, homepage, post, page, about, dark mode, responsive |
| Delete | `assets/css/screen.css` | Old Mediumish theme |
| Replace | `_layouts/default.html` | HTML shell: head, nav, footer, dark mode scripts |
| Create | `_layouts/home.html` | Homepage: site header, hero post, thumbnail post list, pagination |
| Replace | `_layouts/post.html` | Article: post header, hero image, body, tags, author card, prev/next |
| Replace | `_layouts/page.html` | Generic static page wrapper |
| Create | `_layouts/about.html` | Profile card (from `site.author`) + optional content |
| Replace | `index.html` | Switch to `home` layout |
| Replace | `_pages/about.md` | Use `about` layout |
| Replace | `_config.yml` | Add `author` block, remove unused Mediumish config |
| Delete | `assets/js/jquery.min.js` | Not needed |
| Delete | `assets/js/mediumish.js` | Old theme JS |
| Delete | `assets/js/ie10-viewport-bug-workaround.js` | Not needed |
| Delete | `assets/js/lazyload.js` | Not needed |
| Delete | `assets/js/lunr.js` | Search removed |
| Delete | `assets/js/lunrsearchengine.js` | Search removed |
| Delete | `_includes/share.html` | Social share removed |
| Delete | `_includes/disqus.html` | Comments removed |
| Delete | `_includes/adsense-under-header.html` | Ads removed |
| Delete | `_includes/featuredbox.html` | Replaced by home layout |
| Delete | `_includes/postbox.html` | Replaced by home layout |
| Delete | `_includes/pagination.html` | Inline in home layout |
| Delete | `_includes/toc.html` | TOC removed |
| Delete | `_includes/star_rating.html` | Ratings removed |
| Delete | `_includes/star_rating_postbox.html` | Ratings removed |
| Delete | `_includes/search-lunr.html` | Search removed |
| Delete | `_sass/_stars.scss` | Ratings removed |
| Delete | `_sass/_starsnonscss.scss` | Ratings removed |

---

### Task 1: Update `_config.yml`

**Files:**
- Modify: `_config.yml`

- [ ] **Step 1: Replace `_config.yml` content**

```yaml
# Site
name: "Amod's Blog"
title: "Amod's Blog"
description: "Thoughts on software engineering and building cool things."
logo: 'assets/images/log.png'
favicon: 'assets/images/log.png'
baseurl: ""
url: "https://amod0017.github.io"
google_analytics: 'UA-46278016-1'

# Author — edit this to update your profile everywhere
author:
  name: Amod
  bio: Software engineer. I write about building things and sharing what I learn.
  avatar: assets/images/author.png
  twitter: https://twitter.com/amod0017
  github: https://github.com/amod0017
  email: amod0017@gmail.com

# Plugins
plugins:
  - jekyll-paginate
  - jekyll-sitemap
  - jekyll-feed
  - jekyll-seo-tag
  - jekyll-archives

# Archives
jekyll-archives:
  enabled:
    - categories
  layout: archive
  permalinks:
    category: '/category/:name/'

# Pagination
paginate: 6
paginate_path: /page:num/

# Other
markdown: kramdown
permalink: /:title/
include: ["_pages"]

kramdown:
  input: GFM
  syntax_highlighter: rouge
  syntax_highlighter_opts:
    block:
      line_numbers: true

exclude: [changelog.md, LICENSE.txt, README.md, Gemfile, Gemfile.lock, docs/, docker-compose.yml]
```

- [ ] **Step 2: Verify Jekyll builds**

```bash
bundle exec jekyll build
```
Expected: no errors. (The missing SCSS imports will be fixed in Task 2.)

- [ ] **Step 3: Commit**

```bash
git add _config.yml
git commit -m "config: add author block, clean up unused settings"
```

---

### Task 2: Replace `assets/css/main.scss` with full new CSS

**Files:**
- Replace content: `assets/css/main.scss`
- Delete: `assets/css/screen.css`

- [ ] **Step 1: Delete `screen.css`**

```bash
rm assets/css/screen.css
```

- [ ] **Step 2: Replace the entire content of `assets/css/main.scss`**

```scss
---
---

/* ═══════════════════════════════════════
   CSS Custom Properties (light / dark)
═══════════════════════════════════════ */

:root {
  --bg:             #ffffff;
  --surface:        #f7f7f7;
  --border-strong:  #000000;
  --border-subtle:  #e8e8e8;
  --text-primary:   #000000;
  --text-body:      #1a1a1a;
  --text-secondary: #555555;
  --text-muted:     #999999;
  --text-faint:     #bbbbbb;
}

[data-theme="dark"] {
  --bg:             #111111;
  --surface:        #1a1a1a;
  --border-strong:  #f0f0f0;
  --border-subtle:  #222222;
  --text-primary:   #f0f0f0;
  --text-body:      #e8e8e8;
  --text-secondary: #888888;
  --text-muted:     #555555;
  --text-faint:     #444444;
}

/* ═══════════════════════════════════════
   Reset
═══════════════════════════════════════ */

*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
img { max-width: 100%; display: block; }

/* ═══════════════════════════════════════
   Base
═══════════════════════════════════════ */

body {
  font-family: 'Inter', system-ui, -apple-system, sans-serif;
  background: var(--bg);
  color: var(--text-body);
  font-size: 17px;
  line-height: 1.5;
  transition: background 0.15s, color 0.15s;
}

a { color: inherit; }

/* ═══════════════════════════════════════
   Navigation
═══════════════════════════════════════ */

.site-nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 22px 48px;
  border-bottom: 2px solid var(--border-strong);
  background: var(--bg);
  position: sticky;
  top: 0;
  z-index: 100;
  transition: background 0.15s;
}

.logo {
  font-size: 15px;
  font-weight: 900;
  letter-spacing: -0.5px;
  text-transform: uppercase;
  text-decoration: none;
  color: var(--text-primary);
}

.nav-right {
  display: flex;
  align-items: center;
  gap: 24px;
}

.nav-links {
  display: flex;
  gap: 28px;
  list-style: none;
}

.nav-links a {
  font-size: 11px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  text-decoration: none;
  color: var(--text-muted);
}

.nav-links a:hover { color: var(--text-primary); }

.theme-toggle {
  width: 36px;
  height: 20px;
  border-radius: 10px;
  border: 2px solid var(--border-strong);
  background: transparent;
  cursor: pointer;
  position: relative;
  flex-shrink: 0;
  padding: 0;
  transition: border-color 0.15s;
}

.theme-toggle::after {
  content: '';
  position: absolute;
  top: 2px;
  left: 2px;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: var(--text-muted);
  transition: transform 0.2s, background 0.2s;
}

[data-theme="dark"] .theme-toggle::after {
  transform: translateX(16px);
  background: var(--text-primary);
}

.nav-toggle {
  display: none;
  background: none;
  border: none;
  font-size: 22px;
  cursor: pointer;
  color: var(--text-primary);
  line-height: 1;
}

/* ═══════════════════════════════════════
   Page wrapper
═══════════════════════════════════════ */

.page {
  max-width: 720px;
  margin: 0 auto;
  padding: 0 24px;
}

/* ═══════════════════════════════════════
   Site header (homepage tagline)
═══════════════════════════════════════ */

.site-header {
  padding: 56px 0 40px;
  border-bottom: 1px solid var(--border-subtle);
}

.site-header .site-label {
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 2px;
  color: var(--text-faint);
  margin-bottom: 8px;
}

.site-header p {
  font-size: 15px;
  color: var(--text-secondary);
  line-height: 1.6;
}

/* ═══════════════════════════════════════
   Hero post (homepage latest)
═══════════════════════════════════════ */

.hero-post {
  display: block;
  text-decoration: none;
  color: inherit;
  border-bottom: 1px solid var(--border-subtle);
}

.hero-image {
  width: 100%;
  height: 320px;
  margin-top: 32px;
  background: var(--surface);
  position: relative;
  overflow: hidden;
}

.hero-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.hero-image::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(to top, rgba(0,0,0,0.65) 0%, transparent 55%);
}

.hero-image-text {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 28px;
  z-index: 1;
}

.hero-cat {
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 2px;
  color: rgba(255,255,255,0.75);
  margin-bottom: 8px;
}

.hero-title {
  font-size: 28px;
  font-weight: 900;
  letter-spacing: -1px;
  line-height: 1.05;
  color: #fff;
}

.hero-meta {
  font-size: 11px;
  color: rgba(255,255,255,0.6);
  margin-top: 10px;
}

.hero-post:hover .hero-title {
  text-decoration: underline;
  text-underline-offset: 4px;
}

/* ═══════════════════════════════════════
   Post list (homepage older posts)
═══════════════════════════════════════ */

.section-label {
  font-size: 9px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 2px;
  color: var(--text-faint);
  padding: 28px 0 0;
}

.post-list { list-style: none; }

.post-row {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 16px 0;
  border-bottom: 1px solid var(--border-subtle);
  text-decoration: none;
  color: inherit;
}

.post-row:hover .post-row-title {
  text-decoration: underline;
  text-underline-offset: 3px;
}

.post-thumb {
  width: 64px;
  height: 64px;
  flex-shrink: 0;
  overflow: hidden;
  background: var(--surface);
}

.post-thumb img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.post-info { flex: 1; min-width: 0; }

.post-row-cat {
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  color: var(--text-muted);
  margin-bottom: 4px;
}

.post-row-title {
  font-size: 17px;
  font-weight: 800;
  letter-spacing: -0.4px;
  line-height: 1.25;
  color: var(--text-primary);
}

.post-row-date {
  font-size: 11px;
  color: var(--text-faint);
  white-space: nowrap;
  margin-left: 8px;
}

/* ═══════════════════════════════════════
   Pagination
═══════════════════════════════════════ */

.pagination {
  display: flex;
  gap: 8px;
  justify-content: center;
  align-items: center;
  padding: 40px 0;
}

.pagination a,
.pagination span {
  font-size: 12px;
  font-weight: 700;
  text-decoration: none;
  color: var(--text-muted);
  border: 1.5px solid var(--border-subtle);
  padding: 6px 14px;
}

.pagination a:hover {
  color: var(--text-primary);
  border-color: var(--border-strong);
}

.pagination .current {
  color: var(--text-primary);
  border-color: var(--border-strong);
}

/* ═══════════════════════════════════════
   Post page
═══════════════════════════════════════ */

.post-header {
  padding: 56px 0 32px;
  border-bottom: 1px solid var(--border-subtle);
  margin-bottom: 40px;
}

.post-cat {
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 2px;
  color: var(--text-muted);
  margin-bottom: 14px;
}

.post-header h1 {
  font-size: 38px;
  font-weight: 900;
  letter-spacing: -1.5px;
  line-height: 1.05;
  color: var(--text-primary);
  margin-bottom: 20px;
}

.post-byline {
  display: flex;
  align-items: center;
  gap: 12px;
}

.author-avatar-sm {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  overflow: hidden;
  flex-shrink: 0;
  background: var(--surface);
}

.author-avatar-sm img { width: 100%; height: 100%; object-fit: cover; }

.byline-text {
  font-size: 13px;
  color: var(--text-secondary);
}

.byline-text strong {
  font-weight: 700;
  color: var(--text-primary);
}

.post-hero-image {
  width: 100%;
  height: 360px;
  margin-bottom: 40px;
  overflow: hidden;
  background: var(--surface);
}

.post-hero-image img { width: 100%; height: 100%; object-fit: cover; }

/* ═══════════════════════════════════════
   Article body
═══════════════════════════════════════ */

.article-body {
  font-size: 17px;
  line-height: 1.75;
  color: var(--text-body);
}

.article-body p { margin-bottom: 24px; }

.article-body h2 {
  font-size: 22px;
  font-weight: 900;
  letter-spacing: -0.6px;
  line-height: 1.2;
  color: var(--text-primary);
  margin: 48px 0 16px;
  border-top: 2px solid var(--border-strong);
  padding-top: 16px;
}

.article-body h3 {
  font-size: 18px;
  font-weight: 800;
  letter-spacing: -0.4px;
  color: var(--text-primary);
  margin: 32px 0 12px;
}

.article-body a {
  text-decoration: underline;
  text-underline-offset: 3px;
}

.article-body blockquote {
  border-left: 3px solid var(--border-strong);
  padding-left: 20px;
  margin: 28px 0;
  font-size: 18px;
  font-style: italic;
  color: var(--text-secondary);
  line-height: 1.6;
}

.article-body code {
  font-family: 'Courier New', Courier, monospace;
  font-size: 14px;
  background: var(--surface);
  padding: 2px 6px;
}

.article-body pre {
  background: var(--surface);
  border-left: 3px solid var(--border-strong);
  padding: 20px;
  margin: 24px 0;
  overflow-x: auto;
}

.article-body pre code {
  background: none;
  padding: 0;
  font-size: 13px;
  line-height: 1.6;
}

.article-body img { margin: 28px 0; }

.article-body ul,
.article-body ol {
  padding-left: 24px;
  margin-bottom: 24px;
}

.article-body li { margin-bottom: 6px; }

/* Rouge syntax highlighting */
.highlight { background: var(--surface); border-left: 3px solid var(--border-strong); padding: 20px; margin: 24px 0; overflow-x: auto; }
.highlight pre { background: none; border: none; padding: 0; margin: 0; }
.highlight .c, .highlight .c1, .highlight .cm { color: var(--text-muted); font-style: italic; }
.highlight .k, .highlight .kd, .highlight .kn { color: var(--text-primary); font-weight: 700; }
.highlight .s, .highlight .s1, .highlight .s2 { color: var(--text-secondary); }
.highlight .n, .highlight .na { color: var(--text-body); }

/* ═══════════════════════════════════════
   Post footer
═══════════════════════════════════════ */

.post-footer {
  margin-top: 56px;
  padding-top: 32px;
  border-top: 2px solid var(--border-strong);
}

.post-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-bottom: 32px;
}

.post-tag {
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1px;
  border: 1.5px solid var(--border-strong);
  padding: 4px 10px;
  color: var(--text-primary);
  text-decoration: none;
}

.post-tag:hover { background: var(--border-strong); color: var(--bg); }

.author-card {
  display: flex;
  gap: 20px;
  align-items: flex-start;
  padding: 24px;
  background: var(--surface);
  margin-bottom: 40px;
}

.author-avatar-lg {
  width: 56px;
  height: 56px;
  border-radius: 50%;
  overflow: hidden;
  flex-shrink: 0;
  background: var(--border-subtle);
}

.author-avatar-lg img { width: 100%; height: 100%; object-fit: cover; }

.author-card-name {
  font-size: 13px;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: -0.3px;
  color: var(--text-primary);
  margin-bottom: 6px;
}

.author-card-bio {
  font-size: 13px;
  color: var(--text-secondary);
  line-height: 1.5;
}

.post-nav {
  display: flex;
  justify-content: space-between;
  gap: 24px;
}

.post-nav a {
  flex: 1;
  text-decoration: none;
  color: var(--text-primary);
}

.nav-label {
  font-size: 9px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  color: var(--text-faint);
  margin-bottom: 4px;
}

.nav-title {
  font-size: 13px;
  font-weight: 800;
  letter-spacing: -0.3px;
  line-height: 1.3;
}

.post-nav a:hover .nav-title {
  text-decoration: underline;
  text-underline-offset: 3px;
}

.post-nav .next { text-align: right; }

/* ═══════════════════════════════════════
   Generic page (page.html layout)
═══════════════════════════════════════ */

.page-header {
  padding: 56px 0 32px;
  border-bottom: 1px solid var(--border-subtle);
  margin-bottom: 40px;
}

.page-header h1 {
  font-size: 32px;
  font-weight: 900;
  letter-spacing: -1px;
  color: var(--text-primary);
}

.page-body {
  font-size: 17px;
  line-height: 1.75;
  color: var(--text-body);
  padding-bottom: 64px;
}

.page-body p { margin-bottom: 20px; }
.page-body a { text-decoration: underline; text-underline-offset: 3px; }
.page-body h2 { font-size: 20px; font-weight: 900; letter-spacing: -0.5px; color: var(--text-primary); margin: 36px 0 14px; }

/* ═══════════════════════════════════════
   About page
═══════════════════════════════════════ */

.about-profile {
  display: flex;
  gap: 32px;
  align-items: flex-start;
  padding: 48px 0 40px;
  border-bottom: 1px solid var(--border-subtle);
  margin-bottom: 40px;
}

.about-avatar {
  width: 96px;
  height: 96px;
  border-radius: 50%;
  overflow: hidden;
  flex-shrink: 0;
  background: var(--surface);
}

.about-avatar img { width: 100%; height: 100%; object-fit: cover; }

.about-name {
  font-size: 22px;
  font-weight: 900;
  letter-spacing: -0.6px;
  color: var(--text-primary);
  margin-bottom: 10px;
}

.about-bio {
  font-size: 15px;
  color: var(--text-secondary);
  line-height: 1.6;
  margin-bottom: 16px;
}

.about-links {
  display: flex;
  gap: 16px;
  list-style: none;
}

.about-links a {
  font-size: 11px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  text-decoration: none;
  color: var(--text-muted);
  border-bottom: 1.5px solid var(--border-subtle);
  padding-bottom: 2px;
}

.about-links a:hover {
  color: var(--text-primary);
  border-color: var(--border-strong);
}

/* ═══════════════════════════════════════
   Footer
═══════════════════════════════════════ */

.site-footer {
  max-width: 720px;
  margin: 64px auto 0;
  padding: 28px 24px;
  border-top: 2px solid var(--border-strong);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.footer-name {
  font-size: 13px;
  font-weight: 900;
  letter-spacing: -0.3px;
  text-transform: uppercase;
  color: var(--text-primary);
}

.footer-links {
  display: flex;
  gap: 20px;
}

.footer-links a {
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1px;
  color: var(--text-muted);
  text-decoration: none;
}

.footer-links a:hover { color: var(--text-primary); }

.footer-copy {
  font-size: 10px;
  color: var(--text-faint);
}

/* ═══════════════════════════════════════
   Responsive — mobile (≤ 640px)
═══════════════════════════════════════ */

@media (max-width: 640px) {
  .site-nav { padding: 16px 20px; }

  .nav-right .nav-links { display: none; }
  .nav-right .nav-links.open {
    display: flex;
    flex-direction: column;
    position: fixed;
    top: 61px;
    left: 0;
    right: 0;
    background: var(--bg);
    border-bottom: 2px solid var(--border-strong);
    padding: 20px;
    gap: 16px;
    z-index: 99;
  }

  .nav-toggle { display: block; }

  .page { padding: 0 16px; }

  .hero-image { height: 220px; }
  .hero-title { font-size: 20px; }

  .post-header h1 { font-size: 26px; }
  .post-hero-image { height: 220px; }

  .site-footer { flex-direction: column; gap: 14px; text-align: center; }
  .post-nav { flex-direction: column; }
  .post-nav .next { text-align: left; }

  .about-profile { flex-direction: column; }
}
```

- [ ] **Step 3: Verify Jekyll builds without errors**

```bash
bundle exec jekyll build
```
Expected: `Build complete`, no errors. The existing layouts still reference `screen.css` so the site will look broken visually, but it should build.

- [ ] **Step 4: Commit**

```bash
git add assets/css/
git commit -m "style: replace Mediumish CSS with Typographic Bold design system"
```

---

### Task 3: Create `_layouts/default.html`

**Files:**
- Replace: `_layouts/default.html`

- [ ] **Step 1: Replace `_layouts/default.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{% if page.title %}{{ page.title }} | {% endif %}{{ site.name }}</title>
  {% seo %}
  <link rel="icon" href="{{ site.baseurl }}/assets/images/log.png">
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;900&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="{{ site.baseurl }}/assets/css/main.css">
  <script>
    (function() {
      var saved = localStorage.getItem('theme');
      var prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
      document.documentElement.setAttribute(
        'data-theme',
        saved ? saved : (prefersDark ? 'dark' : 'light')
      );
    })();
  </script>
</head>
<body>

<nav class="site-nav">
  <a class="logo" href="{{ site.baseurl }}/">{{ site.author.name }}</a>
  <div class="nav-right">
    <ul class="nav-links" id="nav-links">
      <li><a href="{{ site.baseurl }}/blog">Blog</a></li>
      <li><a href="{{ site.baseurl }}/projects">Projects</a></li>
      <li><a href="{{ site.baseurl }}/about">About</a></li>
    </ul>
    <button class="theme-toggle" id="theme-toggle" aria-label="Toggle dark mode" title="Toggle dark mode"></button>
  </div>
  <button class="nav-toggle" id="nav-toggle" aria-label="Toggle navigation">&#9776;</button>
</nav>

{{ content }}

<footer class="site-footer">
  <span class="footer-name">{{ site.author.name }}</span>
  <div class="footer-links">
    {% if site.author.twitter %}<a href="{{ site.author.twitter }}">Twitter</a>{% endif %}
    {% if site.author.github %}<a href="{{ site.author.github }}">GitHub</a>{% endif %}
  </div>
  <span class="footer-copy">&copy; {{ 'now' | date: '%Y' }}</span>
</footer>

<script>
  document.getElementById('theme-toggle').addEventListener('click', function() {
    var html = document.documentElement;
    var isDark = html.getAttribute('data-theme') === 'dark';
    html.setAttribute('data-theme', isDark ? 'light' : 'dark');
    localStorage.setItem('theme', isDark ? 'light' : 'dark');
  });
  document.getElementById('nav-toggle').addEventListener('click', function() {
    document.getElementById('nav-links').classList.toggle('open');
  });
</script>

</body>
</html>
```

- [ ] **Step 2: Serve and verify the shell**

```bash
bundle exec jekyll serve --livereload
```
Open `http://localhost:4000`. Expected:
- Inter font loads
- Nav shows `AMOD` logo left, `Blog / Projects / About` links right, pill toggle button
- Footer shows name left, Twitter/GitHub links center, `© 2026` right
- Dark mode toggle switches theme immediately, persists on page reload
- Hamburger menu appears on narrow viewport, links collapse/expand

- [ ] **Step 3: Commit**

```bash
git add _layouts/default.html
git commit -m "layout: new default shell with nav, footer, dark mode toggle"
```

---

### Task 4: Create `_layouts/home.html` and update `index.html`

**Files:**
- Create: `_layouts/home.html`
- Replace: `index.html`

- [ ] **Step 1: Create `_layouts/home.html`**

```html
---
layout: default
---

<div class="page">

  <header class="site-header">
    <div class="site-label">{{ site.name }}</div>
    <p>{{ site.description }}</p>
  </header>

  {% assign posts = paginator.posts | default: site.posts %}
  {% assign first_post = posts | first %}
  {% assign other_posts = posts | slice: 1, 100 %}

  {% if first_post %}
  <a class="hero-post" href="{{ site.baseurl }}{{ first_post.url }}">
    <div class="hero-image">
      {% if first_post.image %}
        <img src="{{ site.baseurl }}/{{ first_post.image }}" alt="{{ first_post.title }}">
      {% endif %}
      <div class="hero-image-text">
        {% if first_post.category %}<div class="hero-cat">{{ first_post.category }}</div>{% endif %}
        <div class="hero-title">{{ first_post.title }}</div>
        <div class="hero-meta">{{ first_post.date | date: "%b %-d, %Y" }}</div>
      </div>
    </div>
  </a>
  {% endif %}

  {% if other_posts.size > 0 %}
  <div class="section-label">More posts</div>
  <ul class="post-list">
    {% for post in other_posts %}
    <li>
      <a class="post-row" href="{{ site.baseurl }}{{ post.url }}">
        {% if post.image %}
        <div class="post-thumb">
          <img src="{{ site.baseurl }}/{{ post.image }}" alt="{{ post.title }}">
        </div>
        {% endif %}
        <div class="post-info">
          {% if post.category %}<div class="post-row-cat">{{ post.category }}</div>{% endif %}
          <div class="post-row-title">{{ post.title }}</div>
        </div>
        <span class="post-row-date">{{ post.date | date: "%b %Y" }}</span>
      </a>
    </li>
    {% endfor %}
  </ul>
  {% endif %}

  {% if paginator.total_pages > 1 %}
  <div class="pagination">
    {% if paginator.previous_page %}
      <a href="{{ site.baseurl }}{{ paginator.previous_page_path }}">&larr; Newer</a>
    {% endif %}
    <span class="current">{{ paginator.page }} / {{ paginator.total_pages }}</span>
    {% if paginator.next_page %}
      <a href="{{ site.baseurl }}{{ paginator.next_page_path }}">Older &rarr;</a>
    {% endif %}
  </div>
  {% endif %}

</div>
```

- [ ] **Step 2: Replace `index.html`**

```html
---
layout: home
title: Home
---
```

- [ ] **Step 3: Verify homepage**

Open `http://localhost:4000`. Expected:
- Site label + tagline below the nav
- Latest post ("Rethinking Quarterly Planning") renders as a full-width hero image block with category + title overlaid. If no `image:` is set on the post, the block shows a solid dark surface background — still looks intentional.
- Older posts ("Creating Your Blog...", "Starting This Journey") appear as rows with category + title + date. If they have no `image:` set, the thumbnail slot is absent — rows remain clean.
- Clicking a post navigates to the post URL (will look unstyled until Task 5).

- [ ] **Step 4: Commit**

```bash
git add _layouts/home.html index.html
git commit -m "layout: homepage with hero post and thumbnail list"
```

---

### Task 5: Replace `_layouts/post.html`

**Files:**
- Replace: `_layouts/post.html`

- [ ] **Step 1: Replace `_layouts/post.html`**

```html
---
layout: default
---

{% assign author = site.author %}
{% assign words = content | number_of_words %}
{% assign mins = words | divided_by: 200 | plus: 1 %}

<div class="page">

  <div class="post-header">
    {% if page.category %}<div class="post-cat">{{ page.category }}</div>{% endif %}
    <h1>{{ page.title }}</h1>
    <div class="post-byline">
      <div class="author-avatar-sm">
        <img src="{{ site.baseurl }}/{{ author.avatar }}" alt="{{ author.name }}">
      </div>
      <div class="byline-text">
        <strong>{{ author.name }}</strong> &middot; {{ page.date | date: "%b %-d, %Y" }} &middot; {{ mins }} min read
      </div>
    </div>
  </div>

  {% if page.image %}
  <div class="post-hero-image">
    <img src="{{ site.baseurl }}/{{ page.image }}" alt="{{ page.title }}">
  </div>
  {% endif %}

  <div class="article-body">
    {{ content }}
  </div>

  <div class="post-footer">

    {% assign all_tags = page.tags | concat: page.categories %}
    {% if all_tags.size > 0 %}
    <div class="post-tags">
      {% for tag in page.tags %}
        <a class="post-tag" href="{{ site.baseurl }}/tags#{{ tag | replace: ' ', '-' }}">#{{ tag }}</a>
      {% endfor %}
      {% for cat in page.categories %}
        <a class="post-tag" href="{{ site.baseurl }}/categories#{{ cat | replace: ' ', '-' }}">{{ cat }}</a>
      {% endfor %}
    </div>
    {% endif %}

    <div class="author-card">
      <div class="author-avatar-lg">
        <img src="{{ site.baseurl }}/{{ author.avatar }}" alt="{{ author.name }}">
      </div>
      <div>
        <div class="author-card-name">{{ author.name }}</div>
        <div class="author-card-bio">{{ author.bio }}</div>
      </div>
    </div>

    <div class="post-nav">
      {% if page.previous %}
      <a href="{{ site.baseurl }}{{ page.previous.url }}" class="prev">
        <div class="nav-label">&larr; Previous</div>
        <div class="nav-title">{{ page.previous.title }}</div>
      </a>
      {% else %}<div></div>{% endif %}
      {% if page.next %}
      <a href="{{ site.baseurl }}{{ page.next.url }}" class="next">
        <div class="nav-label">Next &rarr;</div>
        <div class="nav-title">{{ page.next.title }}</div>
      </a>
      {% endif %}
    </div>

  </div>

</div>
```

- [ ] **Step 2: Verify post pages**

Open `http://localhost:4000/rethinking-quarterly-planning/` (or whichever post URL Jekyll generates). Expected:
- Category label → large bold title → avatar + name + date + read time
- If the post has `image:` in front matter, full-width hero image appears
- Body text is large (17px), readable, with Inter font
- H2 headings have a 2px top border
- Author card appears below the post with avatar + bio from `_config.yml`
- Prev/next links work
- Dark mode applies correctly

- [ ] **Step 3: Commit**

```bash
git add _layouts/post.html
git commit -m "layout: new post page with author card and prev/next"
```

---

### Task 6: Replace `_layouts/page.html` and create `_layouts/about.html`

**Files:**
- Replace: `_layouts/page.html`
- Create: `_layouts/about.html`
- Replace: `_pages/about.md`

- [ ] **Step 1: Replace `_layouts/page.html`**

```html
---
layout: default
---

<div class="page">
  <div class="page-header">
    <h1>{{ page.title }}</h1>
  </div>
  <div class="page-body">
    {{ content }}
  </div>
</div>
```

- [ ] **Step 2: Create `_layouts/about.html`**

```html
---
layout: default
---

{% assign author = site.author %}

<div class="page">

  <div class="about-profile">
    <div class="about-avatar">
      <img src="{{ site.baseurl }}/{{ author.avatar }}" alt="{{ author.name }}">
    </div>
    <div>
      <div class="about-name">{{ author.name }}</div>
      <p class="about-bio">{{ author.bio }}</p>
      <ul class="about-links">
        {% if author.twitter %}<li><a href="{{ author.twitter }}">Twitter</a></li>{% endif %}
        {% if author.github %}<li><a href="{{ author.github }}">GitHub</a></li>{% endif %}
        {% if author.email %}<li><a href="mailto:{{ author.email }}">Email</a></li>{% endif %}
      </ul>
    </div>
  </div>

  {% if content != "" %}
  <div class="page-body">
    {{ content }}
  </div>
  {% endif %}

</div>
```

- [ ] **Step 3: Update `_pages/about.md` to use the about layout**

```markdown
---
layout: about
title: About
permalink: /about/
---
```

- [ ] **Step 4: Verify pages**

Check these URLs:
- `http://localhost:4000/about/` — profile card with avatar, name, bio, Twitter/GitHub links from `_config.yml`. No extra content below (the old prose was removed; if you want to keep it, add it back as markdown below the front matter).
- `http://localhost:4000/projects/` — page header "Projects" + "Coming soon" body text
- `http://localhost:4000/techblog/` — page header "Tech Blog" + "Coming soon" body text

- [ ] **Step 5: Commit**

```bash
git add _layouts/page.html _layouts/about.html _pages/about.md
git commit -m "layout: page and about layouts, about page pulls from config"
```

---

### Task 7: Delete old files

**Files:**
- Delete: all files listed below

- [ ] **Step 1: Delete old JS, CSS, includes, and SCSS**

```bash
# Unused SCSS partials (main.scss no longer imports these)
rm _sass/_stars.scss _sass/_starsnonscss.scss

# JS
rm assets/js/jquery.min.js
rm assets/js/mediumish.js
rm assets/js/ie10-viewport-bug-workaround.js
rm assets/js/lazyload.js
rm assets/js/lunr.js
rm assets/js/lunrsearchengine.js

# Includes
rm _includes/share.html
rm _includes/disqus.html
rm _includes/adsense-under-header.html
rm _includes/featuredbox.html
rm _includes/postbox.html
rm _includes/pagination.html
rm _includes/toc.html
rm _includes/star_rating.html
rm _includes/star_rating_postbox.html
rm _includes/search-lunr.html
```

- [ ] **Step 2: Verify build still succeeds after deletions**

```bash
bundle exec jekyll build
```
Expected: `Build complete`, no errors. If Jekyll reports a missing include or file, check which layout is still referencing it and remove the `{% include %}` tag.

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "chore: remove Bootstrap, jQuery, Mediumish, and unused includes"
```

---

### Task 8: Full site verification

**Files:** None — read-only verification pass.

- [ ] **Step 1: Start the dev server and check all pages**

```bash
bundle exec jekyll serve --livereload
```

Visit each URL and verify:

| URL | What to check |
|---|---|
| `http://localhost:4000/` | Hero post + thumbnail list, footer, nav sticky on scroll |
| `http://localhost:4000/rethinking-quarterly-planning/` | Post header, body typography, author card, prev/next |
| `http://localhost:4000/about/` | Avatar, name, bio, social links from config |
| `http://localhost:4000/projects/` | Page title + body text |
| `http://localhost:4000/techblog/` | Page title + body text |

- [ ] **Step 2: Verify dark mode**

On `http://localhost:4000/`:
1. Click the toggle button — page switches to dark mode immediately, no flash
2. Reload — dark mode persists (localStorage)
3. Click again — switches back to light
4. Open DevTools → Application → Local Storage → confirm `theme` key is set

- [ ] **Step 3: Verify mobile layout**

In DevTools, set viewport to 375px wide. Expected:
- Hamburger button (☰) visible, nav links hidden
- Tapping hamburger reveals nav links as a dropdown
- Hero image shrinks to 220px height
- Post title font size reduces to 26px
- Footer stacks vertically

- [ ] **Step 4: Final commit**

```bash
git add -A
git commit -m "feat: frontend redesign complete — Typographic Bold B&W with dark mode"
```
