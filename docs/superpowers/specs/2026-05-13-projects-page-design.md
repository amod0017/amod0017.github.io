# Projects Page Design

**Date:** 2026-05-13
**Status:** Approved

## Goal

Replace the "Coming soon" placeholder on `/projects/` with a reusable, data-driven project card grid. Designed to grow as more projects are added over time.

## Data Structure

**File:** `_data/projects.yml`

Each project entry:

```yaml
- name: contribution-logger
  description: A local Streamlit dashboard that turns sprint task CSVs into interactive charts for engineering performance reviews. Tracks KPIs like AI adoption rate, proactive initiative %, and services touched.
  github: https://github.com/amod0017/contribution-logger
  tech: [Python, Streamlit, Pandas, Plotly]
  status: active
```

Fields:
- `name` — project display name
- `description` — 1-2 sentence summary of what it solves
- `github` — full GitHub URL
- `tech` — list of technology/language tags
- `status` — `active` or `archived`

Adding a new project = adding one YAML block to this file. No layout changes needed.

## Layout

**File:** `_layouts/projects.html` (new)

- Extends `default` layout
- Renders a page header (matching `.page-header` pattern)
- Iterates over `site.data.projects`
- Renders a 2-column `.project-grid` (collapses to 1 column on mobile)

Each `.project-card`:
- Title — links to GitHub URL
- Description text
- Tech stack tags (`.project-tech-tag` inline pills)
- Footer row: status badge + "View on GitHub" link

## CSS

**File:** `assets/css/main.css` (additions)

New classes following existing naming conventions:

| Class | Purpose |
|---|---|
| `.project-grid` | 2-col CSS grid, matches `.post-grid` gap/padding |
| `.project-card` | Card with border, hover effect matching `.post-card` |
| `.project-card-body` | Padding, flex column layout |
| `.project-card-title` | Bold title, links to GitHub |
| `.project-card-desc` | Description text, `--text-secondary` color |
| `.project-tech-tags` | Flex row, wraps tag pills |
| `.project-tech-tag` | Pill tag, styled like `.post-tag` |
| `.project-card-footer` | Flex row space-between: status badge + GitHub link |
| `.project-status` | Badge base styles |
| `.project-status--active` | Accent color (uses `--accent`) |
| `.project-status--archived` | Muted color (uses `--text-muted`) |

Responsive: collapses to 1 column at the same breakpoint as `.post-grid`.

## Page Update

**File:** `_pages/projects.md`

Change `layout: page` to `layout: projects`. Remove placeholder text. The layout handles all rendering.

## Files to Create/Modify

| Action | File |
|---|---|
| Create | `_data/projects.yml` |
| Create | `_layouts/projects.html` |
| Modify | `assets/css/main.css` |
| Modify | `_pages/projects.md` |
