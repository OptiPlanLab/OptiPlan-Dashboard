---
title: 'OptiPlan Logo & Brand Identity Integration'
slug: 'optiplan-logo-brand-identity'
created: '2026-02-23'
status: 'completed'
stepsCompleted: [1, 2, 3, 4]
tech_stack: ['HTML5', 'CSS3 (embedded <style>)', 'Vanilla JS', 'Varela Round + JetBrains Mono fonts', 'Tabler Icons']
files_to_modify: ['index.html']
code_patterns: ['BEM naming (.sidebar__brand-pill)', 'CSS custom properties on :root', 'Dark/Light theme via [data-theme]', 'RTL layout (dir=rtl)', 'Monolithic single-file architecture']
test_patterns: ['Manual visual testing (no automated test framework)']
---

# Tech-Spec: OptiPlan Logo & Brand Identity Integration

**Created:** 2026-02-23

## Overview

### Problem Statement

The OptiPlan dashboard lacks proper brand identity. A logo file (`user-data/LOGO-ICON.png`) exists but is not used anywhere in the dashboard. The sidebar displays a gradient pill badge instead of the actual logo. There is no favicon set for the browser tab. The topbar has no OptiPlan branding at all.

### Solution

Integrate the OptiPlan logo image into the sidebar brand area, set it as the browser favicon, and add subtle branding to the topbar. Replace the current gradient pill styling with clean, flat "OptiPlan" text.

### Scope

**In Scope:**
- Sidebar: Replace gradient pill with logo image + "OptiPlan" text (clean, no gradient background)
- Favicon: Set `LOGO-ICON.png` as the browser tab icon
- Topbar: Add subtle "OptiPlan" branding alongside "מרכז בקרה"

**Out of Scope:**
- Redesigning the sidebar layout beyond the brand area
- Creating new logo assets
- Changing module color schemes

## Context for Development

### Codebase Patterns

- **Architecture**: Monolithic single-file `index.html` containing all CSS (`<style>`), HTML, and JS (`<script>`)
- **CSS naming**: BEM convention — `.sidebar__brand`, `.sidebar__brand-pill`, `.topbar__title`, etc.
- **Theming**: CSS custom properties on `:root`, with `[data-theme="dark"]` and `[data-theme="light"]` overrides
- **Layout**: RTL (`dir="rtl"`), sidebar fixed 240px on `inset-inline-end: 0`
- **Fonts**: `--font-main: 'Varela Round'` for UI, `--font-mono: 'JetBrains Mono'` for data/mono
- **Icons**: Tabler Icons webfont (`ti ti-*` classes)
- **Spacing**: `--gap: 16px`, `--gap-sm: 8px`, `--gap-lg: 24px`
- **Radius**: `--radius: 14px`, `--radius-sm: 8px`

### Files to Reference

| File | Purpose |
| ---- | ------- |
| `index.html:1-16` | `<head>` section — where favicon `<link>` needs to go |
| `index.html:234-251` | CSS for `.sidebar__brand` and `.sidebar__brand-pill` (gradient pill) |
| `index.html:384-456` | CSS for `.topbar__title-block`, `.topbar__title`, `.topbar__subtitle` |
| `index.html:1990-1994` | HTML sidebar brand badge (`<div class="sidebar__brand">`) |
| `index.html:2085-2101` | HTML topbar (`<header class="topbar">`) |
| `user-data/LOGO-ICON.png` | The logo asset to integrate |

### Technical Decisions

- **Sidebar brand**: Replace `.sidebar__brand-pill` gradient background with a flex row containing an `<img>` for the logo + a `<span>` for "OptiPlan" text. Clean background (no gradient).
- **Favicon**: Add `<link rel="icon" href="user-data/LOGO-ICON.png">` in the `<head>`. PNG favicon is widely supported.
- **Topbar**: Prepend "OptiPlan" before the existing topbar title. The topbar title becomes "OptiPlan" and the subtitle remains "מרכז בקרה · הרחבת קו מטרו, גוש דן". This keeps branding visible without cluttering the layout.
- **Theme support**: Brand text color must work in both dark and light themes — use `var(--text-primary)` for the "OptiPlan" text.
- **No gradient on text**: "OptiPlan" displayed in `--font-main` (Varela Round), standard `var(--text-primary)` color. No `background-clip: text` or gradient tricks.

## Implementation Plan

### Tasks

- [x] Task 1: Add favicon to `<head>`
  - File: `index.html` — `<head>` section (after line 15)
  - Action: Add `<link rel="icon" type="image/png" href="user-data/LOGO-ICON.png">` before the closing `</head>` or after the Tabler Icons link
  - Notes: PNG favicons are supported by all modern browsers. No conversion needed.

- [x] Task 2: Restyle sidebar brand CSS — remove gradient, add logo layout
  - File: `index.html` — CSS lines 234-251
  - Action: Replace `.sidebar__brand` and `.sidebar__brand-pill` styles:
    - `.sidebar__brand`: Change to `display: flex; align-items: center; gap: var(--gap-sm); padding: var(--gap) var(--gap);`
    - `.sidebar__brand-logo` (new): `width: 28px; height: 28px; object-fit: contain;` — for the `<img>` element
    - `.sidebar__brand-text` (new): `font-family: var(--font-main); font-size: 16px; font-weight: 700; color: var(--text-primary); letter-spacing: 0.5px;` — clean, no gradient
    - Remove `.sidebar__brand-pill` entirely (or repurpose the selector)
  - Notes: Keep `justify-content: center` on `.sidebar__brand` so logo + text are centered in the 240px sidebar.

- [x] Task 3: Update sidebar brand HTML — replace pill with logo + text
  - File: `index.html` — HTML lines 1990-1994
  - Action: Replace the `<div class="sidebar__brand">` contents:
    ```html
    <div class="sidebar__brand">
      <img class="sidebar__brand-logo" src="user-data/LOGO-ICON.png" alt="OptiPlan logo" width="28" height="28">
      <span class="sidebar__brand-text">OptiPlan</span>
    </div>
    ```
  - Notes: `alt` text for accessibility. Explicit `width`/`height` attributes prevent layout shift.

- [x] Task 4: Update topbar HTML — add "OptiPlan" branding
  - File: `index.html` — HTML lines 2085-2101
  - Action: Change the topbar title block:
    ```html
    <div class="topbar__title-block">
      <span class="topbar__title">OptiPlan</span>
      <span class="topbar__subtitle">מרכז בקרה · הרחבת קו מטרו, גוש דן</span>
    </div>
    ```
    The existing "מרכז בקרה" moves into the subtitle, combined with the project subtitle using a `·` separator.
  - Notes: This gives "OptiPlan" the prominent 18px bold treatment and keeps the Hebrew context as secondary info. No CSS changes needed — reuses existing `.topbar__title` and `.topbar__subtitle` styles.

### Acceptance Criteria

- [x] AC 1: Given the dashboard is loaded in a browser, when the user views the browser tab, then the OptiPlan logo (`LOGO-ICON.png`) is displayed as the favicon.
- [x] AC 2: Given the dashboard is loaded, when the user views the sidebar brand area, then a logo image + "OptiPlan" text (mixed-case) is displayed with no gradient background — clean flat styling.
- [x] AC 3: Given the dashboard is loaded in dark theme, when the user views the sidebar brand text, then "OptiPlan" is legible against the dark sidebar background (`#0F1328`).
- [x] AC 4: Given the dashboard is loaded in light theme, when the user views the sidebar brand text, then "OptiPlan" is legible against the light sidebar background (`#FFFFFF`).
- [x] AC 5: Given the dashboard is loaded, when the user views the topbar, then "OptiPlan" is displayed as the primary title with "מרכז בקרה · הרחבת קו מטרו, גוש דן" as the subtitle.
- [x] AC 6: Given the sidebar brand area, when the logo image fails to load, then the alt text "OptiPlan logo" is displayed gracefully and the "OptiPlan" text still renders correctly.

## Additional Context

### Dependencies

- `user-data/LOGO-ICON.png` must exist and be a valid PNG image. Already confirmed present in the repo.
- No external dependencies or new libraries needed.

### Testing Strategy

- **Manual visual testing**: Open `index.html` in browser, verify each AC visually.
- **Dark theme**: Toggle theme to dark, verify sidebar brand text and topbar text are legible.
- **Light theme**: Toggle theme to light, verify sidebar brand text and topbar text are legible.
- **Favicon**: Check browser tab for logo icon.
- **Image broken path**: Temporarily rename the PNG to confirm graceful fallback (alt text visible, text still renders).

### Notes

- The logo PNG dimensions should ideally be square or near-square for clean rendering at 28x28px in the sidebar. If the aspect ratio is off, `object-fit: contain` will handle it.
- Favicon best practice would eventually include an `.ico` file and multiple sizes, but for this dashboard (internal tool, not public-facing) a single PNG is sufficient.
- The `<title>` tag already reads "OptiPlan — מרכז בקרה" and requires no changes.

## Review Notes

- Adversarial review completed
- Findings: 10 total, 6 fixed, 4 skipped
- Resolution approach: auto-fix
- Fixed: F3 (bidi isolation), F6 (decoding=async), F7 (aria-hidden on separator), F8 (lang=en on English text), F10 (empty alt on decorative logo img)
- Skipped (intentional per spec): F4 (redundant brand — by design), F5 (flat text styling — by design)
- Skipped (noise): F9 (relative path — matches project architecture)
- Deferred: F1 (oversized PNG — needs image processing outside scope), F2 (additional favicon formats — noted for future)
