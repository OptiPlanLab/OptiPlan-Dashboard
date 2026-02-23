# Story 2.6: OptiDocs Module — Document Tracking Table

Status: done

## Story

As an investor,
I want to see the full OptiDocs module with a document tracking table and completion summary,
so that I understand the platform's document management and approval workflow at project scale.

## Acceptance Criteria

1. **Given** the module overlay is open for OptiDocs
   **When** `OptiPlan.modules.optidocs.render(OPTIDOCS_DATA)` is called
   **Then** a metrics summary renders: 847 total documents, 94% completion rate, pending count, overdue count — all sourced from `OPTIDOCS_DATA`
   **And** the 847 total and 94% figures match the values displayed on the OptiDocs stat card from Epic 1

2. **Given** the OptiDocs overlay renders
   **When** a developer inspects the document table
   **Then** rows contain: Hebrew document name, document type, status badge (pill: green=אושר, amber=ממתין, red=באיחור), date in DD/MM/YY format (JetBrains Mono), and assigned person initials
   **And** alternating rows use `background: var(--bar-track)` and row hover uses `background: var(--feed-hover)`
   **And** status badges always combine color with Hebrew text — never color alone

3. **Given** the document table has more rows than fit in the overlay
   **When** an investor scrolls within the overlay
   **Then** the table scrolls naturally; column headers remain visible (sticky or fixed within the scroll container)
   **And** `OptiPlan.modules.optidocs` follows the `init()`, `render(data)`, `destroy()` pattern

## Tasks / Subtasks

- [x] Task 1: Add document metrics summary row (AC: #1)
  - [x] 1.1 Create `.optidocs-metrics` container with 4 metric cards (follow `.optitrack-metrics` pattern)
  - [x] 1.2 Metric 1: Total documents — `OPTIDOCS_DATA.total` (847)
  - [x] 1.3 Metric 2: Completion rate — `OPTIDOCS_DATA.completionRate` (94%)
  - [x] 1.4 Metric 3: Pending — `OPTIDOCS_DATA.pending` (38)
  - [x] 1.5 Metric 4: Overdue — `OPTIDOCS_DATA.overdue` (13) with accent color
  - [x] 1.6 All values from `OPTIDOCS_DATA` — zero hardcoded numbers
  - [x] 1.7 Follow `.optitrack-metrics` / `.optibiz-metrics` pattern from Stories 2.3/2.4

- [x] Task 2: Implement document tracking table header (AC: #2, #3)
  - [x] 2.1 Create `.optidocs-table` container with CSS grid or flex layout
  - [x] 2.2 Table header row with 5 columns: מסמך (Document), סוג (Type), סטטוס (Status), תאריך (Date), אחראי (Assigned)
  - [x] 2.3 Header uses `var(--text-muted)`, `var(--font-main)`, 11px uppercase
  - [x] 2.4 Header has sticky positioning (`position: sticky; top: 0`) within scroll container
  - [x] 2.5 Header background: `var(--bg-card)` to maintain visibility over scrolling rows

- [x] Task 3: Implement document table rows (AC: #2)
  - [x] 3.1 Render all 8 documents from `OPTIDOCS_DATA.documents`
  - [x] 3.2 Each row contains: Hebrew document name, type badge, status badge, formatted date, assignee initials
  - [x] 3.3 Document name: `var(--font-main)`, `var(--text-primary)`, 13-14px
  - [x] 3.4 Type: text label in `var(--text-secondary)`, 12px
  - [x] 3.5 Date: DD/MM/YY format via `OptiPlan.utils.formatDate()` in `var(--font-mono)`
  - [x] 3.6 Assignee: initials circle/avatar (2-char Hebrew initials from `document.assignee`)
  - [x] 3.7 Alternating row backgrounds: odd rows transparent, even rows `var(--bar-track)`
  - [x] 3.8 Row hover: `background: var(--feed-hover)` transition

- [x] Task 4: Implement status badges for documents (AC: #2)
  - [x] 4.1 Status badge mapping:
    - `approved` → green background + "אושר"
    - `pending` → amber background + "ממתין"
    - `overdue` → red background + "באיחור"
  - [x] 4.2 Badge styling: colored pill (inline-block, padding 2px 8px, border-radius `var(--radius-sm)`)
  - [x] 4.3 Badge uses `rgba()` background with text in matching color (follow OptiRisk badge pattern)
  - [x] 4.4 Color + text always present — never color alone as status indicator

- [x] Task 5: Implement scrollable table with sticky header (AC: #3)
  - [x] 5.1 Wrap table in `.optidocs-table-wrapper` with `overflow-y: auto`
  - [x] 5.2 Set `max-height` appropriate for overlay content area
  - [x] 5.3 Table header uses `position: sticky; top: 0; z-index: 1`
  - [x] 5.4 Header gets `background: var(--bg-card)` for visibility over scrolling content
  - [x] 5.5 Add `overscroll-behavior: contain` to prevent propagation to overlay backdrop
  - [x] 5.6 Touch scroll works naturally within the container

- [x] Task 6: Implement OptiPlan.modules.optidocs namespace (AC: #3)
  - [x] 6.1 Create `init()` method — cache DOM refs with `$` prefix
  - [x] 6.2 Create `render(data)` method — generates all HTML for metrics + table via innerHTML
  - [x] 6.3 Create `destroy()` method — clear timeouts, clear innerHTML
  - [x] 6.4 Replace placeholder stub from Story 2.2 with full implementation

- [x] Task 7: Staggered content entrance animation
  - [x] 7.1 Metrics row fades in first (0ms delay)
  - [x] 7.2 Table fades in second (100ms delay)
  - [x] 7.3 Use `opacity: 0→1` + `translateY(12px)→0` over 300ms
  - [x] 7.4 Apply `--visible` class via staggered `setTimeout` in `render()`
  - [x] 7.5 Store timeout references in `_entranceTimeouts` array for cleanup in `destroy()`

- [x] Task 8: Add CSS section for OptiDocs module
  - [x] 8.1 Add new CSS section: `/* SECTION: Component — OptiDocs Module */` after OptiBiz Module CSS (after line ~1231, before Ambient Background section)
  - [x] 8.2 Metrics summary styles (follow `.optitrack-metrics` pattern with `--optidocs` color)
  - [x] 8.3 Table container styles with grid columns
  - [x] 8.4 Table header sticky styles
  - [x] 8.5 Row styles with alternating backgrounds
  - [x] 8.6 Status badge pill styles (follow OptiRisk badge pattern from Story 2.5)
  - [x] 8.7 Assignee initials circle styles
  - [x] 8.8 Entrance animation classes (`.optidocs-section` + `.optidocs-section--visible`)
  - [x] 8.9 All colors via `var(--*)` tokens — zero hardcoded hex

- [x] Task 9: Verify and test all acceptance criteria
  - [x] 9.1 Zero console errors on overlay open/close
  - [x] 9.2 Metrics row shows 847 total, 94% complete, 38 pending, 13 overdue from `OPTIDOCS_DATA`
  - [x] 9.3 Document table renders all 8 documents from `OPTIDOCS_DATA.documents`
  - [x] 9.4 Status badges show correct Hebrew text + color (אושר/ממתין/באיחור)
  - [x] 9.5 Dates render in DD/MM/YY format via JetBrains Mono
  - [x] 9.6 Assignee initials render correctly
  - [x] 9.7 Alternating row backgrounds work in dark and light themes
  - [x] 9.8 Row hover shows `var(--feed-hover)` background
  - [x] 9.9 Table header stays visible during scroll (sticky)
  - [x] 9.10 Table scrolls naturally within overlay content area
  - [x] 9.11 847 total and 94% match stat card values from Epic 1
  - [x] 9.12 Theme toggle works (dark/light) on all new elements
  - [x] 9.13 Hebrew text renders correctly in RTL
  - [x] 9.14 Existing card flip, overlay open/close, sidebar nav unbroken
  - [x] 9.15 Touch targets meet 44px minimum
  - [x] 9.16 No hardcoded hex values in new CSS
  - [x] 9.17 Entrance animation stagger works (metrics → table)

## Dev Notes

### Architecture Patterns & Constraints

**Single File Architecture:** ALL code goes into `index.html`. No separate JS/CSS files.

**Module Namespace Pattern (MUST follow exactly):**
```javascript
// ================================================================
// MODULE: OptiDocs
// ================================================================
OptiPlan.modules.optidocs = {
  init: function() {
    // Cache DOM refs with $ prefix
  },
  render: function(data) {
    // Populate .module-overlay__content via innerHTML
    // ALL values from data parameter — zero hardcoded numbers
  },
  destroy: function() {
    // Clear timeouts, clear innerHTML
  }
};
```

**This replaces the placeholder stub** created in Story 2.2. The stub currently renders a centered icon + "Content coming in Story 2.6" at line ~3144 in `index.html`. You MUST replace it entirely with the full implementation.

**Data Source — `OPTIDOCS_DATA` (already defined in Story 1.2, Object.freeze'd at line ~1815):**
```javascript
{
  total: 847,
  approved: 796,
  completionRate: 94,
  pending: 38,
  overdue: 13,
  documents: [
    { id: 'D001', name: 'תוכנית בינוי קטע א1', type: 'תוכנית', status: 'approved', date: '2024-03-15', assignee: 'ד.ל' },
    { id: 'D002', name: 'דוח סקר קרקע א2', type: 'דוח', status: 'approved', date: '2024-05-22', assignee: 'מ.כ' },
    { id: 'D003', name: 'היתר בנייה א3 — שלב א', type: 'היתר', status: 'pending', date: '2024-09-10', assignee: 'א.ש' },
    { id: 'D004', name: 'תסקיר השפעה על הסביבה', type: 'תסקיר', status: 'approved', date: '2024-01-30', assignee: 'ר.ב' },
    { id: 'D005', name: 'חוזה קבלן ראשי — קטע א4', type: 'חוזה', status: 'approved', date: '2024-07-08', assignee: 'נ.פ' },
    { id: 'D006', name: 'בקשת שינוי — מנהרה א3', type: 'בקשת שינוי', status: 'overdue', date: '2024-10-01', assignee: 'י.ג' },
    { id: 'D007', name: 'תוכנית ניהול בטיחות א2', type: 'תוכנית', status: 'approved', date: '2024-04-18', assignee: 'ד.ל' },
    { id: 'D008', name: 'פרוטוקול בדיקת איכות א1', type: 'פרוטוקול', status: 'approved', date: '2024-06-30', assignee: 'מ.כ' }
  ]
}
```

**Data integrity check:**
- `approved` count: 6 documents with status 'approved' out of 8 in the array
- Total documents in data: 847 — the 8 records in the `documents` array are representative samples displayed in the table, NOT the complete dataset
- `completionRate`: 94% — `approved` (796) / `total` (847) ≈ 94%
- `pending`: 38 — count of documents awaiting review across the full dataset
- `overdue`: 13 — count of overdue documents across the full dataset

### Document Table Implementation Details

**Table Structure (HTML divs with CSS grid, NOT `<table>`):**

Use CSS grid for the table layout to maintain consistent column widths and enable sticky header. This follows the established pattern from OptiRisk's top risks table (div-based layout).

```html
<div class="optidocs-table-wrapper">
  <div class="optidocs-table">
    <!-- Sticky Header -->
    <div class="optidocs-table__header">
      <span class="optidocs-table__col optidocs-table__col--name">מסמך</span>
      <span class="optidocs-table__col optidocs-table__col--type">סוג</span>
      <span class="optidocs-table__col optidocs-table__col--status">סטטוס</span>
      <span class="optidocs-table__col optidocs-table__col--date">תאריך</span>
      <span class="optidocs-table__col optidocs-table__col--assignee">אחראי</span>
    </div>
    <!-- Rows from OPTIDOCS_DATA.documents -->
  </div>
</div>
```

**Column Grid Layout:**
```css
.optidocs-table__header,
.optidocs-table__row {
  display: grid;
  grid-template-columns: 2fr 1fr 1fr 1fr 0.6fr;
  align-items: center;
  gap: var(--gap-sm);
  padding: 10px var(--gap);
}
```

**Column proportions (RTL auto-reversed):**
- Name: 2fr (widest — Hebrew document names can be long)
- Type: 1fr (document category)
- Status: 1fr (badge pill)
- Date: 1fr (DD/MM/YY)
- Assignee: 0.6fr (2-char initials)

**Row Structure:**
```html
<div class="optidocs-table__row optidocs-table__row--alt">
  <span class="optidocs-table__col optidocs-table__col--name">{doc.name}</span>
  <span class="optidocs-table__col optidocs-table__col--type">{doc.type}</span>
  <span class="optidocs-table__col optidocs-table__col--status">
    <span class="badge badge--{statusKey}">{statusLabel}</span>
  </span>
  <span class="optidocs-table__col optidocs-table__col--date">{formattedDate}</span>
  <span class="optidocs-table__col optidocs-table__col--assignee">
    <span class="optidocs-assignee">{doc.assignee}</span>
  </span>
</div>
```

**Status Badge Mapping:**
```
approved → green badge, "אושר"  → .badge--approved { background: rgba(34, 197, 94, 0.2); color: var(--status-online); }
pending  → amber badge, "ממתין" → .badge--pending  { background: rgba(246, 174, 45, 0.2); color: var(--optibiz); }
overdue  → red badge, "באיחור"  → .badge--overdue  { background: rgba(239, 68, 68, 0.2); color: #EF4444; }
```

Note: Reuse badge classes from OptiRisk Story 2.5 if they already exist (`.badge--approved` may not exist yet — OptiRisk uses `.badge--critical`, `.badge--high`, etc.). Check if common badge styles exist; if not, add new status-specific badge classes.

**Date Formatting:**
Use the existing `OptiPlan.utils.formatDate(dateStr)` utility (defined at line ~1971) which returns DD/MM/YY format.

**Assignee Initials Circle:**
```css
.optidocs-assignee {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  background: color-mix(in srgb, var(--optidocs) 15%, var(--bg-card));
  color: var(--optidocs);
  font-family: var(--font-main);
  font-size: 11px;
  font-weight: 600;
}
```

### Metrics Summary Row

**Follow the exact pattern from Stories 2.3 (OptiTrack) and 2.4 (OptiBiz):**
```html
<div class="optidocs-metrics optidocs-section">
  <div class="optidocs-metric">
    <span class="optidocs-metric__value">{total}</span>
    <span class="optidocs-metric__label">סה"כ מסמכים</span>
  </div>
  <div class="optidocs-metric">
    <span class="optidocs-metric__value">{completionRate}%</span>
    <span class="optidocs-metric__label">אחוז השלמה</span>
  </div>
  <div class="optidocs-metric">
    <span class="optidocs-metric__value">{pending}</span>
    <span class="optidocs-metric__label">ממתינים</span>
  </div>
  <div class="optidocs-metric">
    <span class="optidocs-metric__value optidocs-metric__value--overdue">{overdue}</span>
    <span class="optidocs-metric__label">באיחור</span>
  </div>
</div>
```

**Metric value styling:** JetBrains Mono Bold, 24px, `var(--text-primary)`. Overdue value uses `color: #EF4444` or red accent to draw attention.

**Metric card background:** `color-mix(in srgb, var(--optidocs) 8%, var(--bg-card))` with `border: 1px solid color-mix(in srgb, var(--optidocs) 15%, transparent)` — exactly matches OptiTrack/OptiBiz patterns.

### Content Entrance Animation Pattern

Follow the exact stagger pattern from Stories 2.3, 2.4, and 2.5:
```css
.optidocs-section {
  opacity: 0;
  transform: translateY(12px);
  transition: opacity 300ms cubic-bezier(0.16, 1, 0.3, 1),
              transform 300ms cubic-bezier(0.16, 1, 0.3, 1);
}

.optidocs-section--visible {
  opacity: 1;
  transform: translateY(0);
}
```

In `render()`, add `--visible` class with staggered `setTimeout`:
- Metrics row: 0ms
- Document table: 100ms

**IMPORTANT: Store timeout references for cleanup in `destroy()`:**
```javascript
var _entranceTimeouts = [];

// In render():
_entranceTimeouts.push(setTimeout(function() { ... }, 100));

// In destroy():
_entranceTimeouts.forEach(clearTimeout);
_entranceTimeouts = [];
```

### Sticky Header Implementation

The document table header must remain visible as the user scrolls through document rows:

```css
.optidocs-table-wrapper {
  overflow-y: auto;
  max-height: calc(100% - 120px); /* Subtract metrics row height */
  overscroll-behavior: contain;
  border-radius: var(--radius-sm);
  border: 1px solid var(--border);
}

.optidocs-table__header {
  position: sticky;
  top: 0;
  z-index: 1;
  background: var(--bg-card);
  border-bottom: 1px solid var(--border);
}
```

**Important:** The `overflow-y: auto` container is the `.optidocs-table-wrapper`, NOT the overlay content itself (which already scrolls via `.module-overlay__content`). Since we only have 8 rows, a separate scroll container is technically optional — but implement it correctly for architectural consistency and to demonstrate the pattern for larger datasets.

### Project Structure Notes

- **Single file:** ALL additions go into `index.html`
- **CSS section:** Add after "Component — OptiBiz Module" section (ends ~line 1231), before "Component — Ambient Background" section. Create new section: `/* SECTION: Component — OptiDocs Module */`
- **JS section:** Replace the `OptiPlan.modules.optidocs` placeholder stub at line ~3144
- **No new HTML elements in body:** Module content is rendered dynamically by `render(data)` into the `.module-overlay__content` container
- **Section delimiter format:**
```javascript
// ================================================================
// MODULE: OptiDocs
// ================================================================
```
```css
/* ================================================================ */
/* SECTION: Component — OptiDocs Module                              */
/* ================================================================ */
```

### Anti-Patterns to AVOID

1. **NO hardcoded hex colors** — use `var(--*)` tokens or CSS classes (exception: badge background `rgba()` values are acceptable as specialized status indicators, and `#EF4444` for red status if no CSS custom property exists)
2. **NO anonymous arrow functions** — named functions only for event handlers
3. **NO animating width/height/top/left** — only `transform` and `opacity`
4. **NO new files** — edit `index.html` only
5. **NO functions outside `OptiPlan.*` namespace**
6. **NO emojis** — Tabler Icons only
7. **NO breaking existing** entrance animations, touch feedback, theme toggle, card flip, overlay open/close, idle reset, sidebar nav, OptiTrack module, OptiBiz module, OptiRisk module
8. **NO canvas/D3.js/charting libraries** — vanilla HTML + CSS only
9. **NO hardcoded display values** — ALL numbers from `OPTIDOCS_DATA`
10. **NO `setInterval` for animations** — CSS transitions/animations only
11. **NO growing arrays or orphaned listeners** — memory-safe patterns
12. **NO forgetting `-webkit-` prefixes** for Safari where needed
13. **NO `console.error`** — use `console.warn` for caught errors, `console.log` for state changes
14. **NO multiple module overlays** — only one in DOM at a time (already enforced by Story 2.2)
15. **NO HTML `<table>` element** — use div-based grid layout for consistent styling and sticky header behavior. `<table>` with `position: sticky` on `<thead>` has known issues in Safari.

### CSS Custom Property Rules

- ALL structural colors MUST use `var(--*)` tokens
- Module color set via `--module` local custom property (already set by overlay from Story 2.2 to `var(--optidocs)`)
- Background: `var(--bg-card)` — adapts to dark/light theme
- Text: `var(--text-primary)`, `var(--text-secondary)`, `var(--text-muted)`
- Borders: `var(--border)`
- Spacing: `var(--gap)`, `var(--gap-sm)`, `var(--gap-lg)`
- Radius: `var(--radius)`, `var(--radius-sm)`
- Font: `var(--font-main)` for Varela Round, `var(--font-mono)` for JetBrains Mono
- Chart-specific: `var(--optidocs)` (#4395FD) for module accent
- Status colors: `var(--status-online)` for green, `var(--optibiz)` for amber
- Row backgrounds: `var(--bar-track)` for alternating, `var(--feed-hover)` for hover
- Hover transition: `var(--transition-default)` or `0.15s ease`

### RTL Layout Considerations

- HTML `dir="rtl"` is set on `<html>` — all flex/grid auto-reverses
- Metrics row flows RTL naturally (first metric appears on right)
- CSS grid table: columns auto-reverse in RTL — "מסמך" column appears on the right, "אחראי" on the left
- Hebrew document names and type labels render RTL automatically
- Date format (DD/MM/YY) is LTR numeric — renders fine within RTL context
- Assignee initials are 2-char Hebrew — centered in circle, RTL doesn't affect
- Use `margin-inline-start` / `padding-inline-end` (logical properties) for HTML elements
- Back button positioned via `inset-inline-end` (handled by Story 2.2 overlay)
- Status badges: Hebrew text inside pill renders correctly in RTL

### Safari WebKit Compatibility

- `position: sticky` works in Safari 16.2+ (no `-webkit-sticky` prefix needed since Safari 15+)
- CSS grid: fully supported in Safari 16.2+
- `color-mix()` for metric card backgrounds: Safari 16.2+ (confirmed safe for iPad 10th gen)
- `overscroll-behavior: contain`: supported in Safari 16+
- `overflow-y: auto` with touch scroll: works natively on iPad Safari
- `rgba()` in CSS: fully supported
- Font rendering: Varela Round + JetBrains Mono via Google Fonts CDN
- `border-radius` on nested elements within grid: fully supported

### Previous Story Intelligence

**From Story 2.5 (OptiRisk Module — ready-for-dev, immediately prior):**
- SVG-based heat map pattern — NOT applicable here (OptiDocs uses HTML table, not SVG)
- Metrics summary row pattern: `.optirisk-metrics` with metric cards — **directly reusable layout pattern**
- Badge styling pattern: `.badge--critical`, `.badge--high` etc. with `rgba()` backgrounds — **extend for `.badge--approved`, `.badge--pending`, `.badge--overdue`**
- Entrance animation stagger: 0ms/100ms/200ms with `--visible` class
- `_entranceTimeouts` pattern for cleanup in `destroy()`
- Top risks table uses div-based layout — **confirms div-based table approach** (not HTML `<table>`)
- Row alternating backgrounds: `var(--bar-track)` / transparent
- Row hover: `var(--feed-hover)`

**From Story 2.4 (OptiBiz Module — review):**
- SVG-based bar chart (not applicable — OptiDocs is pure table)
- Financial KPI summary row pattern: `.optibiz-metrics` with metric cards
- Entrance animation stagger pattern: identical
- Currency formatting helpers via `OptiPlan.utils.formatCurrency()` — not needed for OptiDocs
- Date formatting via `OptiPlan.utils.formatDate()` — **directly needed for document dates**

**From Story 2.3 (OptiTrack Module — review):**
- Permit matrix SVG cells — not applicable
- Metrics summary row: `.optitrack-metrics` with 4 metric cards — **template for OptiDocs metrics**
- Activity feed layout: styled rows with avatars, timestamps — **similar pattern to document rows with assignee initials**
- Feed item avatars: colored circle with initials — **reusable for assignee initials circle**
- `color-mix(in srgb, var(--module) 8%, var(--bg-card))` for metric card backgrounds

**From Story 2.2 (Module Expansion Overlay — review):**
- Overlay container fully functional: `OptiPlan.components.moduleOverlay.expand('optidocs')` opens overlay
- Header already renders: "OptiDocs — ניהול מסמכים" with blue accent bar and `ti-file-text` icon
- Content area: `.module-overlay__content` is the render target — your `render(data)` output goes HERE
- Module stub exists: `OptiPlan.modules.optidocs = { init, render, destroy }` — REPLACE the placeholder at line ~3144
- `MODULE_OVERLAY_CONFIG.optidocs.dataKey` = `'OPTIDOCS_DATA'` — overlay calls `render(window[dataKey])`
- Close/destroy lifecycle: overlay calls `OptiPlan.modules[moduleId].destroy()` on close
- `.module-overlay__content` already has `overflow-y: auto` for content scrolling

**From Story 2.1 (Card Flip — done):**
- Card back shows OptiDocs KPIs: 847 total, 94% complete, 38 pending, 13 overdue
- CTA "Full Module" button triggers overlay expand
- Data sourced from `OPTIDOCS_DATA` via `CARD_BACK_CONFIG`

**From Story 1.5 (Stat Cards — done):**
- OptiDocs front card shows: 847 total, sublabel "94% הושלם"
- Uses `var(--optidocs)` (#4395FD) for card gradient
- `STAT_CARD_CONFIG` for OptiDocs: value from `OPTIDOCS_DATA.total`

**From Story 1.2 (Data Model — done):**
- `OPTIDOCS_DATA` is `Object.freeze()`'d — read-only at runtime
- 8 document records with id, name, type, status, date, assignee fields
- Status values are English lowercase strings: 'approved', 'pending', 'overdue'
- Assignee values are 2-character Hebrew initials (e.g., 'ד.ל', 'מ.כ')

**From Story 1.1 (Scaffold — done):**
- `OptiPlan` namespace established with `modules: {}` placeholder
- Section delimiter pattern for code organization
- Design token system in `:root`

### Git Intelligence

**Recent commit pattern (last 5 commits):**
1. `3b4df64` — Complete Story 2.2: Module Expansion Overlay with code review fixes
2. `b00f87e` — Complete Story 2.1: Card Flip Interaction & KPI Back Face with code review fixes
3. `31c439a` — Story 1.6 code review fixes
4. `9457370` — Complete Stories 1.5 & 1.6
5. `56ffa2a` — Complete Story 1.4: Hero Gauges

**Pattern insights:**
- Commit messages follow "Complete Story X.X: Description" format
- Stories 2.3 and 2.4 are in `review` status (code exists but under review)
- All changes go exclusively into `index.html`
- Code review fixes are applied as separate commits after initial implementation
- Each module implementation replaces the placeholder stub from Story 2.2

### Data Consistency Cross-References

| Location | Value | Must Match |
|----------|-------|------------|
| Stat card front (1.5) | 847 total | `OPTIDOCS_DATA.total` |
| Stat card front (1.5) | "94% הושלם" | `OPTIDOCS_DATA.completionRate` |
| Card back KPI (2.1) | 847 total docs | `OPTIDOCS_DATA.total` |
| Card back KPI (2.1) | 94% completion | `OPTIDOCS_DATA.completionRate` |
| Card back KPI (2.1) | 38 pending | `OPTIDOCS_DATA.pending` |
| Card back KPI (2.1) | 13 overdue | `OPTIDOCS_DATA.overdue` |
| Module metrics (this story) | 847, 94%, 38, 13 | All from `OPTIDOCS_DATA` |
| Document rows | 8 entries | `OPTIDOCS_DATA.documents.length` |
| Document statuses | 6 approved, 1 pending, 1 overdue | Count by status in `.documents` |
| Approved ÷ Total | 796 / 847 = 94% | `OPTIDOCS_DATA.approved / .total` |

**CRITICAL: Metric values validation:**
- Total: 847 ✓ (from `OPTIDOCS_DATA.total`)
- Completion rate: 94% ✓ (from `OPTIDOCS_DATA.completionRate`, consistent with 796/847)
- Pending: 38 ✓ (from `OPTIDOCS_DATA.pending`)
- Overdue: 13 ✓ (from `OPTIDOCS_DATA.overdue`)

### Existing OptiDocs Configuration in Codebase

**Stat Card Config (line ~2225):**
```javascript
{ key: 'optidocs', name: 'OptiDocs', icon: 'ti-file-text',
  value: OPTIDOCS_DATA.total, label: 'מסמכים',
  sublabel: OPTIDOCS_DATA.completionRate + '% הושלם' }
```

**Card Back Config (line ~2275):**
```javascript
optidocs: {
  title: 'OptiDocs',
  kpis: [
    { label: 'סה"כ מסמכים', getValue: function() { return OPTIDOCS_DATA.total; } },
    { label: 'אחוז השלמה', getValue: function() { return OPTIDOCS_DATA.completionRate + '%'; } },
    { label: 'ממתינים', getValue: function() { return OPTIDOCS_DATA.pending; } },
    { label: 'באיחור', getValue: function() { return OPTIDOCS_DATA.overdue; } }
  ]
}
```

**Module Overlay Config (line ~2318):**
```javascript
optidocs: {
  title: 'OptiDocs',
  subtitle: 'ניהול מסמכים',
  icon: 'ti-file-text',
  dataKey: 'OPTIDOCS_DATA'
}
```

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Epic-2-Story-2.6]
- [Source: _bmad-output/planning-artifacts/architecture.md#Data-Visualization]
- [Source: _bmad-output/planning-artifacts/architecture.md#Component-Architecture]
- [Source: _bmad-output/planning-artifacts/architecture.md#Internal-Structure-of-index.html]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Component-11-Document-Table]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Layer-3-Module-Expansion]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Data-Display-Patterns]
- [Source: _bmad-output/planning-artifacts/prd.md#FR24-FR25]
- [Source: _bmad-output/implementation-artifacts/2-5-optirisk-module-risk-heat-map-top-risks-table.md]
- [Source: _bmad-output/implementation-artifacts/2-4-optibiz-module-cash-flow-chart-financial-kpis.md]
- [Source: _bmad-output/implementation-artifacts/2-3-optitrack-module-permit-matrix-activity-feed.md]
- [Source: _bmad-output/implementation-artifacts/2-2-module-expansion-overlay-container-layer-3.md]
- [Source: _bmad-output/implementation-artifacts/2-1-card-flip-interaction-kpi-back-face-layer-2.md]
- [Source: index.html — OPTIDOCS_DATA definition (line ~1815)]
- [Source: index.html — OptiPlan.modules.optidocs stub (line ~3144)]
- [Source: index.html — MODULE_OVERLAY_CONFIG.optidocs (line ~2318)]
- [Source: index.html — CARD_BACK_CONFIG.optidocs (line ~2275)]
- [Source: index.html — STAT_CARD_CONFIG for optidocs (line ~2225)]

## Dev Agent Record

### Agent Model Used

Claude Opus 4.6

### Debug Log References

No debug issues encountered during implementation.

### Completion Notes List

- Implemented full OptiDocs module replacing Story 2.2 placeholder stub
- Added CSS section (lines 1475-1634) after OptiRisk Module, before Ambient Background
- Replaced JS stub (previously lines 3564-3580) with full init/render/destroy implementation (lines 3726-3822)
- Metrics summary row renders 4 cards (total, completion rate, pending, overdue) — all from `data` parameter
- Document tracking table with CSS grid layout (5 columns: 2fr 1fr 1fr 1fr 0.6fr)
- Status badges: approved (green + אושר), pending (amber + ממתין), overdue (red + באיחור)
- Sticky header with `position: sticky; top: 0` within scrollable wrapper
- Assignee initials displayed in colored circle (28px, optidocs accent)
- Staggered entrance animation: metrics at 0ms, table at 100ms
- `_entranceTimeouts` array tracks all setTimeout refs, cleaned up in `destroy()`
- All CSS uses `var(--*)` design tokens, no hardcoded hex (except allowed #EF4444 for red status)
- All display values sourced from `OPTIDOCS_DATA` — zero hardcoded numbers
- Existing modules (OptiTrack, OptiBiz, OptiRisk, OptiGantt stub) remain unmodified

### Change Log

- 2026-02-23: Implemented Story 2.6 — OptiDocs Module with document tracking table, metrics summary, status badges, and staggered entrance animations
- 2026-02-23: Code review fixes — Added render() timeout cleanup, prefers-reduced-motion CSS/JS support, date column color fix, light theme pending badge contrast, will-change on animated elements, named setTimeout callback

### File List

- index.html (modified) — Added OptiDocs CSS section (metrics, table, badges, assignee, entrance animation), replaced JS placeholder stub with full init/render/destroy implementation, added `--feed-hover` design token to `:root`, added `MODULE_DATA` lookup map
