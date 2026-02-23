# Story 2.7: OptiGantt Module — Schedule Timeline

Status: review

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As an investor,
I want to see the full OptiGantt module with a Gantt timeline showing all 6 project sections,
So that I can visualize the project schedule and understand current progress against the original plan.

## Acceptance Criteria

1. **AC1 — Gantt Timeline Rendering:**
   **Given** the module overlay is open for OptiGantt
   **When** `OptiPlan.modules.optigantt.render(OPTIGANTT_DATA)` is called
   **Then** a horizontal Gantt timeline renders with 6 rows (one per section A1–A6, Hebrew labels on y-axis) and monthly columns on the x-axis

2. **AC2 — Bar and Milestone Rendering:**
   **Given** the Gantt timeline renders
   **When** a developer inspects the bars
   **Then** planned duration bars render in `var(--optigantt)` at 40% opacity
   **And** actual progress bars render in `var(--optigantt)` at full opacity, sized to the actual completion date or current progress
   **And** diamond milestone markers (`<polygon>`) render at key project dates from `OPTIGANTT_DATA.milestones`

3. **AC3 — Today Indicator:**
   **Given** the Gantt timeline renders
   **When** a developer inspects the today indicator
   **Then** a vertical dashed line (`<path>` with `stroke-dasharray`) marks today's date position on the timeline
   **And** the today position is calculated from `new Date()` at render time — not hardcoded

4. **AC4 — SVG Implementation & Pattern Compliance:**
   **Given** the timeline is rendered
   **When** a developer inspects the SVG implementation
   **Then** bars use `<rect>` elements, the today line uses `<path>`, text labels use `<text>`, and the SVG has a `viewBox` for responsive scaling
   **And** no canvas, no chart libraries, no external dependencies
   **And** `OptiPlan.modules.optigantt` follows the `init()`, `render(data)`, `destroy()` pattern

## Tasks / Subtasks

- [x] **Task 1: Metrics Summary Row** (AC: 1, 4)
  - [x] 1.1 Create metrics container `.optigantt-metrics` with 4 metric cards
  - [x] 1.2 Card 1: Total sections count from `data.sections.length` (6)
  - [x] 1.3 Card 2: On-track sections (progress >= planned progress based on dates)
  - [x] 1.4 Card 3: Delayed sections count
  - [x] 1.5 Card 4: Milestones count from `data.milestones.length` (4)
  - [x] 1.6 Style with `color-mix(in srgb, var(--optigantt) 8%, var(--bg-card))` background, `1px solid color-mix(in srgb, var(--optigantt) 15%, transparent)` border
  - [x] 1.7 Values in `var(--font-mono)` Bold 22-24px, labels in `var(--font-main)` 12px

- [x] **Task 2: SVG Gantt Chart Container** (AC: 1, 4)
  - [x] 2.1 Create `<svg>` element with `viewBox` for responsive scaling within `.module-overlay__content`
  - [x] 2.2 Calculate time axis: find min `plannedStart` and max `plannedEnd` across all sections to determine full timeline range (2022-04 to 2027-12)
  - [x] 2.3 Define layout regions: y-axis labels area (~120px), timeline grid area, padding
  - [x] 2.4 Render monthly column grid lines using `<line>` elements with `stroke="var(--border)"` at `stroke-opacity="0.3"`
  - [x] 2.5 Render month/year labels on x-axis using `<text>` with `var(--font-main)` size 10, `fill="var(--text-muted)"`
  - [x] 2.6 Show year markers prominently, month labels at reduced intervals (every 3 or 6 months) to avoid clutter

- [x] **Task 3: Section Row Labels (Y-Axis)** (AC: 1)
  - [x] 3.1 Render 6 Hebrew section labels from `data.sections[].name` on y-axis using `<text>` elements
  - [x] 3.2 Position labels with `text-anchor="end"` (right-aligned for RTL context within SVG)
  - [x] 3.3 Use `var(--font-main)` size 11-12, `fill="var(--text-secondary)"`
  - [x] 3.4 Add section ID prefix (A1–A6) before each name
  - [x] 3.5 Add horizontal row separator lines between sections at ~4% opacity

- [x] **Task 4: Planned Duration Bars** (AC: 2)
  - [x] 4.1 For each section, render a `<rect>` from `plannedStart` to `plannedEnd` mapped to x-axis pixel positions
  - [x] 4.2 Apply `fill` using CSS class `.optigantt-bar--plan` with `fill: var(--optigantt); opacity: 0.4`
  - [x] 4.3 Set bar height ~20px with `rx="3"` for rounded corners using `var(--radius-sm)` scale
  - [x] 4.4 Vertically center bars within their row

- [x] **Task 5: Actual Progress Bars** (AC: 2)
  - [x] 5.1 For each section, render a `<rect>` from `actualStart` to `actualEnd` (or calculated current progress position if `actualEnd` is null)
  - [x] 5.2 For sections with `actualEnd: null`, calculate bar end position: `actualStart` + (`plannedEnd` - `plannedStart`) * (`progress` / 100)
  - [x] 5.3 Apply `fill` using CSS class `.optigantt-bar--actual` with `fill: var(--optigantt); opacity: 1`
  - [x] 5.4 Overlay on top of planned bar (same row, same y-position, shorter or equal width)
  - [x] 5.5 For section A1 (progress: 100%), actual bar extends to `actualEnd` date (2024-07-15), slightly past `plannedEnd` (2024-06-30) to show overrun

- [x] **Task 6: Diamond Milestone Markers** (AC: 2)
  - [x] 6.1 For each milestone in `data.milestones`, render a diamond shape using `<polygon>` element
  - [x] 6.2 Calculate x-position from milestone `date` mapped to timeline axis
  - [x] 6.3 Position vertically at the row of the milestone's `section`
  - [x] 6.4 Style: `fill="var(--optigantt)"` for completion milestones, differentiate by `type` (completion/review/launch) using opacity or accent
  - [x] 6.5 Add milestone label using `<text>` element near the diamond, Hebrew text from `data.milestones[].label`
  - [x] 6.6 Size diamonds ~10x10px, ensure they don't overlap bar elements

- [x] **Task 7: Today Indicator Line** (AC: 3)
  - [x] 7.1 Calculate today's x-position from `new Date()` mapped to timeline axis — never hardcode
  - [x] 7.2 Render vertical line using `<path>` with `stroke-dasharray="6,4"` for dashed appearance
  - [x] 7.3 Style: `stroke="var(--text-secondary)"` with `stroke-width="1.5"`
  - [x] 7.4 Line spans full chart height from top to bottom of the grid area
  - [x] 7.5 Add "היום" (Today) label above or below the line using `<text>` element

- [x] **Task 8: Module Namespace & Lifecycle** (AC: 4)
  - [x] 8.1 Replace placeholder stub at `OptiPlan.modules.optigantt` (line ~3582 in index.html)
  - [x] 8.2 Implement `init()`: cache `$content = document.querySelector('.module-overlay__content')`
  - [x] 8.3 Implement `render(data)`: build complete HTML string with SVG, set `$content.innerHTML`, trigger entrance animations
  - [x] 8.4 Implement `destroy()`: clear `_entranceTimeouts` via `forEach(clearTimeout)`, reset `_entranceTimeouts = []`, clear `$content.innerHTML`
  - [x] 8.5 Use `var` declarations (not `let`/`const` inside functions for Safari compat), named functions only — no anonymous callbacks

- [x] **Task 9: Entrance Animation** (AC: 4)
  - [x] 9.1 Define `.optigantt-section` base class: `opacity: 0; transform: translateY(12px); transition: opacity 300ms cubic-bezier(0.16, 1, 0.3, 1), transform 300ms cubic-bezier(0.16, 1, 0.3, 1)`
  - [x] 9.2 Define `.optigantt-section--visible`: `opacity: 1; transform: translateY(0)`
  - [x] 9.3 Stagger in `render()`: metrics row at 0ms, Gantt chart at 100ms, legend/milestones at 200ms
  - [x] 9.4 Store all `setTimeout` IDs in `_entranceTimeouts` array
  - [x] 9.5 Add `@media (prefers-reduced-motion: reduce)` override: disable transitions on `.optigantt-section`

- [x] **Task 10: CSS Section** (AC: 4)
  - [x] 10.1 Add `/* SECTION: Component — OptiGantt Module */` CSS block after OptiDocs section, before Ambient Background
  - [x] 10.2 `.optigantt-metrics` — flex row with gap, metrics cards
  - [x] 10.3 `.optigantt-metric` — card styling with color-mix background, border, radius
  - [x] 10.4 `.optigantt-metric__value` — JetBrains Mono Bold 22-24px, `var(--text-primary)`
  - [x] 10.5 `.optigantt-metric__label` — Varela Round 12px, `var(--text-secondary)`
  - [x] 10.6 `.optigantt-chart` — SVG container with `width: 100%` responsive
  - [x] 10.7 `.optigantt-bar--plan` — `fill: var(--optigantt); opacity: 0.4`
  - [x] 10.8 `.optigantt-bar--actual` — `fill: var(--optigantt)`
  - [x] 10.9 `.optigantt-milestone` — diamond polygon styling
  - [x] 10.10 `.optigantt-today` — dashed line styling
  - [x] 10.11 `.optigantt-section` / `.optigantt-section--visible` — entrance animation classes
  - [x] 10.12 Dark theme + light theme support via `var()` tokens only — zero hardcoded hex values
  - [x] 10.13 All values reference CSS custom properties

- [x] **Task 11: Verification** (AC: 1, 2, 3, 4)
  - [x] 11.1 Zero console errors on overlay open/close cycle
  - [x] 11.2 Gantt renders 6 section rows with Hebrew labels from `OPTIGANTT_DATA.sections`
  - [x] 11.3 Monthly x-axis grid visible with time labels
  - [x] 11.4 Planned bars render at 40% opacity in `var(--optigantt)` color
  - [x] 11.5 Actual bars render at full opacity, correctly sized by progress/dates
  - [x] 11.6 Section A1 shows completion (actual bar extends slightly past planned)
  - [x] 11.7 4 diamond milestone markers render at correct dates and sections
  - [x] 11.8 Today indicator dashed line appears at current date position
  - [x] 11.9 Today position changes if tested on different dates (not hardcoded)
  - [x] 11.10 SVG uses `viewBox` and scales responsively within overlay
  - [x] 11.11 All SVG elements are `<rect>`, `<path>`, `<text>`, `<polygon>`, `<line>` — no `<canvas>`
  - [x] 11.12 Metrics match stat card and card back KPI values
  - [x] 11.13 Theme toggle works (dark/light) on all new elements
  - [x] 11.14 Hebrew text renders correctly in both the SVG and metrics
  - [x] 11.15 Existing card flip, overlay open/close, sidebar nav unbroken
  - [x] 11.16 Touch targets meet 44px minimum where applicable
  - [x] 11.17 No hardcoded hex values in new CSS
  - [x] 11.18 Entrance animation stagger works (metrics -> chart -> legend)
  - [x] 11.19 `destroy()` properly cleans up (reopen overlay shows fresh render, no stale content)
  - [x] 11.20 Module overlay header shows OptiGantt icon, title, subtitle correctly

## Dev Notes

### Architecture Patterns & Constraints

- **Single File Architecture:** ALL code goes into `index.html`. No separate JS/CSS files. No new files.
- **Module Namespace Pattern:** `OptiPlan.modules.optigantt` with `init()`, `render(data)`, `destroy()` lifecycle. Replace the placeholder stub at line ~3582.
- **Data Source:** `OPTIGANTT_DATA` (already `Object.freeze()`'d at line ~2077). Contains 6 sections with planned/actual dates + progress, and 4 milestones.
- **SVG Only:** Pure SVG with `<rect>`, `<path>`, `<text>`, `<polygon>`, `<line>` elements. NO canvas, NO D3.js, NO Chart.js, NO external chart libraries.
- **CSS Custom Properties Only:** Zero hardcoded hex colors. All colors via `var(--optigantt)`, `var(--text-primary)`, `var(--border)`, etc.
- **Named Functions Only:** No anonymous arrow functions in event handlers. `var` declarations for function-scoped variables.
- **Animate Only `transform` and `opacity`:** GPU-composited properties only. `will-change: transform` during active animation.
- **Console Logging:** `console.warn('[OptiPlan OptiGantt] message')` for caught errors. Never `console.error`, never `alert()`.
- **Section Delimiters:** CSS: `/* SECTION: Component — OptiGantt Module */`, JS: `// MODULE: OptiGantt` with `// ================================================================` borders.

### Data Model Reference

```javascript
// OPTIGANTT_DATA.sections[] — 6 items:
{ id: 'a1', name: 'תל אביב מרכז – גבעתיים', plannedStart: '2022-04-01', plannedEnd: '2024-06-30', actualStart: '2022-04-01', actualEnd: '2024-07-15', progress: 100 }
{ id: 'a2', name: 'גבעתיים – רמת גן', plannedStart: '2023-01-01', plannedEnd: '2025-03-31', actualStart: '2023-01-15', actualEnd: null, progress: 78 }
{ id: 'a3', name: 'רמת גן – בני ברק', plannedStart: '2023-07-01', plannedEnd: '2025-12-31', actualStart: '2023-08-01', actualEnd: null, progress: 52 }
{ id: 'a4', name: 'בני ברק – פתח תקווה', plannedStart: '2024-01-01', plannedEnd: '2026-06-30', actualStart: '2024-03-01', actualEnd: null, progress: 31 }
{ id: 'a5', name: 'פתח תקווה מרכז – שרונה', plannedStart: '2024-07-01', plannedEnd: '2027-03-31', actualStart: '2024-10-01', actualEnd: null, progress: 15 }
{ id: 'a6', name: 'שרונה – גוש דן מזרח', plannedStart: '2025-01-01', plannedEnd: '2027-12-31', actualStart: '2025-03-01', actualEnd: null, progress: 8 }

// OPTIGANTT_DATA.milestones[] — 4 items:
{ date: '2024-07-15', label: 'השלמת קטע א1', section: 'a1', type: 'completion' }
{ date: '2025-06-30', label: 'בדיקות הפעלה — שלב א', section: 'a2', type: 'review' }
{ date: '2026-12-31', label: 'תחילת הפעלה מסחרית', section: 'a4', type: 'launch' }
{ date: '2027-12-31', label: 'השלמת פרויקט', section: 'a6', type: 'completion' }
```

### Data Integrity Cross-Checks

- `sections.length` (6) must match stat card value and card back "קטעים" KPI
- On-track count (sections with `progress >= 50`) = 3 → must match card back "במסלול" KPI
- Delayed count (sections with `progress < 50`) = 3 → must match card back "בעיכוב" KPI and stat card sublabel "3 בעיכוב"
- `milestones.length` (4) → must match card back "אבני דרך" KPI
- Section names must match `PROJECT_DATA.sections[].name` identically

### SVG Layout Guidance

**Timeline Range:** April 2022 → December 2027 (~69 months)
**Suggested viewBox:** `"0 0 900 340"` (adjust to fit 6 rows + axes + padding)
**Layout Regions:**
- Y-axis label area: x = 0 to ~140px (Hebrew section names are long)
- Grid area: x = ~140px to ~880px
- X-axis labels: below grid area
- Row height: ~40px per section (6 rows = 240px grid height)
- Top padding: ~30px for month labels
- Bottom padding: ~30px for x-axis labels

**Date-to-X Mapping:**
```javascript
function dateToX(dateStr) {
  var d = new Date(dateStr);
  var startDate = new Date('2022-04-01');
  var endDate = new Date('2027-12-31');
  var totalMs = endDate.getTime() - startDate.getTime();
  var elapsedMs = d.getTime() - startDate.getTime();
  return gridStartX + (elapsedMs / totalMs) * gridWidth;
}
```

### CSS Insertion Point

Insert new CSS section **after** the OptiDocs module CSS section (which follows OptiRisk), **before** the Ambient Background / AI Chat section. Use delimiter:
```css
/* ================================================================ */
/* SECTION: Component — OptiGantt Module                            */
/* ================================================================ */
```

### JS Insertion Point

Replace the existing placeholder stub at `OptiPlan.modules.optigantt` (line ~3582). The section delimiter is:
```javascript
// ================================================================
// MODULE: OptiGantt
// ================================================================
```

### Overlay Integration (Already Wired)

The overlay system is already fully wired for OptiGantt:
- `MODULE_OVERLAY_CONFIG.optigantt` exists (title, subtitle, icon, dataKey)
- Sidebar nav item with `data-module="optigantt"` exists
- Stat card with CTA button exists
- Card back with 4 KPIs exists
- The overlay calls `OptiPlan.modules.optigantt.render(OPTIGANTT_DATA)` on expand
- The overlay calls `OptiPlan.modules.optigantt.destroy()` on close

**The only work needed is:**
1. Add the OptiGantt CSS section
2. Replace the JS placeholder stub with the full module implementation

### Peer Module Patterns to Follow

**OptiBiz (Story 2.4)** — closest peer for SVG chart rendering:
- Uses SVG `<rect>` bars for cash flow chart (plan vs actual)
- CSS classes: `.optibiz-bar--plan { fill: var(--optibiz); opacity: 0.4 }`, `.optibiz-bar--actual { fill: var(--optibiz) }`
- SVG grid lines: `<line>` with `stroke="var(--border)"` `stroke-opacity="0.3"`
- SVG text: `<text>` with `fill="var(--text-muted)"` `font-family="var(--font-main)"` `font-size="10"`
- Metrics row above chart with 4 metric cards

**OptiRisk (Story 2.5)** — reference for SVG viewBox and entrance animation:
- SVG `viewBox="0 0 420 360"` with region-based layout
- CSS class-based fills on SVG elements (`.risk-cell--zone-0` through `.risk-cell--zone-4`)
- Staggered entrance: metrics (0ms), chart (100ms), table (200ms)
- `_entranceTimeouts` array pattern for cleanup

**OptiDocs (Story 2.6)** — reference for most recent pattern decisions:
- Entrance stagger: only 2 stages (metrics: 0ms, table: 100ms)
- Metrics row with `color-mix()` backgrounds
- CSS grid layout patterns

### Anti-Patterns (DO NOT)

1. NO hardcoded hex colors — use `var(--*)` tokens or CSS classes on SVG elements
2. NO anonymous arrow functions — named functions only
3. NO animating width/height/top/left — only `transform` and `opacity`
4. NO new files — edit `index.html` only
5. NO functions outside `OptiPlan.*` namespace
6. NO emojis — Tabler Icons only
7. NO breaking existing modules, card flip, overlay, sidebar nav, theme toggle, idle reset
8. NO canvas/D3.js/charting libraries — vanilla SVG only
9. NO hardcoded display values — ALL numbers from `OPTIGANTT_DATA`
10. NO `setInterval` for animations — CSS transitions only
11. NO growing arrays or orphaned listeners — memory-safe patterns
12. NO `-webkit-overflow-scrolling: touch` (deprecated Safari 16+)
13. NO `console.error` — use `console.warn`
14. NO multiple module overlays simultaneously
15. NO `<table>` elements for data display — use div-based layouts or SVG
16. NO pre-formatted strings in data — format at render time

### Previous Story Intelligence

**From Story 2.5 (OptiRisk — review status):**
- Clean implementation with no errors encountered
- SVG heat map using `<rect>` with `rx` for rounded corners, CSS class-based fills
- Badge system established (`.badge--critical`, `.badge--high`, etc.) — reusable if needed
- Row styling: `var(--bar-track)` alternating backgrounds
- Metrics row pattern: metric cards with `color-mix()` backgrounds

**From Story 2.6 (OptiDocs — ready-for-dev):**
- Div-based CSS grid table (not `<table>`) — consistent pattern
- Sticky header with `position: sticky` in scroll container
- Date formatting via `OptiPlan.utils.formatDate(dateStr)` utility (line ~1971)
- Entrance animation: only 2 stages needed for simpler layouts
- Assignee initials circle pattern with `color-mix()`

### Git Intelligence

Recent commits follow pattern: one story per commit, modifying `index.html` + story `.md` file + `sprint-status.yaml`. Last 5 commits all touch `index.html`. The codebase is currently ~3700 lines in `index.html`.

### Project Structure Notes

- **Single file:** `index.html` is the entire application (~3700 lines)
- **CSS sections 1-18** in order, OptiGantt CSS goes at position 13 (after OptiDocs, before AI Chat)
- **JS sections 1-19** in order, OptiGantt JS goes at position 15 (after OptiDocs, before AI Chat)
- **No build system:** What you write ships directly
- **iPad Safari 16.2+** is the only target browser
- **RTL:** `<html lang="he" dir="rtl">`, all Hebrew labels

### Functional Requirements Coverage

- **FR26:** Investor can view a project schedule visualization showing sections and milestones
- **FR27:** Investor can view schedule adherence status per project section

### References

- [Source: _bmad-output/planning-artifacts/epics.md — Epic 2, Story 2.7 (lines 658-687)]
- [Source: _bmad-output/planning-artifacts/architecture.md — Data Visualization, Module Pattern, CSS/JS Section Order]
- [Source: _bmad-output/planning-artifacts/prd.md — FR26, FR27, Cut-if-needed notes]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md — Component #10 Gantt Timeline, Overlay Patterns, Animation Tokens]
- [Source: _bmad-output/implementation-artifacts/2-5-optirisk-module-risk-heat-map-top-risks-table.md — SVG patterns, entrance animation, badge system]
- [Source: _bmad-output/implementation-artifacts/2-6-optidocs-module-document-tracking-table.md — Latest pattern decisions, metrics row, CSS grid]
- [Source: index.html lines ~2077-2092 — OPTIGANTT_DATA constant]
- [Source: index.html lines ~3582-3598 — Current placeholder stub to replace]
- [Source: index.html lines ~2581-2586 — MODULE_OVERLAY_CONFIG.optigantt]

## Dev Agent Record

### Agent Model Used

Claude Opus 4.6

### Debug Log References

No errors encountered during implementation.

### Completion Notes List

- Implemented full OptiGantt module with horizontal Gantt timeline visualization
- Metrics summary row: 4 cards showing total sections (6), on-track (3), delayed (3), milestones (4) — all computed from OPTIGANTT_DATA
- SVG Gantt chart with viewBox="0 0 900 340" for responsive scaling
- 6 section rows with Hebrew labels (A1-A6 prefix) on y-axis using text-anchor="end"
- Monthly grid lines with quarterly x-axis labels and prominent year markers
- Planned duration bars (rect, rx=3) at 40% opacity in var(--optigantt)
- Actual progress bars at full opacity — section A1 shows overrun past plannedEnd
- Progress calculation: sections with actualEnd=null use (plannedDuration * progress/100) from actualStart
- 4 diamond milestone markers (polygon) with type-based opacity (completion/review/launch)
- Today indicator: dashed vertical path using new Date() — not hardcoded
- CSS section with zero hardcoded hex values, all via var() custom properties
- Entrance animation: staggered 100ms (metrics -> chart), with prefers-reduced-motion override
- Module follows init/render/destroy lifecycle pattern matching OptiBiz/OptiRisk peers
- All var declarations, named functions only (Safari compat), no arrow functions
- Pure SVG: rect, line, text, polygon, path elements only — no canvas, no libraries

### Change Log

- 2026-02-23: Story 2.7 implementation complete — OptiGantt module with Gantt timeline, metrics row, milestone markers, today indicator

### File List

- index.html (modified: added OptiGantt CSS section after OptiDocs, replaced JS placeholder stub with full module implementation)
- _bmad-output/implementation-artifacts/2-7-optigantt-module-schedule-timeline.md (modified: status updates, task checkboxes, dev agent record)
- _bmad-output/implementation-artifacts/sprint-status.yaml (modified: story status ready-for-dev -> in-progress -> review)
