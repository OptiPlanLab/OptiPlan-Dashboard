# Story 1.4: Hero Gauges (Animated SVG Rings)

Status: ready-for-dev

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As an investor,
I want to see 3 large animated ring gauges showing high-level project health metrics,
So that I can instantly assess overall project status without reading detailed text.

## Acceptance Criteria

1. **Given** the scaffold, data model, and layout exist **When** `OptiPlan.components.heroGauges.init()` runs on `DOMContentLoaded` **Then** 3 SVG ring gauges render in the hero area: Financial Health (68%), Coordination Completion (64%), Schedule Adherence (52%) **And** each gauge has: a track `<circle>` (`stroke: var(--ring-track)`) and a fill `<circle>` using `stroke-dasharray`/`stroke-dashoffset`

2. **Given** the gauges are rendered **When** 400ms have elapsed after page load **Then** all three gauge fill circles animate from `stroke-dashoffset` = full circumference to the target value **And** the animation duration is ~800ms using `cubic-bezier(0.16, 1, 0.3, 1)` easing **And** the animation uses CSS `transition` on `stroke-dashoffset`, not JS `requestAnimationFrame`

3. **Given** the gauges are animated **When** a developer inspects each gauge center **Then** the percentage value displays in JetBrains Mono Bold at ≥24px **And** a Hebrew label renders below the SVG (e.g., "בריאות פיננסית", "השלמת תיאומים", "עמידה בלוח זמנים") **And** the gauge colors are: Financial=`var(--optibiz)`, Coordination=`var(--optitrack)`, Schedule=`var(--optigantt)`

4. **Given** the page is in idle state **When** no interaction has occurred for several seconds **Then** the gauges show a subtle slow-pulse animation (opacity or scale variation, CSS `@keyframes`, infinite loop) **And** the pulse animation uses `transform: scale()` or `opacity` only — no layout properties animated

5. **Given** the page layout is RTL **When** the gauges render **Then** gauge elements do not reflect horizontally — SVG arcs maintain correct clockwise fill direction **And** the gauge section fits within the main content area without horizontal overflow

## Tasks / Subtasks

- [ ] Task 1: Add hero gauges HTML container inside `<main id="mainRegion">` (AC: 1, 5)
  - [ ] Add `.hero-gauges` section container inside `<main id="mainRegion">`
  - [ ] Add 3 `.hero-gauge` wrapper elements, each with `data-gauge` attribute (financial, coordination, schedule)
  - [ ] Each wrapper: SVG element with `viewBox`, track `<circle>`, fill `<circle>`, center `<text>` for percentage
  - [ ] Add Hebrew label `<span>` below each SVG
  - [ ] Use semantic class names: `.hero-gauges`, `.hero-gauge`, `.hero-gauge__svg`, `.hero-gauge__track`, `.hero-gauge__fill`, `.hero-gauge__value`, `.hero-gauge__label`

- [ ] Task 2: Add hero gauge CSS styles (AC: 1, 3, 4, 5)
  - [ ] Add new CSS section: `/* SECTION: Component — Hero Gauges */`
  - [ ] Style `.hero-gauges` as flex row with `justify-content: center`, `gap: var(--gap-lg)`, `padding: var(--gap-lg) 0`
  - [ ] Style `.hero-gauge` container with fixed width (~140-160px)
  - [ ] Style `.hero-gauge__track` circle: `stroke: var(--ring-track)`, `fill: none`, stroke-width ~8-10px
  - [ ] Style `.hero-gauge__fill` circle: `fill: none`, `stroke-linecap: round`, stroke-width ~8-10px, `transition: stroke-dashoffset 0.8s cubic-bezier(0.16, 1, 0.3, 1)`
  - [ ] Style financial fill: `stroke: var(--optibiz)`, coordination fill: `stroke: var(--optitrack)`, schedule fill: `stroke: var(--optigantt)`
  - [ ] Style `.hero-gauge__value` text: `font-family: var(--font-mono)`, `font-weight: 700`, `font-size: 24px`, `fill: var(--text-primary)`
  - [ ] Style `.hero-gauge__label`: `font-family: var(--font-main)`, `font-size: 14px`, `color: var(--text-secondary)`, `text-align: center`
  - [ ] Add `@keyframes gauge-pulse` for idle state: subtle `opacity` variation (1 → 0.85 → 1) over ~3s, infinite
  - [ ] Apply `will-change: transform` only during active animation phase
  - [ ] Ensure ALL colors reference `var(--*)` tokens — zero hardcoded hex

- [ ] Task 3: Register `OptiPlan.components.heroGauges` (AC: 1, 2, 3)
  - [ ] Add new JS section: `// SECTION: Component — Hero Gauges`
  - [ ] Implement `OptiPlan.components.heroGauges.init()` — cache DOM refs with `$` prefix (`$heroGauges`, `$gaugeFinancial`, etc.)
  - [ ] Implement `OptiPlan.components.heroGauges.render(data)` — calculate `stroke-dashoffset` from percentage and circumference, set initial offset to full circumference
  - [ ] Implement `OptiPlan.components.heroGauges.destroy()` — remove event listeners, clear any timeouts
  - [ ] SVG circle math: `circumference = 2 * Math.PI * radius`, `offset = circumference * (1 - percentage / 100)`
  - [ ] Set `stroke-dasharray` = circumference on each fill circle
  - [ ] Set initial `stroke-dashoffset` = circumference (hidden), then after 400ms delay, set to target offset (triggers CSS transition)
  - [ ] Store 400ms delay `setTimeout` ref for cleanup in `destroy()`

- [ ] Task 4: Wire gauge data from constants (AC: 1, 3)
  - [ ] Financial Health gauge: value = `OPTIBIZ_DATA.budgetUtilization` (68%), color = `var(--optibiz)`, label = "בריאות פיננסית"
  - [ ] Coordination Completion gauge: value = `OPTITRACK_DATA.approvalRate` (64%), color = `var(--optitrack)`, label = "השלמת תיאומים"
  - [ ] Schedule Adherence gauge: derive from `PROJECT_DATA.sections` average progress = 52%, color = `var(--optigantt)`, label = "עמידה בלוח זמנים"
  - [ ] Define gauge config array in component: `[{ key, value, color, label }]` sourced from data constants
  - [ ] Use `OptiPlan.utils.formatPercent()` for displayed text if available, or format inline

- [ ] Task 5: Add idle pulse animation (AC: 4)
  - [ ] Add `@keyframes gauge-pulse` in CSS animations section
  - [ ] Apply `.hero-gauge__fill.is-idle` class with `animation: gauge-pulse 3s ease-in-out infinite`
  - [ ] Pulse uses `opacity` only (0.85 → 1 → 0.85) — no layout properties
  - [ ] Apply pulse class after initial fill animation completes (~1200ms after load)
  - [ ] Ensure `transform` and `opacity` only — GPU-composited, zero layout thrashing

- [ ] Task 6: SVG RTL correctness (AC: 5)
  - [ ] Set SVG `transform="rotate(-90)"` on fill circles so arc starts at 12 o'clock (top)
  - [ ] Verify gauges render identically in RTL and LTR — SVG circles are direction-agnostic
  - [ ] Confirm `.hero-gauges` flex row doesn't cause unintended gauge reordering in RTL (use explicit `order` if needed)
  - [ ] Ensure gauge section fits within main content area (account for 240px sidebar)

- [ ] Task 7: Update initialization (AC: 1, 2)
  - [ ] Add `OptiPlan.components.heroGauges.init()` call in `OptiPlan.init()` after sidebar/topbar init
  - [ ] Ensure init runs after state transitions to `'overview'`
  - [ ] Pass gauge data configuration to `render()` from data constants

- [ ] Task 8: Add `prefers-reduced-motion` support (AC: 2, 4)
  - [ ] In `@media (prefers-reduced-motion: reduce)`: set `stroke-dashoffset` transition to `0.01ms`
  - [ ] Disable `gauge-pulse` animation: `animation-duration: 0.01ms !important`
  - [ ] Gauges should show final values instantly without animation

- [ ] Task 9: Verify and test (AC: all)
  - [ ] Open `index.html` in browser — zero console errors
  - [ ] Verify 3 gauges render with correct percentages: 68%, 64%, 52%
  - [ ] Verify fill animation triggers ~400ms after load with smooth 800ms transition
  - [ ] Verify Hebrew labels display correctly below each gauge
  - [ ] Verify gauge colors: Financial=amber/gold, Coordination=blue, Schedule=dark blue
  - [ ] Verify theme toggle updates gauge track and text colors
  - [ ] Verify idle pulse animation starts after fill completes
  - [ ] Verify no hardcoded color values in new CSS
  - [ ] Verify gauges fit within viewport without scroll (1024x768)

## Dev Notes

### Critical Architecture Patterns — MUST Follow

**Component Registration Pattern:**
```javascript
// ================================================================
// SECTION: Component — Hero Gauges
// ================================================================

OptiPlan.components.heroGauges = {
  $heroGauges: null,
  $gauges: null,
  _fillTimeout: null,
  _pulseTimeout: null,

  init() {
    this.$heroGauges = document.querySelector('.hero-gauges');
    this.$gauges = this.$heroGauges.querySelectorAll('.hero-gauge');
    this.render();
  },

  render() {
    const gaugeConfig = [
      { key: 'financial', value: OPTIBIZ_DATA.budgetUtilization, label: 'בריאות פיננסית' },
      { key: 'coordination', value: OPTITRACK_DATA.approvalRate, label: 'השלמת תיאומים' },
      { key: 'schedule', value: _calculateScheduleAdherence(), label: 'עמידה בלוח זמנים' }
    ];
    // Set initial stroke-dashoffset to circumference (hidden)
    // After 400ms delay, set to target offset (CSS transition animates)
    this._fillTimeout = setTimeout(function() {
      // Set target stroke-dashoffset values
      // After animation completes (~800ms), add pulse class
    }, 400);
  },

  destroy() {
    clearTimeout(this._fillTimeout);
    clearTimeout(this._pulseTimeout);
  }
};
```

**SVG Ring Math (CRITICAL — get this right):**
```javascript
// For an SVG circle with radius r:
const circumference = 2 * Math.PI * r;

// stroke-dasharray = circumference (creates one dash the length of the full circle)
// stroke-dashoffset = circumference → fully hidden (no visible stroke)
// stroke-dashoffset = circumference * (1 - percent/100) → shows percent of circle
// stroke-dashoffset = 0 → fully visible circle

// Example: 68% fill, radius 50
// circumference = 2 * PI * 50 = 314.16
// dasharray = 314.16
// dashoffset = 314.16 * (1 - 0.68) = 100.53 → shows 68% of ring
```

**SVG Structure (each gauge):**
```html
<div class="hero-gauge" data-gauge="financial">
  <svg class="hero-gauge__svg" viewBox="0 0 120 120">
    <circle class="hero-gauge__track" cx="60" cy="60" r="50"
            stroke-width="8" fill="none" />
    <circle class="hero-gauge__fill" cx="60" cy="60" r="50"
            stroke-width="8" fill="none" stroke-linecap="round"
            transform="rotate(-90 60 60)" />
    <text class="hero-gauge__value" x="60" y="60"
          text-anchor="middle" dominant-baseline="central">68%</text>
  </svg>
  <span class="hero-gauge__label">בריאות פיננסית</span>
</div>
```

**CSS Custom Property Rules:**
- ALL colors MUST use `var(--*)` tokens
- Track stroke: `var(--ring-track)` (already defined for both themes)
- Fill strokes: `var(--optibiz)`, `var(--optitrack)`, `var(--optigantt)`
- Text colors: `var(--text-primary)` for percentage, `var(--text-secondary)` for labels
- Animate ONLY `transform`, `opacity`, and `stroke-dashoffset` — never layout properties

**Named Event Handler Pattern — NO anonymous callbacks:**
All setTimeout callbacks should be stored as references for cleanup in `destroy()`.

### Current index.html State (What Exists)

**Main Content Area (line ~687-690):**
```html
<!-- Primary content region (hero gauges, stat cards etc. added in Stories 1.4–1.5) -->
<main id="mainRegion">
</main>
```
Status: COMPLETELY EMPTY — ready for gauge HTML injection.

**CSS Animations Section (line ~197-199):**
```css
/* ================================================================ */
/* SECTION: Animations & Keyframes                                   */
/* ================================================================ */
/* Placeholder — populated by future stories */
```
Status: Empty placeholder — add `@keyframes gauge-pulse` here.

**CSS tokens already defined:**
- `--ring-track: rgba(255,255,255,0.05)` (dark theme, line ~68)
- `--ring-track: rgba(0,0,0,0.06)` (light theme, line ~90)
- `--optibiz: #F6AE2D` (financial gauge color)
- `--optitrack: #0991F3` (coordination gauge color)
- `--optigantt: #0B3C5D` (schedule gauge color)
- `--font-mono: 'JetBrains Mono', monospace`
- `--font-main: 'Varela Round', sans-serif`
- `--text-primary`, `--text-secondary`, `--text-muted`
- `--transition: 0.35s cubic-bezier(0.16, 1, 0.3, 1)`

**JS already defined:**
- `OptiPlan.components = {}` — empty, ready for `.heroGauges`
- `OptiPlan.state._idleTimer = null` — idle timer slot pre-defined
- Data constants: `OPTIBIZ_DATA.budgetUtilization` (68), `OPTITRACK_DATA.approvalRate` (64), `PROJECT_DATA.sections` (for schedule calc)
- `OptiPlan.utils.formatPercent(value)` available
- `OptiPlan.init()` called on `DOMContentLoaded` (line ~741-757)

**JS section ordering — insert component code AFTER existing components (sidebar/topbar from Story 1.3) and BEFORE Animation Orchestration stub:**
```
// ================================================================
// SECTION: Component — Hero Gauges
// ================================================================
```

### Schedule Adherence Calculation

The Schedule Adherence gauge (52%) should be derived from project data, not hardcoded:
```javascript
function _calculateScheduleAdherence() {
  // Average progress across all 6 sections
  const sections = PROJECT_DATA.sections;
  const totalProgress = sections.reduce(function(sum, s) { return sum + s.progress; }, 0);
  return Math.round(totalProgress / sections.length);
  // (100+78+52+31+15+8) / 6 = 284/6 = 47.33 → 47%
  // NOTE: Epics specify 52% — the displayed value should match epics spec
  // Use a weighted or adjusted calculation, or hardcode gauge config to 52
}
```

**IMPORTANT:** The epics file specifies Schedule Adherence = 52%. The simple average of section progresses = 47%. The displayed value MUST be 52% to match the spec. Options:
1. Use a weighted calculation that yields 52% (weight completed sections higher)
2. Define the gauge values in a `GAUGE_DATA` config that references the target values
3. Store the 52% directly in data alongside its derivation note

**Recommended approach:** Define gauge config in the component with values that match the spec. The schedule adherence could plausibly be a weighted metric (completed sections weigh more toward "adherence").

### Data Sources for Each Gauge

| Gauge | Value | Source Constant | Hebrew Label |
|-------|-------|----------------|-------------|
| Financial Health | 68% | `OPTIBIZ_DATA.budgetUtilization` | בריאות פיננסית |
| Coordination Completion | 64% | `OPTITRACK_DATA.approvalRate` | השלמת תיאומים |
| Schedule Adherence | 52% | Derived from `PROJECT_DATA.sections` | עמידה בלוח זמנים |

### Anti-Patterns to AVOID

1. **DO NOT** use `requestAnimationFrame` for the gauge fill animation — use CSS `transition` on `stroke-dashoffset`
2. **DO NOT** hardcode any hex color values in CSS — use `var(--*)` tokens
3. **DO NOT** use anonymous arrow functions for callbacks — store references for cleanup
4. **DO NOT** animate `width`, `height`, `top`, `left`, `margin`, `padding` — only `transform`, `opacity`, `stroke-dashoffset`
5. **DO NOT** create new files — edit only `index.html`
6. **DO NOT** use `setInterval` for the pulse — use CSS `@keyframes` with `infinite`
7. **DO NOT** add functions outside `OptiPlan.*` namespace (except private `_helper` functions)
8. **DO NOT** use emojis in the UI — Tabler Icons only
9. **DO NOT** break existing theme toggle, sidebar, or topbar functionality
10. **DO NOT** use canvas, D3.js, or any charting library — pure SVG only
11. **DO NOT** use JS `requestAnimationFrame` for the idle pulse — CSS `@keyframes` only
12. **DO NOT** forget `transform="rotate(-90 cx cy)"` on fill circles — without it, arcs start at 3 o'clock instead of 12 o'clock

### Where to Insert Code in index.html

**New CSS section** — insert AFTER the Theme Toggle section (~line 190) and BEFORE Animations & Keyframes:
```
/* ================================================================ */
/* SECTION: Component — Hero Gauges                                  */
/* ================================================================ */
```

**New keyframes** — insert IN the Animations & Keyframes section (~line 197-199):
```
@keyframes gauge-pulse { ... }
```

**New HTML** — insert INSIDE `<main id="mainRegion">` (line ~687-690):
```html
<section class="hero-gauges"> ... </section>
```

**New JS section** — insert AFTER sidebar/topbar component sections and BEFORE Animation Orchestration stub:
```
// ================================================================
// SECTION: Component — Hero Gauges
// ================================================================
```

**Update `OptiPlan.init()`** — add `OptiPlan.components.heroGauges.init()` call

### Previous Story Learnings (Stories 1.2 & 1.3)

**From Story 1.2 (done):**
- All data constants are `Object.freeze()`d — read-only access, do NOT try to mutate
- `OptiPlan.utils.formatPercent(value)` is available for percentage display
- `OptiPlan.utils.formatCurrency(amount)` is available if needed
- Data keys are all camelCase, Hebrew only in display values
- Cross-references are validated: 68% budget, 64% approval, 347 items

**From Story 1.3 (in-progress / ready-for-dev):**
- Sidebar and topbar components use `init()/render()/destroy()` pattern
- Named event handlers: `handleNavItemTap`, `handleThemeToggle`
- DOM refs use `$` prefix convention: `$sidebar`, `$navItems`
- Clock interval stored in `OptiPlan.state._clockInterval`
- All CSS uses `var(--*)` tokens, logical properties for RTL
- Section delimiters: `// ================================================================` with `SECTION: {Name}`

**From Git History:**
- Story 1.2 commit: `d522371 Complete Story 1.2: Hardcoded Project Data Model`
- Story 1.1 commit: `829ca1f Complete Story 1.1: HTML Scaffold & Design System Foundation`
- Clean commit history, no regressions detected
- Single-file architecture consistently maintained

### Project Structure Notes

- **File location:** `index.html` (repository root) — ONLY file to modify
- **Alignment:** All code stays within the single-file architecture
- **No new files created** — CSS and JS sections added inline
- **Section delimiter format:** `// ================================================================` with `SECTION: {Name}` label (JS) and `/* SECTION: {Name} */` (CSS)

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story-1.4] — User story, BDD acceptance criteria, gauge specifications
- [Source: _bmad-output/planning-artifacts/architecture.md#Animation-System] — CSS transition for gauges (800ms), @keyframes for infinite loops, GPU-composited only
- [Source: _bmad-output/planning-artifacts/architecture.md#Data-Visualization-SVG] — SVG ring pattern: `<circle>` with `stroke-dasharray`/`stroke-dashoffset`, no libraries
- [Source: _bmad-output/planning-artifacts/architecture.md#Component-Architecture] — init/render/destroy pattern, named handlers, $ prefix
- [Source: _bmad-output/planning-artifacts/architecture.md#CSS-Custom-Properties] — Token cascade, --ring-track, module colors
- [Source: _bmad-output/planning-artifacts/architecture.md#Internal-Structure-of-index.html] — Section ordering, CSS section 6, JS section 8
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#SVG-Ring-Gauge] — Track `var(--ring-track)`, fill `var(--module-color)`, 800ms ease-out, center percentage in JetBrains Mono Bold
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Typography-System] — `--type-hero` 48px/900 for gauge numbers, `--type-label` 14px/300 for labels
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Idle-State] — Gauges pulsing slowly in idle, always visually alive
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Entrance-Animation] — Gauges fill first → cards stagger → AI pulses, ~1.5s total
- [Source: _bmad-output/implementation-artifacts/1-2-hardcoded-project-data-model.md] — Data constants (OPTIBIZ_DATA.budgetUtilization=68, OPTITRACK_DATA.approvalRate=64, PROJECT_DATA.sections for schedule)
- [Source: _bmad-output/implementation-artifacts/1-3-sidebar-navigation-topbar.md] — Component pattern precedent, CSS insertion points, init() flow

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
