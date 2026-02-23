# Story 1.3: Sidebar Navigation & Topbar

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As an investor,
I want to see an OptiPlan branded sidebar and topbar when the dashboard loads,
So that I immediately understand this is a professional, authoritative infrastructure platform.

## Acceptance Criteria

1. **Given** the scaffold and data model exist **When** `OptiPlan.components.sidebar.init()` and `OptiPlan.components.topbar.init()` are called on `DOMContentLoaded` **Then** the sidebar renders at 240px width with: brand badge pill ("OPTIPLAN" gradient), user avatar (initials "בע", role "מנהל פרויקט"), 5 module nav items each with Tabler icon and colored module dot, "כלים" section (Opium AI, section map, reports), "מערכת" section (settings), and footer row

2. **Given** the sidebar is rendered **When** a developer inspects the sidebar footer **Then** it shows a pulsing green dot (CSS `@keyframes pulse`, infinite), "API 18ms", and version string "v2.4.1" in JetBrains Mono **And** the green dot uses `box-shadow: 0 0 6px rgba(34,197,94,0.5)` for the glow effect

3. **Given** the topbar is rendered **When** the page loads **Then** the topbar shows "מרכז בקרה" as the page title and "הרחבת קו מטרו, גוש דן" as subtitle **And** a live clock displays current time in HH:MM format (JetBrains Mono), updating every 30 seconds via a single `setInterval` reference **And** a cosmetic search input and bell icon are visible

4. **Given** the sidebar nav items are rendered **When** "לוח בקרה" (Dashboard) nav item is in its default active state **Then** it shows `border-inline-start: 3px solid var(--optitrack)` (RTL logical property) and `background: var(--sidebar-active)` **And** all other nav items show no border and secondary text color

5. **Given** a nav item is tapped **When** the named `handleNavItemTap` function fires **Then** the tapped item becomes active (border + highlight), all others revert to default state **And** no anonymous arrow functions are used as event handlers anywhere in the sidebar or topbar components

6. **Given** the sidebar and topbar are fully rendered **When** the page is inspected **Then** zero hardcoded color values appear in sidebar or topbar CSS — all colors reference `var(--*)` tokens

## Tasks / Subtasks

- [x] Task 1: Add sidebar HTML content (AC: 1, 6)
  - [x] Add brand badge pill element inside `.sidebar` (before theme toggle)
  - [x] Add user avatar section with initials "בע" and role text
  - [x] Add nav section "מודולים" with 5 module items: לוח בקרה (ti-dashboard), OptiTrack (ti-chart-dots-3), OptiBiz (ti-chart-bar), OptiRisk (ti-shield-check), OptiDocs (ti-file-text), OptiGantt (ti-calendar-event)
  - [x] Add nav section "כלים" with items: Opium AI (ti-robot), מפת קטעים (ti-map), דוחות (ti-report)
  - [x] Add nav section "מערכת" with item: הגדרות (ti-settings)
  - [x] Add sidebar footer with pulsing dot, API latency text, version string
  - [x] Move existing theme toggle to correct position within sidebar layout
  - [x] Each nav item: `data-module` attribute, colored dot element, `touch-action: manipulation`

- [x] Task 2: Add sidebar CSS styles (AC: 1, 2, 4, 6)
  - [x] Style `.sidebar__brand` badge pill with gradient background
  - [x] Style `.sidebar__avatar` with initials circle and role text
  - [x] Style `.sidebar__nav` section with nav groups and dividers
  - [x] Style `.sidebar__nav-item` with 44px min-height, padding, icon + label + dot layout
  - [x] Style `.sidebar__nav-item--active` with `border-inline-start: 3px solid var(--optitrack)` and `background: var(--sidebar-active)`
  - [x] Style `.sidebar__dot` with module-specific `background: var(--module-color)`
  - [x] Style `.sidebar__section-label` as uppercase 8px muted text (JetBrains Mono)
  - [x] Style `.sidebar__footer` with pulsing green dot, API text, version string
  - [x] Add `@keyframes sidebar-pulse` for green dot (scale + opacity + box-shadow cycle)
  - [x] Ensure ALL colors reference `var(--*)` tokens — zero hardcoded hex values

- [x] Task 3: Add topbar HTML content (AC: 3, 6)
  - [x] Add page title "מרכז בקרה" (Varela Round bold)
  - [x] Add subtitle "הרחבת קו מטרו, גוש דן" (secondary text)
  - [x] Add clock element with `id="topbarClock"` (JetBrains Mono)
  - [x] Add cosmetic search input with `ti-search` icon (non-functional)
  - [x] Add bell notification icon `ti-bell` with badge dot

- [x] Task 4: Add topbar CSS styles (AC: 3, 6)
  - [x] Style `.topbar__title` and `.topbar__subtitle`
  - [x] Style `.topbar__clock` in JetBrains Mono
  - [x] Style `.topbar__search` as cosmetic input (RTL-aware)
  - [x] Style `.topbar__bell` with notification badge dot
  - [x] Ensure flex layout with RTL logical properties

- [x] Task 5: Register `OptiPlan.components.sidebar` (AC: 1, 5)
  - [x] Add new JS section: `// SECTION: Component — Sidebar`
  - [x] Implement `OptiPlan.components.sidebar.init()` — cache DOM refs with `$` prefix
  - [x] Implement `OptiPlan.components.sidebar.render()` — set active state on "לוח בקרה"
  - [x] Implement `OptiPlan.components.sidebar.destroy()` — remove all event listeners
  - [x] Implement named `handleNavItemTap(e)` — update active state, no anonymous callbacks
  - [x] Register touchstart/click listeners with `{ passive: false }` on nav items
  - [x] Use `touch-action: manipulation` CSS on all nav items

- [x] Task 6: Register `OptiPlan.components.topbar` (AC: 3)
  - [x] Add new JS section: `// SECTION: Component — Topbar`
  - [x] Implement `OptiPlan.components.topbar.init()` — cache DOM refs, start clock
  - [x] Implement clock update: `setInterval` at 30000ms, store ref in `OptiPlan.state._clockInterval`
  - [x] Implement `OptiPlan.components.topbar.destroy()` — clear interval, remove listeners
  - [x] Format clock as HH:MM using `Intl.DateTimeFormat('he-IL', { hour: '2-digit', minute: '2-digit', hour12: false })`

- [x] Task 7: Update initialization (AC: 1, 3)
  - [x] Add `OptiPlan.components.sidebar.init()` call in `OptiPlan.init()`
  - [x] Add `OptiPlan.components.topbar.init()` call in `OptiPlan.init()`
  - [x] Transition state to `overview` after init completes

- [x] Task 8: Verify and test (AC: all)
  - [x] Open `index.html` in browser — zero console errors
  - [x] Verify sidebar renders all sections with correct icons and Hebrew text
  - [x] Verify topbar shows title, subtitle, and ticking clock
  - [x] Verify "לוח בקרה" is active by default with blue border
  - [x] Verify tapping another nav item changes active state
  - [x] Verify theme toggle (dark/light) still works and all sidebar/topbar colors update
  - [x] Verify zero hardcoded color values in new CSS

## Dev Notes

### Critical Architecture Patterns — MUST Follow

**Component Registration Pattern:**
```javascript
// ================================================================
// SECTION: Component — Sidebar
// ================================================================

OptiPlan.components.sidebar = {
  $sidebar: null,
  $navItems: null,

  init() {
    this.$sidebar = document.querySelector('.sidebar');
    this.$navItems = this.$sidebar.querySelectorAll('.sidebar__nav-item');
    this.$navItems.forEach(function($item) {
      $item.addEventListener('touchstart', handleNavItemTap, { passive: false });
      $item.addEventListener('click', handleNavItemTap);
    });
    this.render();
  },

  render() {
    // Set "לוח בקרה" as active by default
  },

  destroy() {
    this.$navItems.forEach(function($item) {
      $item.removeEventListener('touchstart', handleNavItemTap);
      $item.removeEventListener('click', handleNavItemTap);
    });
  }
};
```

**Named Event Handler Pattern — NO anonymous callbacks:**
```javascript
function handleNavItemTap(e) {
  e.preventDefault();
  const $tapped = e.currentTarget;
  // Remove active from all items
  // Add active to tapped item
}
```

**CSS Custom Property Rules:**
- ALL colors MUST use `var(--*)` tokens
- Use `border-inline-start` (not `border-left`) for RTL
- Use `inset-inline-start`/`inset-inline-end` for RTL positioning
- Module dots use `background: var(--optitrack)`, `var(--optibiz)`, etc.

**RTL Logical Properties Required:**
- `margin-inline-start` instead of `margin-right`
- `padding-inline-start` instead of `padding-right`
- `border-inline-start` instead of `border-right`
- Flexbox `row` auto-reverses in RTL — sidebar items already flow correctly

### Current index.html State (What Exists)

**Sidebar (lines ~313-323):**
- Container `<aside class="sidebar">` exists at 240px width, positioned right (RTL)
- Contains ONLY theme toggle (`#themeToggle`) — move it to correct position in sidebar layout
- Comment: `<!-- Additional sidebar content added in Story 1.3 -->`

**Topbar (lines ~327-330):**
- Container `<header class="topbar">` exists at 58px height, sticky
- COMPLETELY EMPTY — comment: `<!-- Topbar content added in Story 1.3 -->`

**CSS already defined:**
- `.sidebar` layout: `position: fixed`, `inset-inline-end: 0`, `width: 240px`, `height: 100vh`, `background: var(--bg-sidebar)`, `border-inline-start: 1px solid var(--border)`
- `.topbar` layout: `height: 58px`, `position: sticky`, `top: 0`, `background: var(--bg-sidebar)`, `border-bottom: 1px solid var(--border)`
- Theme toggle styles: fully implemented (`.theme-toggle`, `__track`, `__knob`, `__label`)
- All design tokens exist on `:root` and `[data-theme]`

**JS already defined:**
- `OptiPlan.components = {}` — empty, ready for `.sidebar` and `.topbar`
- `OptiPlan.state._clockInterval = null` — clock interval slot pre-defined
- Theme toggle handler: `handleThemeToggle` already registered in init
- `OptiPlan.init()` called on `DOMContentLoaded` (lines ~645-665)

**CSS tokens available (use these, don't create new ones unless truly needed):**
- `--bg-sidebar`, `--bg-card`, `--bg-input`, `--bg-body`
- `--text-primary`, `--text-secondary`, `--text-muted`
- `--border`, `--border-hover`
- `--sidebar-active` (already defined for nav active background)
- `--optitrack`, `--optibiz`, `--optirisk`, `--optidocs`, `--optigantt`
- `--radius`, `--radius-sm`, `--gap`, `--gap-sm`, `--gap-lg`
- `--transition`, `--font-main`, `--font-mono`
- `--shadow-card`, `--shadow-card-hover`

### Sidebar Content Specification

**Brand Badge Pill:**
- Text: "OPTIPLAN"
- Background: gradient using `var(--optitrack)` to `var(--optibiz)` or similar brand gradient
- Font: JetBrains Mono, uppercase, small (10-11px), letter-spacing
- Pill shape with `border-radius: var(--radius-sm)`
- Centered in sidebar, top area

**User Avatar:**
- Initials circle: "בע" (Hebrew initials for BenAkiva)
- Role text: "מנהל פרויקט" (Project Manager)
- Avatar circle: ~36px, `background: var(--optitrack)`, white text, Varela Round bold
- Name/role below or beside avatar

**Navigation Items (5 modules + sections):**

| Order | Hebrew Label | Tabler Icon | Module Color Token |
|-------|-------------|-------------|-------------------|
| 1 | לוח בקרה | ti-dashboard | `--optitrack` (active border) |
| 2 | OptiTrack — תיאומים | ti-chart-dots-3 | `--optitrack` |
| 3 | OptiBiz — פיננסי | ti-chart-bar | `--optibiz` |
| 4 | OptiRisk — סיכונים | ti-shield-check | `--optirisk` |
| 5 | OptiDocs — מסמכים | ti-file-text | `--optidocs` |
| 6 | OptiGantt — לוח זמנים | ti-calendar-event | `--optigantt` |

**"כלים" (Tools) section:**

| Hebrew Label | Tabler Icon |
|-------------|-------------|
| Opium AI | ti-robot |
| מפת קטעים | ti-map |
| דוחות | ti-report |

**"מערכת" (System) section:**

| Hebrew Label | Tabler Icon |
|-------------|-------------|
| הגדרות | ti-settings |

**Sidebar Footer:**
- Pulsing green dot: `background: #22C55E` via `var(--green)` if defined, or use `rgb(34,197,94)` referenced through a CSS variable
- API latency: "API 18ms" in JetBrains Mono, muted color
- Version: "v2.4.1" in JetBrains Mono, muted color
- Pulse animation: `@keyframes sidebar-pulse` with `box-shadow: 0 0 6px rgba(34,197,94,0.5)` glow

### Topbar Content Specification

**Layout (flex row, RTL):**
- Right side (in RTL = visual left): Title + subtitle block
- Left side (in RTL = visual right): Clock, search, bell

**Title:** "מרכז בקרה" — Varela Round, bold, `var(--text-primary)`, ~18px
**Subtitle:** "הרחבת קו מטרו, גוש דן" — Varela Round, `var(--text-secondary)`, ~13px

**Clock:** HH:MM format, JetBrains Mono, `var(--text-muted)`, updates every 30s
- Use `Intl.DateTimeFormat('he-IL', { hour: '2-digit', minute: '2-digit', hour12: false })`
- Store interval ref: `OptiPlan.state._clockInterval = setInterval(updateClock, 30000)`

**Search:** Cosmetic input with `ti-search` icon, `background: var(--bg-input)`, `border: 1px solid var(--border)`, non-functional
**Bell:** `ti-bell` icon with small badge dot (cosmetic notification indicator)

### Anti-Patterns to AVOID

1. **DO NOT** use anonymous arrow functions for event listeners — always named functions
2. **DO NOT** hardcode any hex color values in CSS — use `var(--*)` tokens
3. **DO NOT** use `margin-left`, `margin-right`, `padding-left`, `padding-right` — use logical properties (`-inline-start`, `-inline-end`)
4. **DO NOT** use `border-left` or `border-right` — use `border-inline-start` / `border-inline-end`
5. **DO NOT** create new files — edit only `index.html`
6. **DO NOT** use `setInterval` without storing the reference for cleanup
7. **DO NOT** add global functions outside `OptiPlan.*` namespace
8. **DO NOT** use emojis in the UI — Tabler Icons only
9. **DO NOT** animate `width`, `height`, `top`, `left`, `margin`, `padding` — only `transform` and `opacity`
10. **DO NOT** break existing theme toggle functionality

### Where to Insert Code in index.html

**New CSS sections** — insert AFTER the existing Theme Toggle section (~line 220) and BEFORE Animations & Keyframes:
```
/* ================================================================ */
/* SECTION: Component — Sidebar Content                              */
/* ================================================================ */

/* ================================================================ */
/* SECTION: Component — Topbar Content                               */
/* ================================================================ */
```

**New HTML** — modify existing sidebar `<aside>` and topbar `<header>` containers (lines ~313-330)

**New JS sections** — insert AFTER UI Reflection Layer section (~line 613) and BEFORE Theme Toggle Handler:
```
// ================================================================
// SECTION: Component — Sidebar
// ================================================================

// ================================================================
// SECTION: Component — Topbar
// ================================================================
```

**Update `OptiPlan.init()`** — add sidebar and topbar init calls

### Previous Story Learnings (Story 1.2)

- Data constants are fully populated and working
- `OptiPlan.utils.formatCurrency()` is available for currency formatting
- The index.html has well-defined section delimiters — follow the same pattern
- Story 1.2 completed cleanly with no regressions — the scaffold is stable
- Theme toggle functionality works — do not break it when restructuring sidebar

### Project Structure Notes

- **File location:** `index.html` (repository root) — ONLY file to modify
- **Alignment:** All code stays within the single-file architecture
- **No new files created** — CSS and JS sections added inline
- **Section delimiter format:** `// ================================================================` with `SECTION: {Name}` label (JS) and `/* SECTION: {Name} */` (CSS)

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story-1.3] — User story, BDD acceptance criteria, sidebar/topbar specifications
- [Source: _bmad-output/planning-artifacts/architecture.md#Implementation-Patterns] — Component pattern (init/render/destroy), naming conventions, event handler rules
- [Source: _bmad-output/planning-artifacts/architecture.md#Internal-Structure-of-index.html] — CSS/JS section ordering, component placement
- [Source: _bmad-output/planning-artifacts/architecture.md#Naming-Patterns] — CSS BEM-like classes, JS $ prefix, named handlers
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Custom-Components] — Sidebar nav spec (240px, colored dots, footer), Topbar spec (58px, clock, search)
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Touch-Interaction-Patterns] — Touch targets 44px min, touch-action: manipulation
- [Source: _bmad-output/implementation-artifacts/1-2-hardcoded-project-data-model.md] — Previous story context, existing code structure

## Dev Agent Record

### Agent Model Used

Claude Opus 4.6

### Debug Log References

No blocking issues encountered during implementation.

### Completion Notes List

- Implemented complete sidebar with brand badge pill (OPTIPLAN gradient), user avatar (initials "בע", role "מנהל פרויקט"), 6 navigation items across 3 sections (מודולים, כלים, מערכת), and footer with pulsing status dot
- Added `--status-online` and `--status-online-glow` CSS variables to `:root` for the green status dot (avoids hardcoded hex in component CSS)
- Implemented `@keyframes sidebar-pulse` animation using only `transform`, `opacity`, and `box-shadow` (per animation anti-patterns)
- Built topbar with title/subtitle block, live HH:MM clock (Intl.DateTimeFormat, 30s interval stored in `OptiPlan.state._clockInterval`), cosmetic search, and bell notification icon
- Registered `OptiPlan.components.sidebar` with `init()`, `render()`, `destroy()` methods following the component pattern; named `handleNavItemTap` handler with zero anonymous callbacks
- Registered `OptiPlan.components.topbar` with `init()`, `render()`, `destroy()` methods; named `updateTopbarClock` function
- All CSS uses `var(--*)` tokens exclusively; all positioning uses RTL logical properties (`border-inline-start`, `inset-inline-end`, `margin-inline-start`)
- Theme toggle moved to correct position in sidebar layout (between nav and footer); existing functionality preserved
- No new files created; all changes in `index.html` only
- No automated test framework configured for this project (single HTML file architecture); verification is browser-based per Task 8 subtasks

### File List

- `index.html` — Modified: added sidebar content (HTML, CSS, JS), topbar content (HTML, CSS, JS), `@keyframes sidebar-pulse` animation, `--status-online` CSS variables, component init calls

## Senior Developer Review (AI)

**Reviewer:** BenAkiva (via Claude Opus 4.6)
**Date:** 2026-02-23

**Result: APPROVED with fixes applied**

All 6 Acceptance Criteria verified as implemented. 0 HIGH, 3 MEDIUM, 6 LOW issues found.

**Fixed (3 MEDIUM + 2 LOW):**
- M1: Added missing `render()` method to topbar component (architecture triad compliance)
- M3: Added `aria-current="page"` attribute management on nav active state changes
- L1: Changed `var` to `const` in `handleNavItemTap` for consistency with codebase
- L2: Cached `Intl.DateTimeFormat` as module-level `_clockFormatter` constant

**Deferred (1 MEDIUM):**
- M2: `handleNavItemTap` and `updateTopbarClock` at global scope (violates "no global functions outside OptiPlan.*" rule) — pre-existing pattern from Story 1.1 (`handleThemeToggle`); requires cross-story architectural refactoring to resolve

**Accepted as-is (4 LOW):**
- L3: AC1 says "5 module nav items" but 6 rendered (story doc error; implementation matches detailed spec)
- L4: Inline `style` on colored dots (uses var() tokens, satisfies AC6)
- L5: Redundant active class in HTML + render() (harmless, classList.add is idempotent)
- L6: Bell `cursor: pointer` on cosmetic element (intentional affordance for future story)

## Change Log

- 2026-02-23: Story 1.3 implementation complete — sidebar navigation with brand badge, user avatar, module nav items with colored dots, tools/system sections, pulsing status footer; topbar with title, subtitle, live clock, cosmetic search, bell icon; all using CSS custom properties and RTL logical properties
- 2026-02-23: Code review — 4 fixes applied (topbar render method, ARIA nav state, var→const, cached DateTimeFormat); 1 MEDIUM deferred (global-scope handlers); status → done
