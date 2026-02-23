# Story 2.5: OptiRisk Module — Risk Heat Map & Top Risks Table

Status: ready-for-dev

## Story

As an investor,
I want to see the full OptiRisk module with a risk severity heat map and top risks table,
so that I can assess the project's risk exposure and the platform's risk management maturity.

## Acceptance Criteria

1. **Given** the module overlay is open for OptiRisk
   **When** `OptiPlan.modules.optirisk.render(OPTIRISK_DATA)` is called
   **Then** a 5×5 risk heat map renders with probability as rows (Hebrew labels: נמוכה → גבוהה) and impact as columns (Hebrew labels: נמוכה → קריטי)
   **And** cell colors transition from green (low probability/low impact) through yellow and orange to red (high probability/high impact)
   **And** each cell displays the count of risks in that severity zone

2. **Given** the heat map is rendered
   **When** a developer inspects the implementation
   **Then** cells are SVG `<rect>` elements with `rx="var(--radius-sm)"`
   **And** a color-scale legend renders alongside the heat map mapping color bands to severity labels
   **And** color is always paired with a numeric label — never color alone as the sole indicator

3. **Given** the OptiRisk overlay renders
   **When** a developer inspects the top risks table
   **Then** 5+ risk entries render with: Hebrew risk name, severity badge (colored + text), trend arrow (↑/↓ or →), and mitigation status
   **And** the total count (23 active, 8 critical) matches `OPTIRISK_DATA` and the stat card value from Epic 1
   **And** `OptiPlan.modules.optirisk` follows the `init()`, `render(data)`, `destroy()` pattern

## Tasks / Subtasks

- [ ] Task 1: Add risk metrics summary row (AC: #3)
  - [ ] 1.1 Create `.optirisk-metrics` container with 3-4 metric cards
  - [ ] 1.2 Metric 1: Active risks — `OPTIRISK_DATA.activeRisks` (23)
  - [ ] 1.3 Metric 2: Critical risks — `OPTIRISK_DATA.critical` (8)
  - [ ] 1.4 Metric 3: Heat map summary — total cells occupied / total possible
  - [ ] 1.5 All values from `OPTIRISK_DATA` — zero hardcoded numbers
  - [ ] 1.6 Follow `.optitrack-metrics` pattern from Story 2.3

- [ ] Task 2: Implement 5×5 risk heat map SVG (AC: #1, #2)
  - [ ] 2.1 Create `<svg>` with responsive `viewBox` (e.g., `0 0 400 340`)
  - [ ] 2.2 Render 5 probability row labels (Hebrew, right side for RTL): נמוכה, נמוכה-בינונית, בינונית, בינונית-גבוהה, גבוהה — from `OPTIRISK_DATA.probLabels`
  - [ ] 2.3 Render 5 impact column labels (Hebrew, bottom): נמוכה, נמוכה-בינונית, בינונית, גבוהה, קריטי — from `OPTIRISK_DATA.impactLabels`
  - [ ] 2.4 Render 25 `<rect>` cells with `rx="4"` (matches `--radius-sm` in SVG units)
  - [ ] 2.5 Color each cell based on severity zone (row + col index determines color)
  - [ ] 2.6 Add `<text>` with risk count inside each cell — from `OPTIRISK_DATA.heatMap[row][col]`
  - [ ] 2.7 Cells with count 0 show "0" in muted color (not empty)

- [ ] Task 3: Implement heat map color gradient (AC: #1, #2)
  - [ ] 3.1 Define severity-to-color mapping using CSS classes on SVG `<rect>`:
    - Zone 0 (low-low): green (`var(--status-online)` / `#22C55E`)
    - Zone 1 (low-med): yellow-green
    - Zone 2 (medium): amber (`var(--optibiz)` / `#F6AE2D`)
    - Zone 3 (high): orange (`var(--optirisk)` / `#FF7A3C`)
    - Zone 4 (critical): red (`#EF4444` or `var(--status-danger)`)
  - [ ] 3.2 Compute severity zone: `zone = Math.min(4, Math.floor((row + col) * 4 / 8))` or similar mapping where top-right = highest severity
  - [ ] 3.3 Apply via CSS classes: `.risk-cell--zone-0` through `.risk-cell--zone-4`

- [ ] Task 4: Add color-scale legend (AC: #2)
  - [ ] 4.1 Render horizontal or vertical legend below/beside heat map
  - [ ] 4.2 5 colored swatches with Hebrew labels: נמוך, נמוך-בינוני, בינוני, גבוה, קריטי
  - [ ] 4.3 Use same CSS classes as heat map cells for color consistency
  - [ ] 4.4 Legend uses `var(--text-secondary)` text, `var(--font-main)` font

- [ ] Task 5: Implement top risks table (AC: #3)
  - [ ] 5.1 Create `.optirisk-table` container with table-like rows
  - [ ] 5.2 Each row: Hebrew risk name, severity badge, trend arrow, mitigation status
  - [ ] 5.3 Source all 6 risks from `OPTIRISK_DATA.topRisks`
  - [ ] 5.4 Severity badge: colored pill with Hebrew text
    - `critical` → red badge, "קריטי"
    - `high` → orange badge, "גבוה"
    - `medium` → amber badge, "בינוני"
    - `low` → green badge, "נמוך"
  - [ ] 5.5 Trend arrow: ↑ (up/red), ↓ (down/green), → (stable/muted)
  - [ ] 5.6 Mitigation status badge:
    - `active` → blue badge, "טיפול פעיל"
    - `monitoring` → amber badge, "ניטור"
    - `resolved` → green badge, "טופל"
    - `legal-review` → purple/muted badge, "בדיקה משפטית"
  - [ ] 5.7 Alternating row backgrounds: `var(--bar-track)` / transparent
  - [ ] 5.8 Row hover: `var(--feed-hover)`

- [ ] Task 6: Implement OptiPlan.modules.optirisk namespace (AC: #3)
  - [ ] 6.1 Create `init()` method — cache DOM refs with `$` prefix
  - [ ] 6.2 Create `render(data)` method — generates all HTML for metrics + heat map + table
  - [ ] 6.3 Create `destroy()` method — clear timeouts, clear innerHTML
  - [ ] 6.4 Replace placeholder stub from Story 2.2 with full implementation

- [ ] Task 7: Staggered content entrance animation
  - [ ] 7.1 Metrics row fades in first (0ms delay)
  - [ ] 7.2 Heat map fades in second (100ms delay)
  - [ ] 7.3 Top risks table fades in third (200ms delay)
  - [ ] 7.4 Use `opacity: 0→1` + `translateY(12px)→0` over 300ms
  - [ ] 7.5 Apply `--visible` class via staggered `setTimeout` in `render()`

- [ ] Task 8: Add CSS section for OptiRisk module
  - [ ] 8.1 Add new CSS section: `/* SECTION: Component — OptiRisk Module */` after OptiBiz Module CSS
  - [ ] 8.2 Heat map cell styles with zone color classes
  - [ ] 8.3 Risk table row styles with alternating backgrounds
  - [ ] 8.4 Severity badge and mitigation badge pill styles
  - [ ] 8.5 Trend arrow color styles
  - [ ] 8.6 Entrance animation classes (`.optirisk-section` + `.optirisk-section--visible`)
  - [ ] 8.7 All colors via `var(--*)` tokens — zero hardcoded hex

- [ ] Task 9: Verify and test all acceptance criteria
  - [ ] 9.1 Zero console errors on overlay open/close
  - [ ] 9.2 Heat map renders 5×5 grid with correct risk counts from `OPTIRISK_DATA.heatMap`
  - [ ] 9.3 Cell colors follow green-to-red gradient based on severity zone
  - [ ] 9.4 Every cell has a numeric count label — no color-only indicators
  - [ ] 9.5 Color-scale legend renders with labeled swatches
  - [ ] 9.6 Top risks table shows 6 entries from `OPTIRISK_DATA.topRisks`
  - [ ] 9.7 Severity badges show correct Hebrew text + color
  - [ ] 9.8 Trend arrows render correctly (↑ red, ↓ green, → muted)
  - [ ] 9.9 Mitigation status badges render with correct Hebrew text + color
  - [ ] 9.10 Active risks count (23) and critical count (8) match `OPTIRISK_DATA` and stat card
  - [ ] 9.11 Theme toggle works (dark/light) on all new elements
  - [ ] 9.12 Hebrew month labels and text render correctly in RTL
  - [ ] 9.13 Existing card flip, overlay open/close, sidebar nav unbroken
  - [ ] 9.14 Touch targets meet 44px minimum
  - [ ] 9.15 No hardcoded hex values in new CSS

## Dev Notes

### Architecture Patterns & Constraints

**Single File Architecture:** ALL code goes into `index.html`. No separate JS/CSS files.

**Module Namespace Pattern (MUST follow exactly):**
```javascript
// ================================================================
// MODULE: OptiRisk
// ================================================================
OptiPlan.modules.optirisk = {
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

**This replaces the placeholder stub** created in Story 2.2. The stub currently renders a centered icon + "Content coming in Story 2.5". You MUST replace it entirely with the full implementation.

**Data Source — `OPTIRISK_DATA` (already defined in Story 1.2, Object.freeze'd):**
```javascript
{
  activeRisks: 23,
  critical: 8,
  heatMap: [
    [0, 1, 0, 0, 0],   // row 0 = probability "נמוכה" (low)
    [1, 2, 1, 0, 0],   // row 1 = "נמוכה-בינונית"
    [0, 2, 2, 1, 0],   // row 2 = "בינונית"
    [0, 1, 3, 2, 2],   // row 3 = "בינונית-גבוהה"
    [0, 0, 1, 2, 2]    // row 4 = "גבוהה" (high)
  ],
  // heatMap[row][col]: row = probability index, col = impact index
  // Sum of all cells = 23 (matches activeRisks)
  probLabels: ['נמוכה', 'נמוכה-בינונית', 'בינונית', 'בינונית-גבוהה', 'גבוהה'],
  impactLabels: ['נמוכה', 'נמוכה-בינונית', 'בינונית', 'גבוהה', 'קריטי'],
  topRisks: [
    { id: 1, name: 'עיכובי רשות העתיקות — קטע א4', severity: 'critical', trend: 'up', mitigation: 'active', prob: 4, impact: 5 },
    { id: 2, name: 'עלייה בעלות חומרי גלם וחציבה', severity: 'critical', trend: 'up', mitigation: 'monitoring', prob: 5, impact: 4 },
    { id: 3, name: 'מחלוקות קנין — קטע א3', severity: 'critical', trend: 'stable', mitigation: 'legal-review', prob: 4, impact: 4 },
    { id: 4, name: 'מחסור בכוח אדם מהנדסים', severity: 'high', trend: 'down', mitigation: 'resolved', prob: 3, impact: 4 },
    { id: 5, name: 'סיכוני ממשק בין-קבלני — קטע א2/א3', severity: 'high', trend: 'up', mitigation: 'active', prob: 4, impact: 3 },
    { id: 6, name: 'אי עמידה בלוח זמנים — קטע א5', severity: 'high', trend: 'up', mitigation: 'monitoring', prob: 5, impact: 3 }
  ]
}
```

**Data integrity check:** Sum of all `heatMap` cells: 0+1+0+0+0 + 1+2+1+0+0 + 0+2+2+1+0 + 0+1+3+2+2 + 0+0+1+2+2 = 1+4+5+8+5 = 23 = `activeRisks` ✓

**Critical risks check:** Cells at high probability AND high impact (zone 4, roughly row>=3 AND col>=3): heatMap[3][3]=2, heatMap[3][4]=2, heatMap[4][3]=2, heatMap[4][4]=2 → 8 = `critical` ✓

### Heat Map Implementation Details

**SVG-Based 5×5 Grid (NOT HTML table, NOT canvas):**

```
viewBox example: "0 0 420 360"

Layout:
- Row labels area (~100px): probability labels on the left (Hebrew, RTL will position naturally)
- Column labels area (~50px): impact labels at the bottom
- Grid area: 5 columns × 5 rows of colored <rect> cells
- Legend area: below grid
```

**Cell Sizing Calculation:**
```
gridStartX = 110   (space for row labels)
gridStartY = 10    (top padding)
cellWidth = 52
cellHeight = 44
cellGap = 4
gridWidth = 5 * (cellWidth + cellGap) - cellGap = 5*56 - 4 = 276
gridHeight = 5 * (cellHeight + cellGap) - cellGap = 5*48 - 4 = 236

For each cell at [row, col]:
  x = gridStartX + col * (cellWidth + cellGap)
  y = gridStartY + (4 - row) * (cellHeight + cellGap)
  // IMPORTANT: row 0 = low probability = BOTTOM, row 4 = high probability = TOP
  // So invert: visual Y = (4 - dataRow)
```

**CRITICAL: Heat Map Orientation.**
- Y-axis (rows) = Probability: LOW at bottom, HIGH at top
- X-axis (cols) = Impact: LOW at left, CRITICAL at right
- `heatMap[0]` = probability "נמוכה" (low) = bottom row visually
- `heatMap[4]` = probability "גבוהה" (high) = top row visually
- Cell color = severity zone based on (row + col) combination

**Color Zone Mapping:**
```javascript
// Severity zone based on sum of row and col indices (0-8 range)
// Map to 5 color zones:
function getZone(row, col) {
  var sum = row + col;
  if (sum <= 1) return 0;       // green (low risk)
  if (sum <= 3) return 1;       // yellow-green (low-medium)
  if (sum <= 4) return 2;       // amber (medium)
  if (sum <= 6) return 3;       // orange (high)
  return 4;                      // red (critical)
}
```

**CSS Classes for Heat Map Cell Colors:**
```css
.risk-cell--zone-0 { fill: #22C55E; }  /* green - use var(--status-online) */
.risk-cell--zone-1 { fill: #84CC16; }  /* yellow-green */
.risk-cell--zone-2 { fill: #F6AE2D; }  /* amber - use var(--optibiz) */
.risk-cell--zone-3 { fill: #FF7A3C; }  /* orange - use var(--optirisk) */
.risk-cell--zone-4 { fill: #EF4444; }  /* red */
```

Note: Zones 1 and 4 don't have existing CSS custom properties. Define them as new tokens if possible, or use CSS classes with specific fill values. Since SVG fills via CSS classes work in Safari 16.2+, prefer CSS classes for theming:
```css
.risk-cell--zone-0 { fill: var(--status-online); }
.risk-cell--zone-1 { fill: #84CC16; }  /* no existing token, acceptable as intermediate */
.risk-cell--zone-2 { fill: var(--optibiz); }
.risk-cell--zone-3 { fill: var(--optirisk); }
.risk-cell--zone-4 { fill: #EF4444; }  /* add as --status-danger if needed */
```

**IMPORTANT: If `--status-danger` or similar red token doesn't exist, check `:root` first. If not available, define one or use a class-specific fill. The intermediate colors (#84CC16 and #EF4444) are acceptable as specialized heat map colors since they only appear in this one context.**

**Cell Text (Risk Count):**
```html
<text x="{cellCenterX}" y="{cellCenterY + 4}"
      text-anchor="middle"
      fill="{countColor}"
      font-family="var(--font-mono)"
      font-size="14"
      font-weight="700">{count}</text>
```
- Count > 0: white text (`#FFFFFF`) on colored background
- Count = 0: muted text (`var(--text-muted)`) — still visible, just de-emphasized

**Row Labels (Probability — Hebrew):**
```html
<text x="{gridStartX - 8}" y="{cellCenterY + 4}"
      text-anchor="end"
      fill="var(--text-secondary)"
      font-family="var(--font-main)"
      font-size="11">{probLabel}</text>
```

**Column Labels (Impact — Hebrew):**
```html
<text x="{cellCenterX}" y="{gridBottom + 20}"
      text-anchor="middle"
      fill="var(--text-secondary)"
      font-family="var(--font-main)"
      font-size="11">{impactLabel}</text>
```

**Axis Titles:**
- Y-axis title (rotated): "סבירות" (Probability) — left side, rotated 90° or positioned as a label above the row labels
- X-axis title: "השפעה" (Impact) — centered below column labels

### Top Risks Table Implementation Details

**Table Structure (HTML, not SVG):**
```html
<div class="optirisk-table">
  <div class="optirisk-table__header">
    <span class="optirisk-table__col optirisk-table__col--name">סיכון</span>
    <span class="optirisk-table__col optirisk-table__col--severity">חומרה</span>
    <span class="optirisk-table__col optirisk-table__col--trend">מגמה</span>
    <span class="optirisk-table__col optirisk-table__col--mitigation">סטטוס טיפול</span>
  </div>
  <!-- Rows generated from OPTIRISK_DATA.topRisks -->
</div>
```

**Risk Row Structure:**
```html
<div class="optirisk-table__row optirisk-table__row--alt">
  <span class="optirisk-table__col optirisk-table__col--name">{risk.name}</span>
  <span class="optirisk-table__col optirisk-table__col--severity">
    <span class="badge badge--{severity}">{severityLabel}</span>
  </span>
  <span class="optirisk-table__col optirisk-table__col--trend">
    <span class="trend trend--{direction}">{arrow}</span>
  </span>
  <span class="optirisk-table__col optirisk-table__col--mitigation">
    <span class="badge badge--{mitigationType}">{mitigationLabel}</span>
  </span>
</div>
```

**Severity Badge Mapping:**
```
critical → red background + white text, "קריטי"
high     → orange background + white text, "גבוה"
medium   → amber background + dark text, "בינוני"
low      → green background + white text, "נמוך"
```

**Trend Arrow Mapping:**
```
up     → "↑" in red/orange (var(--optirisk) or var(--status-danger))
down   → "↓" in green (var(--status-online))
stable → "→" in muted (var(--text-muted))
```

**Mitigation Status Badge Mapping:**
```
active       → blue tint background, "טיפול פעיל"
monitoring   → amber tint background, "ניטור"
resolved     → green tint background, "טופל"
legal-review → muted/purple tint background, "בדיקה משפטית"
```

**Badge Styling:**
```css
.badge {
  display: inline-block;
  padding: 2px 8px;
  border-radius: var(--radius-sm);
  font-family: var(--font-main);
  font-size: 11px;
  font-weight: 600;
  white-space: nowrap;
}

.badge--critical { background: rgba(239, 68, 68, 0.2); color: #EF4444; }
.badge--high { background: rgba(255, 122, 60, 0.2); color: var(--optirisk); }
.badge--medium { background: rgba(246, 174, 45, 0.2); color: var(--optibiz); }
.badge--low { background: rgba(34, 197, 94, 0.2); color: var(--status-online); }

.badge--active { background: rgba(9, 145, 243, 0.2); color: var(--optitrack); }
.badge--monitoring { background: rgba(246, 174, 45, 0.15); color: var(--optibiz); }
.badge--resolved { background: rgba(34, 197, 94, 0.15); color: var(--status-online); }
.badge--legal-review { background: rgba(160, 164, 184, 0.2); color: var(--text-secondary); }
```

Note: Using `rgba()` with specific colors for badge backgrounds is acceptable since these are specialized status indicators. The text colors still reference CSS custom properties where possible.

### Metrics Summary Row

**Follow the exact pattern from Story 2.3 (OptiTrack):**
```html
<div class="optirisk-metrics optirisk-section">
  <div class="optirisk-metric">
    <span class="optirisk-metric__value">{activeRisks}</span>
    <span class="optirisk-metric__label">סיכונים פעילים</span>
  </div>
  <div class="optirisk-metric">
    <span class="optirisk-metric__value optirisk-metric__value--critical">{critical}</span>
    <span class="optirisk-metric__label">קריטיים</span>
  </div>
  <div class="optirisk-metric">
    <span class="optirisk-metric__value">{topRisks.length}</span>
    <span class="optirisk-metric__label">סיכונים מובילים</span>
  </div>
</div>
```

**Metric value styling:** JetBrains Mono Bold, large size (~22-24px), `var(--text-primary)`. Critical value uses `color: var(--optirisk)` or red to draw attention.

### Content Entrance Animation Pattern

Follow the exact stagger pattern from Stories 2.3 and 2.4:
```css
.optirisk-section {
  opacity: 0;
  transform: translateY(12px);
  transition: opacity 300ms cubic-bezier(0.16, 1, 0.3, 1),
              transform 300ms cubic-bezier(0.16, 1, 0.3, 1);
}

.optirisk-section--visible {
  opacity: 1;
  transform: translateY(0);
}
```

In `render()`, add `--visible` class with staggered `setTimeout`:
- Metrics row: 0ms
- Heat map + legend: 100ms
- Top risks table: 200ms

**IMPORTANT: Store timeout references for cleanup in `destroy()`:**
```javascript
var _entranceTimeouts = [];

// In render():
_entranceTimeouts.push(setTimeout(function() { ... }, 100));

// In destroy():
_entranceTimeouts.forEach(clearTimeout);
_entranceTimeouts = [];
```

### Project Structure Notes

- **Single file:** ALL additions go into `index.html`
- **CSS section:** Add after "Component — OptiBiz Module" section, create new section: "Component — OptiRisk Module"
- **JS section:** Replace the `OptiPlan.modules.optirisk` placeholder stub
- **No new HTML elements in body:** Module content is rendered dynamically by `render(data)` into the `.module-overlay__content` container
- **Section delimiter format:**
```javascript
// ================================================================
// MODULE: OptiRisk
// ================================================================
```
```css
/* ================================================================ */
/* SECTION: Component — OptiRisk Module                               */
/* ================================================================ */
```

### Anti-Patterns to AVOID

1. **NO hardcoded hex colors** — use `var(--*)` tokens or CSS classes on SVG elements (exception: intermediate heat map gradient colors #84CC16 and #EF4444 are acceptable as specialized fills, but wrap in CSS classes not inline styles)
2. **NO anonymous arrow functions** — named functions only for event handlers
3. **NO animating width/height/top/left** — only `transform` and `opacity`
4. **NO new files** — edit `index.html` only
5. **NO functions outside `OptiPlan.*` namespace**
6. **NO emojis** — Tabler Icons only
7. **NO breaking existing** entrance animations, touch feedback, theme toggle, card flip, overlay open/close, idle reset, sidebar nav, OptiTrack module, OptiBiz module
8. **NO canvas/D3.js/charting libraries** — vanilla SVG + HTML only
9. **NO hardcoded display values** — ALL numbers from `OPTIRISK_DATA`
10. **NO HTML `<canvas>` element** — SVG `<rect>`, `<text>`, `<line>` only
11. **NO `setInterval` for animations** — CSS transitions/animations only
12. **NO growing arrays or orphaned listeners** — memory-safe patterns
13. **NO forgetting `-webkit-` prefixes** for Safari where needed
14. **NO `console.error`** — use `console.warn` for caught errors, `console.log` for state changes
15. **NO multiple module overlays** — only one in DOM at a time (already enforced by Story 2.2)
16. **NO HTML `<table>` for heat map** — use SVG `<rect>` per AC requirement. HTML div-based table layout is fine for the top risks table.

### CSS Custom Property Rules

- ALL structural colors MUST use `var(--*)` tokens
- Module color set via `--module` local custom property (already set by overlay from Story 2.2 to `var(--optirisk)`)
- Background: `var(--bg-card)` — adapts to dark/light theme
- Text: `var(--text-primary)`, `var(--text-secondary)`, `var(--text-muted)`
- Borders: `var(--border)`
- Spacing: `var(--gap)`, `var(--gap-sm)`, `var(--gap-lg)`
- Radius: `var(--radius)`, `var(--radius-sm)`
- Font: `var(--font-main)` for Varela Round, `var(--font-mono)` for JetBrains Mono
- Chart-specific: `var(--optirisk)` (#FF7A3C) for module accent
- Status colors: `var(--status-online)` for green, `var(--optibiz)` for amber, `var(--optirisk)` for orange
- Heat map intermediate colors via CSS classes (not inline styles)

### RTL Layout Considerations

- HTML `dir="rtl"` is set on `<html>` — all flex/grid auto-reverses
- Metrics row flows RTL naturally (first metric appears on right)
- SVG heat map: coordinates are absolute (not affected by RTL) — the grid renders left-to-right for impact which is correct (low impact left → critical right) regardless of text direction
- Row labels (probability): positioned to the left of the grid in SVG space. In RTL HTML context the SVG itself is within the overlay content which flows RTL, but SVG internal coordinates are LTR
- Hebrew text in SVG `<text>` elements renders RTL automatically within text bounds
- Top risks table: flex/grid rows auto-reverse in RTL — Hebrew risk names appear on the right side naturally
- Use `margin-inline-start` / `padding-inline-end` (logical properties) for HTML elements
- Back button positioned via `inset-inline-end` (handled by Story 2.2 overlay)

### Safari WebKit Compatibility

- SVG `<rect>` with CSS class-based fills works in Safari 16.2+
- `rx` attribute on `<rect>` for rounded corners supported
- CSS custom properties in SVG (via classes) supported
- SVG `<text>` with `font-family` attribute renders correctly
- `overflow-y: auto` on content container (no deprecated `-webkit-overflow-scrolling`)
- `overscroll-behavior: contain` on scroll container
- Font rendering: Varela Round + JetBrains Mono via Google Fonts CDN
- `rgba()` in CSS fully supported

### Previous Story Intelligence

**From Story 2.4 (OptiBiz Module — ready-for-dev, immediately prior):**
- SVG-based bar chart pattern established — confirms SVG `<rect>` with CSS class fills works
- Financial KPI summary row pattern: `.optibiz-metrics` with metric cards
- Entrance animation stagger pattern: 0ms/100ms/200ms with `--visible` class
- `render(data)` populates `.module-overlay__content` via innerHTML
- Dual y-axis approach for charts (may not apply here but shows SVG complexity level)
- Currency formatting helpers (not needed for OptiRisk)
- `_entranceTimeouts` pattern for cleanup

**From Story 2.3 (OptiTrack Module — review):**
- SVG permit matrix uses `<rect>` cells with CSS class-based fills — **directly reusable pattern for heat map cells**
- Color mapping via CSS classes: `.permit-cell--approved`, `.permit-cell--pending`, etc. — follow same pattern for `.risk-cell--zone-N`
- Metrics summary row: `.optitrack-metrics` with 4 metric cards — reuse layout pattern
- Status badge color mapping: `approved=green, pending=amber, objection=orange` — extend for severity and mitigation badges
- Activity feed layout pattern — not directly applicable but confirms div-based row layout approach for table
- Entrance animation stagger: same pattern (0ms, 100ms, 200ms)
- Memory-safe destroy: clears timeouts and innerHTML

**From Story 2.2 (Module Expansion Overlay — review):**
- Overlay container fully functional: `OptiPlan.components.moduleOverlay.expand('optirisk')` opens overlay
- Header already renders: "OptiRisk — ניהול סיכונים" with orange accent bar and `ti-alert-triangle` icon
- Content area: `.module-overlay__content` is the render target — your `render(data)` output goes HERE
- Module stub exists: `OptiPlan.modules.optirisk = { init, render, destroy }` — REPLACE the placeholder
- `MODULE_OVERLAY_CONFIG.optirisk.dataKey` = `'OPTIRISK_DATA'` — overlay calls `render(window[dataKey])`
- Close/destroy lifecycle: overlay calls `OptiPlan.modules[moduleId].destroy()` on close

**From Story 2.1 (Card Flip — done):**
- Card back shows OptiRisk KPIs: 23 active risks, 8 critical
- CTA "Full Module" button triggers overlay expand
- Data sourced from `OPTIRISK_DATA` via `CARD_BACK_CONFIG`

**From Story 1.5 (Stat Cards — done):**
- OptiRisk front card shows: 23 active, 8 critical sublabel
- Uses `var(--optirisk)` (#FF7A3C) for card gradient
- `STAT_CARD_CONFIG` for OptiRisk: value from `OPTIRISK_DATA.activeRisks`

**From Story 1.4 (Hero Gauges — done):**
- No direct OptiRisk gauge (gauges are financial, coordination, schedule)
- SVG `<circle>` pattern confirms SVG proficiency in codebase

**From Story 1.2 (Data Model — done):**
- `OPTIRISK_DATA` is `Object.freeze()`'d — read-only at runtime
- `heatMap` is a 5×5 array of numbers (risk counts per cell)
- `topRisks` has 6 entries with id, name, severity, trend, mitigation, prob, impact
- `probLabels` and `impactLabels` arrays define Hebrew axis labels

**From Story 1.1 (Scaffold — done):**
- `OptiPlan` namespace established with `modules: {}` placeholder
- Section delimiter pattern for code organization
- Design token system in `:root`

### Data Consistency Cross-References

| Location | Value | Must Match |
|----------|-------|------------|
| Stat card front (1.5) | 23 active | `OPTIRISK_DATA.activeRisks` |
| Stat card front (1.5) | 8 critical | `OPTIRISK_DATA.critical` |
| Card back KPI (2.1) | 23 active risks | `OPTIRISK_DATA.activeRisks` |
| Card back KPI (2.1) | 8 critical | `OPTIRISK_DATA.critical` |
| Module metrics (this story) | 23 active, 8 critical | `OPTIRISK_DATA.activeRisks`, `.critical` |
| Heat map cell sum | 23 total | Sum of all `OPTIRISK_DATA.heatMap[][]` cells |
| Heat map critical cells | 8 critical | Sum of cells in high-probability + high-impact zone |
| Top risks count | 6 entries | `OPTIRISK_DATA.topRisks.length` |
| Top risks severity | 3 critical, 3 high | Count of severity values in `topRisks` |

**CRITICAL: Heat map cell sum validation:**
Row 0: 0+1+0+0+0 = 1
Row 1: 1+2+1+0+0 = 4
Row 2: 0+2+2+1+0 = 5
Row 3: 0+1+3+2+2 = 8
Row 4: 0+0+1+2+2 = 5
Total: 1+4+5+8+5 = **23 ✓** (matches `activeRisks`)

**CRITICAL: Critical risk count validation:**
"Critical" zone = high probability (rows 3-4) AND high impact (cols 3-4):
heatMap[3][3]=2, heatMap[3][4]=2, heatMap[4][3]=2, heatMap[4][4]=2
Sum: 2+2+2+2 = **8 ✓** (matches `critical`)

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Epic-2-Story-2.5]
- [Source: _bmad-output/planning-artifacts/architecture.md#Data-Visualization]
- [Source: _bmad-output/planning-artifacts/architecture.md#Component-Architecture]
- [Source: _bmad-output/planning-artifacts/architecture.md#Animation-System]
- [Source: _bmad-output/planning-artifacts/architecture.md#Internal-Structure-of-index.html]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Component-9-Risk-Heat-Map]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Layer-3-Module-Expansion]
- [Source: _bmad-output/planning-artifacts/prd.md#FR22-FR23]
- [Source: _bmad-output/implementation-artifacts/2-4-optibiz-module-cash-flow-chart-financial-kpis.md]
- [Source: _bmad-output/implementation-artifacts/2-3-optitrack-module-permit-matrix-activity-feed.md]
- [Source: _bmad-output/implementation-artifacts/2-2-module-expansion-overlay-container-layer-3.md]
- [Source: _bmad-output/implementation-artifacts/2-1-card-flip-interaction-kpi-back-face-layer-2.md]

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
