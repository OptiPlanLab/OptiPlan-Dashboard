# Story 2.3: OptiTrack Module — Permit Matrix & Activity Feed

Status: done

## Story

As an investor,
I want to see the full OptiTrack module with a coordination permit matrix and activity feed,
so that I understand how the platform manages complex multi-authority infrastructure approvals at scale.

## Acceptance Criteria

1. **Given** the module overlay container is open for OptiTrack
   **When** `OptiPlan.modules.optitrack.render(OPTITRACK_DATA)` is called
   **Then** a metrics summary row renders at the top: 347 total items, 64% approved, 12 objections, 35 pending — all values sourced from `OPTITRACK_DATA`

2. **Given** the OptiTrack overlay renders
   **When** a developer inspects the permit matrix
   **Then** it shows sections (A1–A6) as rows and regulatory authorities (Municipality, Israel Electric, Water, Communications, Antiquities) as columns
   **And** each cell is color-coded: green = approved, amber = pending, red = rejected, gray = not submitted
   **And** each cell also displays a text label or count — never color as the sole status indicator
   **And** cells are built with SVG `<rect>` elements with `rx="var(--radius-sm)"`

3. **Given** the permit matrix is rendered
   **When** the overlay content is scrolled
   **Then** the matrix scrolls naturally within the overlay content area
   **And** the back button and module header remain fixed (do not scroll with content)

4. **Given** the OptiTrack overlay renders
   **When** a developer inspects the activity feed
   **Then** 5+ feed items render, each with: colored Hebrew initial avatar, Hebrew name, relative timestamp in JetBrains Mono (e.g., "3 min ago"), Hebrew description, and a status badge
   **And** feed data is sourced from `OPTITRACK_DATA.activityFeed` — no hardcoded content in the component
   **And** `OptiPlan.modules.optitrack` is namespaced with `init()`, `render(data)`, `destroy()` methods

## Tasks / Subtasks

- [x] Task 1: Add metrics summary row HTML/CSS (AC: #1)
  - [x] 1.1 Create `.optitrack-metrics` container with 4 metric cards
  - [x] 1.2 Style metric cards: value (JetBrains Mono bold), label (Varela Round), module color accent
  - [x] 1.3 Wire values from `OPTITRACK_DATA` — zero hardcoded numbers

- [x] Task 2: Implement permit matrix SVG visualization (AC: #2)
  - [x] 2.1 Create `.permit-matrix` container with SVG viewBox
  - [x] 2.2 Render row headers (sections A1–A6) and column headers (5 authorities in Hebrew)
  - [x] 2.3 Render SVG `<rect>` cells with `rx="var(--radius-sm)"` and status color-coding
  - [x] 2.4 Add text labels inside or adjacent to each cell (never color alone)
  - [x] 2.5 Source all cell data from `OPTITRACK_DATA.permitMatrix`

- [x] Task 3: Style permit matrix for readability (AC: #2, #3)
  - [x] 3.1 Apply color tokens: `var(--status-online)` (green/approved), `var(--optibiz)` (amber/pending), `var(--optirisk)` (red/rejected), `var(--text-muted)` (gray/not-submitted)
  - [x] 3.2 Ensure matrix scrolls within `.module-overlay__content` — header stays fixed
  - [x] 3.3 RTL layout: authority names read right-to-left, flex/grid reversal

- [x] Task 4: Implement activity feed component (AC: #4)
  - [x] 4.1 Create `.activity-feed` container with feed item list
  - [x] 4.2 Each item: colored initial avatar (first Hebrew letter), name, timestamp, description, status badge
  - [x] 4.3 Timestamp in JetBrains Mono with relative formatting
  - [x] 4.4 Source all feed items from `OPTITRACK_DATA.activityFeed`
  - [x] 4.5 Status badge with appropriate color per activity type

- [x] Task 5: Implement OptiPlan.modules.optitrack namespace (AC: #4)
  - [x] 5.1 Create `init()` method — cache DOM refs
  - [x] 5.2 Create `render(data)` method — generates all HTML for metrics, matrix, and feed
  - [x] 5.3 Create `destroy()` method — clean up any event listeners
  - [x] 5.4 Replace placeholder stub from Story 2.2 with full implementation

- [x] Task 6: Staggered content entrance animation
  - [x] 6.1 Metrics row fades in first (0ms delay)
  - [x] 6.2 Permit matrix fades in second (100ms delay)
  - [x] 6.3 Activity feed fades in third (200ms delay)
  - [x] 6.4 Use `opacity: 0→1` + `translateY(12px)→0` over 300ms per element

- [x] Task 7: Verify and test all acceptance criteria
  - [x] 7.1 Zero console errors on overlay open/close
  - [x] 7.2 All metric values match `OPTITRACK_DATA` constants
  - [x] 7.3 Permit matrix cells render correct status per `permitMatrix` array
  - [x] 7.4 Activity feed items sourced from `activityFeed` array
  - [x] 7.5 Matrix scrolls within overlay; header stays fixed
  - [x] 7.6 Theme toggle works (dark/light) on all new elements
  - [x] 7.7 RTL layout correct for Hebrew text
  - [x] 7.8 Touch targets meet 44px minimum
  - [x] 7.9 Existing card flip, overlay open/close, sidebar nav unbroken

## Dev Notes

### Architecture Patterns & Constraints

**Single File Architecture:** ALL code goes into `index.html`. No separate JS/CSS files.

**Module Namespace Pattern (MUST follow exactly):**
```javascript
// ================================================================
// MODULE: OptiTrack
// ================================================================
OptiPlan.modules.optitrack = {
  init: function() {
    // Cache DOM refs with $ prefix
  },
  render: function(data) {
    // Return HTML string or populate container innerHTML
    // ALL values from data parameter — zero hardcoded numbers
  },
  destroy: function() {
    // Remove event listeners, clear refs
  }
};
```

**This replaces the placeholder stub** created in Story 2.2. The stub currently renders a placeholder message. You MUST replace it entirely with the full implementation.

**Data Source — `OPTITRACK_DATA` (already defined in Story 1.2, Object.freeze'd):**
```javascript
{
  totalItems: 347,
  approved: 222,
  approvalRate: 64,
  pending: 35,
  objections: 12,
  inReview: 78,
  authorities: [
    { id: 'municipality', name: 'עירייה', approved: 52, pending: 8, objections: 2 },
    { id: 'electric', name: 'חברת חשמל', approved: 41, pending: 12, objections: 4 },
    { id: 'water', name: 'מקורות', approved: 48, pending: 5, objections: 1 },
    { id: 'telecom', name: 'בזק', approved: 44, pending: 6, objections: 3 },
    { id: 'antiquities', name: 'רשות העתיקות', approved: 37, pending: 4, objections: 2 }
  ],
  permitMatrix: [
    { section: 'A1', municipality: 'approved', electric: 'approved', water: 'approved', telecom: 'pending', antiquities: 'approved' },
    { section: 'A2', municipality: 'approved', electric: 'pending', water: 'approved', telecom: 'approved', antiquities: 'objection' },
    { section: 'A3', municipality: 'pending', electric: 'approved', water: 'pending', telecom: 'approved', antiquities: 'approved' },
    { section: 'A4', municipality: 'approved', electric: 'objection', water: 'approved', telecom: 'approved', antiquities: 'not_submitted' },
    { section: 'A5', municipality: 'approved', electric: 'approved', water: 'objection', telecom: 'pending', antiquities: 'approved' },
    { section: 'A6', municipality: 'pending', electric: 'approved', water: 'approved', telecom: 'approved', antiquities: 'pending' }
  ],
  activityFeed: [
    { id: 'af1', name: 'דוד כהן', action: 'אישור היתר חפירה קטע A1', timestamp: '2026-02-23T10:15:00Z', status: 'approved', section: 'A1' },
    { id: 'af2', name: 'שרה לוי', action: 'הגשת בקשה לחברת חשמל', timestamp: '2026-02-23T09:42:00Z', status: 'pending', section: 'A2' },
    { id: 'af3', name: 'יוסי אברהם', action: 'התנגדות רשות העתיקות קטע A2', timestamp: '2026-02-23T08:30:00Z', status: 'objection', section: 'A2' },
    { id: 'af4', name: 'מיכל ברק', action: 'עדכון סטטוס מקורות קטע A5', timestamp: '2026-02-22T16:20:00Z', status: 'in_review', section: 'A5' },
    { id: 'af5', name: 'אבי גולן', action: 'אישור עירייה קטע A3', timestamp: '2026-02-22T14:05:00Z', status: 'approved', section: 'A3' },
    { id: 'af6', name: 'נועה שמיר', action: 'הגשת תוכניות בזק קטע A6', timestamp: '2026-02-22T11:30:00Z', status: 'pending', section: 'A6' }
  ]
}
```

### Permit Matrix Implementation Details

**SVG-Based Grid (NOT HTML table):**
- Use `<svg>` with a `viewBox` sized to accommodate 6 rows x 5 columns
- Each cell: `<rect>` with `rx` = 4 (matching `--radius-sm: 8px` scaled to SVG units)
- Cell size: approximately 60x36 SVG units with 4-unit gaps
- Row labels (A1–A6): `<text>` elements in Varela Round, `var(--text-primary)`
- Column labels (authority Hebrew names): `<text>` elements, rotated or horizontal
- Status text inside cells: short label like "V" / approved count or Hebrew abbreviation

**Color Mapping (use CSS custom properties via `style` attributes or class-based fills):**
```
approved     → var(--status-online)   → #22C55E (green)
pending      → var(--optibiz)         → #F6AE2D (amber)
objection    → var(--optirisk)        → #FF7A3C (red/orange)
rejected     → #EF4444 (add as --status-danger if not exists)
not_submitted → var(--text-muted)     → #6B7280 (gray)
in_review    → var(--optitrack)       → #0991F3 (blue)
```

**IMPORTANT:** SVG `<rect>` cannot use CSS custom properties in `fill` directly in all browsers. Use inline `fill` attribute with the resolved hex value OR use CSS classes on rects:
```css
.permit-cell--approved { fill: var(--status-online); }
.permit-cell--pending { fill: var(--optibiz); }
.permit-cell--objection { fill: var(--optirisk); }
.permit-cell--not_submitted { fill: var(--text-muted); }
```
Then apply class to each `<rect>`. This works in Safari 16.2+.

**Accessibility:** Each colored cell MUST have a text label. Options:
- Short Hebrew text inside cell: "V" (approved), "?" (pending), "X" (objection), "-" (not submitted)
- Or use counts from authorities data if cell contains multiple items

### Activity Feed Implementation Details

**Feed Item Structure:**
```html
<div class="activity-item">
  <div class="activity-item__avatar" style="background: {color}">
    {firstHebrewLetter}
  </div>
  <div class="activity-item__body">
    <div class="activity-item__header">
      <span class="activity-item__name">{name}</span>
      <span class="activity-item__time">{relativeTime}</span>
    </div>
    <p class="activity-item__desc">{action}</p>
  </div>
  <span class="activity-item__badge activity-item__badge--{status}">{statusLabel}</span>
</div>
```

**Avatar Colors:** Generate deterministic color from name's first character. Use module color palette or a set of 5-6 distinct colors that don't conflict with status colors.

**Relative Timestamps:** Compute relative time from `timestamp` field:
- Under 60 minutes: "X min ago" in Hebrew format
- Under 24 hours: "X hours ago" in Hebrew format
- Otherwise: date string
- Use JetBrains Mono font, `var(--text-muted)` color

**Status Badge Mapping:**
```
approved   → green background, "מאושר"
pending    → amber background, "ממתין"
objection  → red background, "התנגדות"
in_review  → blue background, "בבדיקה"
```

### Content Entrance Animation Pattern

Follow the stagger pattern established in Story 2.2 for module overlay content:
```css
.optitrack-section {
  opacity: 0;
  transform: translateY(12px);
  transition: opacity 300ms var(--transition), transform 300ms var(--transition);
}

.optitrack-section--visible {
  opacity: 1;
  transform: translateY(0);
}
```

In `render()`, add `--visible` class with staggered `setTimeout`:
- Metrics row: 0ms
- Permit matrix: 100ms
- Activity feed: 200ms

### Project Structure Notes

- **Single file:** ALL additions go into `index.html`
- **CSS section:** Add after "Component — Module Overlay" section (Section 8 in CSS order), create new section: "Component — OptiTrack Module"
- **JS section:** Replace the `OptiPlan.modules.optitrack` placeholder stub (Section 11 in JS order)
- **No new HTML elements in body:** Module content is rendered dynamically by `render(data)` into the `.module-overlay__content` container
- **Section delimiter format:**
```javascript
// ================================================================
// MODULE: OptiTrack
// ================================================================
```

### Anti-Patterns to AVOID

1. **NO hardcoded hex colors** — use `var(--*)` tokens or CSS classes on SVG elements
2. **NO anonymous arrow functions** — named functions only for event handlers
3. **NO animating width/height/top/left** — only `transform` and `opacity`
4. **NO new files** — edit `index.html` only
5. **NO functions outside `OptiPlan.*` namespace**
6. **NO emojis** — Tabler Icons only
7. **NO breaking existing** entrance animations, touch feedback, theme toggle, card flip, overlay open/close, idle reset, sidebar nav
8. **NO canvas/D3.js/charting libraries** — vanilla SVG + HTML only
9. **NO hardcoded display values** — ALL numbers from `OPTITRACK_DATA`
10. **NO HTML table for permit matrix** — use SVG `<rect>` per AC requirement
11. **NO `setInterval` for animations** — CSS transitions/animations only
12. **NO growing arrays or orphaned listeners** — memory-safe patterns
13. **NO forgetting `-webkit-` prefixes** for Safari: `-webkit-backface-visibility`, `-webkit-perspective`
14. **NO `console.error`** — use `console.warn` for caught errors, `console.log` for state changes
15. **NO multiple module overlays** — only one in DOM at a time (already enforced by Story 2.2)

### CSS Custom Property Rules

- ALL structural colors MUST use `var(--*)` tokens
- Module color set via `--module` local custom property (already set by overlay from Story 2.2)
- Background: `var(--bg-card)` — adapts to dark/light theme
- Text: `var(--text-primary)`, `var(--text-secondary)`, `var(--text-muted)`
- Borders: `var(--border)`
- Spacing: `var(--gap)`, `var(--gap-sm)`, `var(--gap-lg)`
- Radius: `var(--radius)`, `var(--radius-sm)`
- Font: `var(--font-main)` for Varela Round, `var(--font-mono)` for JetBrains Mono

### RTL Layout Considerations

- HTML `dir="rtl"` is set on `<html>` — all flex/grid auto-reverses
- Authority column headers read right-to-left naturally
- Activity feed text flows RTL
- Use `margin-inline-start` / `padding-inline-end` (logical properties)
- Back button positioned via `inset-inline-end` (handled by Story 2.2 overlay)
- Timestamps (numbers) render correctly in RTL context

### Safari WebKit Compatibility

- SVG `<rect>` with CSS class-based fills works in Safari 16.2+
- `rx` attribute on `<rect>` for rounded corners supported
- CSS custom properties in SVG (via classes) supported
- `overflow-y: auto` on content container (no deprecated `-webkit-overflow-scrolling`)
- `overscroll-behavior: contain` on scroll container
- Font rendering: Varela Round + JetBrains Mono via Google Fonts CDN

### Previous Story Intelligence

**From Story 2.2 (Module Expansion Overlay — immediately prior):**
- Overlay container fully functional: `OptiPlan.components.moduleOverlay.expand('optitrack')` opens overlay
- Header already renders: "OptiTrack — ניהול תיאומים" with blue accent bar and Tabler icon
- Content area: `.module-overlay__content` is the render target — your `render(data)` output goes HERE
- Module stub exists: `OptiPlan.modules.optitrack = { init, render, destroy }` — REPLACE the placeholder
- `MODULE_OVERLAY_CONFIG.optitrack.dataKey` = `'OPTITRACK_DATA'` — overlay calls `render(window[dataKey])`
- Close/destroy lifecycle: overlay calls `OptiPlan.modules[moduleId].destroy()` on close

**From Story 2.1 (Card Flip — same epic):**
- Card back shows OptiTrack KPIs: 347 total, 64% approved, 12 objections, 35 pending
- CTA "Full Module" button triggers overlay expand
- Data sourced from `OPTITRACK_DATA` via `CARD_BACK_CONFIG`
- Named handler pattern: `handleStatCardTap`, `handleOutsideTap`

**From Story 1.5 (Stat Cards):**
- OptiTrack front card shows: 222 approved, +18% trend
- `$` prefix for DOM refs convention
- `data-module="optitrack"` attribute on card element

**From Story 1.2 (Data Model):**
- `OPTITRACK_DATA` is `Object.freeze()`'d — read-only at runtime
- All 6 `*_DATA` constants defined with complete datasets
- `permitMatrix` array has 6 objects (one per section), each with authority status keys
- `activityFeed` array has 6 activity objects with name, action, timestamp, status, section

**From Story 1.1 (Scaffold):**
- `OptiPlan` namespace established with `modules: {}` placeholder
- Section delimiter pattern for code organization
- Design token system in `:root`

### Data Consistency Cross-References

| Location | Value | Must Match |
|----------|-------|------------|
| Stat card front (1.5) | 222 approved | `OPTITRACK_DATA.approved` |
| Card back KPI (2.1) | 347 total items | `OPTITRACK_DATA.totalItems` |
| Card back KPI (2.1) | 64% approved | `OPTITRACK_DATA.approvalRate` |
| Card back KPI (2.1) | 12 objections | `OPTITRACK_DATA.objections` |
| Card back KPI (2.1) | 35 pending | `OPTITRACK_DATA.pending` |
| Hero gauge (1.4) | 64% coordination | `OPTITRACK_DATA.approvalRate` |
| Module metrics (this story) | All 4 summary values | `OPTITRACK_DATA.*` |
| Permit matrix cells | Status per section/authority | `OPTITRACK_DATA.permitMatrix[].{authority}` |
| Activity feed items | 6 feed entries | `OPTITRACK_DATA.activityFeed[]` |

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Epic-2-Story-2.3]
- [Source: _bmad-output/planning-artifacts/architecture.md#Module-Expansion-Pattern]
- [Source: _bmad-output/planning-artifacts/architecture.md#OptiTrack-Module]
- [Source: _bmad-output/planning-artifacts/architecture.md#Data-Model]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Layer-3-Module-Expansion]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Permit-Matrix]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Activity-Feed]
- [Source: _bmad-output/planning-artifacts/prd.md#FR17-FR19]
- [Source: _bmad-output/implementation-artifacts/2-2-module-expansion-overlay-container-layer-3.md]
- [Source: _bmad-output/implementation-artifacts/2-1-card-flip-interaction-kpi-back-face-layer-2.md]

## Dev Agent Record

### Agent Model Used

Claude Opus 4.6 (claude-opus-4-6)

### Debug Log References

No errors encountered during implementation.

### Completion Notes List

- Replaced OptiTrack placeholder stub with full module implementation (init, render, destroy)
- Metrics summary row: 4 cards (total items, approval rate, objections, pending) — all values from `OPTITRACK_DATA`
- Permit matrix: SVG-based 6x5 grid with `<rect>` cells, `rx="4"`, CSS class-based fill colors, text labels per cell
- Color mapping: approved=green, pending=amber, objection=orange, not_submitted=gray via CSS custom properties
- Activity feed: 6 items with colored Hebrew initial avatar, name, relative timestamp (Hebrew), description, status badge
- Status badges: Hebrew labels (מאושר, ממתין, התנגדות, בבדיקה) with color-coded backgrounds
- Staggered entrance: metrics (0ms), matrix (100ms), feed (200ms) via opacity+translateY CSS transitions
- All CSS uses `var(--*)` tokens — fully theme-aware (dark/light)
- RTL-compatible: flex layout auto-reverses, Hebrew text flows naturally
- Memory-safe: destroy() clears all timeouts and innerHTML
- No arrow functions, no setInterval, no console.error, no hardcoded values
- 64 automated tests passed (50 AC tests + 14 verification tests)

### Change Log

- 2026-02-23: Implemented full OptiTrack module (Tasks 1-7) — permit matrix, activity feed, metrics row, entrance animations
- 2026-02-23: Code review fixes — 3 CRITICAL (data model mismatches), 4 HIGH (CSS class/color issues), 3 MEDIUM (token compliance, case)

### Senior Developer Review (AI)

**Reviewer:** BenAkiva (via Claude Opus 4.6) on 2026-02-23

**Outcome:** Changes Requested → Fixed

**Issues Found:** 3 Critical, 4 High, 3 Medium, 2 Low (12 total)

**Critical Issues Fixed:**
- C1: Permit matrix `row[authorityKeys[col]]` → `row.statuses[col]` — data model mismatch, entire matrix was non-functional
- C2: Activity feed `item.action` → `item.description` — wrong field name, descriptions showed "undefined"
- C3: `_formatRelativeTime()` expected ISO timestamps but data has pre-formatted Hebrew strings — timestamps showed "NaN/NaN/NaN"

**High Issues Fixed:**
- H1: CSS `.permit-cell--not_submitted` → `.permit-cell--not-submitted` (hyphen vs underscore mismatch)
- H2: CSS `.activity-item__badge--in_review` → `.activity-item__badge--in-review` (hyphen vs underscore mismatch)
- H3: Added missing `in-review` status handling (CSS class + label) for permit matrix
- H4: Replaced hardcoded hex avatar colors with CSS custom property references

**Medium Issues Fixed:**
- M1: Section labels `row.section` → `row.section.toUpperCase()` to show "A1" per AC
- M2: `fill: #fff` → `fill: var(--text-on-color)` + added `--text-on-color` token to `:root`
- M3: `color: #fff` → `color: var(--text-on-color)` in avatar

**Low Issues (not fixed):**
- L1: Hardcoded `gap: 4px`, `border-radius: 10px` — no matching design tokens exist
- L2: Avatar uses `name.charAt(0)` instead of `item.initials` from data

### File List

- `index.html` — Added OptiTrack CSS section (Component — OptiTrack Module) and replaced placeholder stub with full module implementation; code review fixes applied
