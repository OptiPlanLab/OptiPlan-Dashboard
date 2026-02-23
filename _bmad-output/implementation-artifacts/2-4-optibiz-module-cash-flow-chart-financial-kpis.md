# Story 2.4: OptiBiz Module — Cash Flow Chart & Financial KPIs

Status: ready-for-dev

## Story

As an investor,
I want to see the full OptiBiz module with a 12-month cash flow chart and financial summary,
so that I can assess the project's financial health and understand the platform's financial planning capability.

## Acceptance Criteria

1. **Given** the module overlay is open for OptiBiz
   **When** `OptiPlan.modules.optibiz.render(OPTIBIZ_DATA)` is called
   **Then** a financial KPI row renders: ₪1.22B utilized of ₪1.8B total (68%), burn rate, and variance indicator — all sourced from `OPTIBIZ_DATA`
   **And** the ₪1.8B total cross-references `PROJECT_DATA.totalBudget` (same constant)

2. **Given** the OptiBiz overlay renders
   **When** a developer inspects the cash flow chart
   **Then** a 12-month grouped bar chart renders with plan bars (`var(--optibiz)` at 40% opacity) and actual bars (solid `var(--optibiz)`)
   **And** Hebrew month abbreviations render on the x-axis
   **And** the y-axis auto-scales to the data range with NIS-formatted labels via `OptiPlan.utils.formatCurrency()`
   **And** grid lines use `stroke: var(--grid-line)` at the defined opacity

3. **Given** the cash flow chart SVG is generated
   **When** a developer inspects the implementation
   **Then** bars are `<rect>` elements, the running total trend is a `<polyline>`, and the SVG has a `viewBox` for responsive scaling
   **And** no canvas elements, no chart libraries, and no external chart dependencies are used
   **And** `OptiPlan.modules.optibiz` follows the `init()`, `render(data)`, `destroy()` pattern

## Tasks / Subtasks

- [ ] Task 1: Add financial KPI summary row (AC: #1)
  - [ ] 1.1 Create `.optibiz-metrics` container with 4 metric cards
  - [ ] 1.2 KPI 1: Budget utilized — ₪1.22B / ₪1.8B with progress indicator
  - [ ] 1.3 KPI 2: Budget utilization percentage — 68%
  - [ ] 1.4 KPI 3: Monthly burn rate — sourced from `OPTIBIZ_DATA.burnRate`
  - [ ] 1.5 KPI 4: Variance indicator — sourced from `OPTIBIZ_DATA.variance`
  - [ ] 1.6 All values from `OPTIBIZ_DATA` and `PROJECT_DATA.totalBudget` — zero hardcoded numbers
  - [ ] 1.7 Format currency using `OptiPlan.utils.formatCurrency()` or inline `Intl.NumberFormat`

- [ ] Task 2: Implement 12-month cash flow bar chart SVG (AC: #2, #3)
  - [ ] 2.1 Create `<svg>` with responsive `viewBox` (e.g., `0 0 720 300`)
  - [ ] 2.2 Compute y-axis scale: find max value across all plan/actual, add 10% padding
  - [ ] 2.3 Render y-axis labels (4-5 tick marks, NIS-formatted via `OptiPlan.utils.formatCurrency()`)
  - [ ] 2.4 Render x-axis labels (Hebrew month abbreviations from `OPTIBIZ_DATA.monthLabels`)
  - [ ] 2.5 Render horizontal grid lines using `stroke: var(--border)` at 0.3 opacity
  - [ ] 2.6 Render plan bars: `<rect>` with `fill` class using `var(--optibiz)` at 40% opacity
  - [ ] 2.7 Render actual bars: `<rect>` with `fill` class using solid `var(--optibiz)`
  - [ ] 2.8 Each bar pair grouped per month with small gap between plan/actual

- [ ] Task 3: Add running total trend line (AC: #3)
  - [ ] 3.1 Compute cumulative actual spend per month
  - [ ] 3.2 Render `<polyline>` with computed points, `stroke: var(--optibiz)`, `fill: none`, `stroke-width: 2`
  - [ ] 3.3 Add small `<circle>` markers at each data point (optional, for visual clarity)

- [ ] Task 4: Add chart legend (AC: #2)
  - [ ] 4.1 Legend below or beside chart: plan bar sample (40% opacity) + "תכנון", actual bar sample (solid) + "ביצוע"
  - [ ] 4.2 If trend line included: line sample + "מצטבר"
  - [ ] 4.3 Legend uses `var(--text-secondary)` text color, `var(--font-main)` font

- [ ] Task 5: Implement OptiPlan.modules.optibiz namespace (AC: #3)
  - [ ] 5.1 Create `init()` method — cache DOM refs
  - [ ] 5.2 Create `render(data)` method — generates all HTML for metrics + chart
  - [ ] 5.3 Create `destroy()` method — clean up any event listeners
  - [ ] 5.4 Replace placeholder stub from Story 2.2 with full implementation

- [ ] Task 6: Staggered content entrance animation
  - [ ] 6.1 KPI metrics row fades in first (0ms delay)
  - [ ] 6.2 Chart fades in second (100ms delay)
  - [ ] 6.3 Legend fades in third (200ms delay)
  - [ ] 6.4 Use `opacity: 0→1` + `translateY(12px)→0` over 300ms

- [ ] Task 7: Verify and test all acceptance criteria
  - [ ] 7.1 Zero console errors on overlay open/close
  - [ ] 7.2 KPI values match `OPTIBIZ_DATA` and `PROJECT_DATA.totalBudget`
  - [ ] 7.3 Chart renders 12 bar pairs (plan + actual) correctly
  - [ ] 7.4 Y-axis auto-scales to data range
  - [ ] 7.5 Hebrew month labels render correctly in RTL
  - [ ] 7.6 Theme toggle works (dark/light) on all new elements
  - [ ] 7.7 Chart scrolls within overlay if needed; header stays fixed
  - [ ] 7.8 Existing card flip, overlay open/close, sidebar nav unbroken
  - [ ] 7.9 Touch targets meet 44px minimum
  - [ ] 7.10 No hardcoded hex values in new CSS

## Dev Notes

### Architecture Patterns & Constraints

**Single File Architecture:** ALL code goes into `index.html`. No separate JS/CSS files.

**Module Namespace Pattern (MUST follow exactly):**
```javascript
// ================================================================
// MODULE: OptiBiz
// ================================================================
OptiPlan.modules.optibiz = {
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

**Data Source — `OPTIBIZ_DATA` (already defined in Story 1.2, Object.freeze'd):**
```javascript
{
  budgetTotal: 1800000000,
  budgetUtilized: 1220000000,
  budgetUtilization: 68,
  burnRate: 101666667,
  variance: -22000000,
  monthLabels: ['ינו׳','פבר׳','מרץ','אפר׳','מאי','יונ׳','יול׳','אוג׳','ספט׳','אוק׳','נוב׳','דצמ׳'],
  cashFlow: [
    { month: 'ינו׳', plan: 100000000, actual: 72000000 },
    { month: 'פבר׳', plan: 118000000, actual: 85000000 },
    { month: 'מרץ',  plan: 138000000, actual: 98000000 },
    { month: 'אפר׳', plan: 150000000, actual: 110000000 },
    { month: 'מאי',  plan: 160000000, actual: 118000000 },
    { month: 'יונ׳', plan: 165000000, actual: 125000000 },
    { month: 'יול׳', plan: 168000000, actual: 128000000 },
    { month: 'אוג׳', plan: 175000000, actual: 135000000 },
    { month: 'ספט׳', plan: 168000000, actual: 130000000 },
    { month: 'אוק׳', plan: 162000000, actual: 122000000 },
    { month: 'נוב׳', plan: 152000000, actual: 55000000 },
    { month: 'דצמ׳', plan: 144000000, actual: 42000000 }
  ]
}
```

**Also references `PROJECT_DATA.totalBudget` (1800000000) for the total budget display — this MUST match `OPTIBIZ_DATA.budgetTotal`.**

### Cash Flow Bar Chart Implementation Details

**SVG-Based Grouped Bar Chart (NOT HTML, NOT canvas, NOT any charting library):**

```
viewBox example: "0 0 720 300"

Layout:
- Left margin (~80px): y-axis labels (NIS formatted)
- Bottom margin (~40px): x-axis month labels
- Chart area: ~620px wide × ~240px tall
- 12 month groups, each group = 2 bars (plan + actual)
```

**Bar Sizing Calculation:**
```
chartWidth = 620 (viewBox units after margins)
groupWidth = chartWidth / 12 ≈ 51.7
barWidth = groupWidth * 0.35 ≈ 18
gap between bars in group = 2
gap between groups = groupWidth - (2 * barWidth + gap) ≈ ~14

For each month i (0-11):
  groupX = leftMargin + (i * groupWidth)
  planBarX = groupX + (groupWidth - 2*barWidth - gap) / 2
  actualBarX = planBarX + barWidth + gap
  barHeight = (value / maxValue) * chartHeight
  barY = chartTop + chartHeight - barHeight
```

**Y-Axis Auto-Scaling:**
```javascript
var maxVal = 0;
data.cashFlow.forEach(function(m) {
  if (m.plan > maxVal) maxVal = m.plan;
  if (m.actual > maxVal) maxVal = m.actual;
});
maxVal = Math.ceil(maxVal / 20000000) * 20000000; // Round up to nearest 20M
```

Generate 4-5 evenly spaced y-axis ticks from 0 to `maxVal`. Format each with `OptiPlan.utils.formatCurrency()` or abbreviated format (e.g., "₪100M", "₪150M").

**Bar Styling (use CSS classes on SVG `<rect>`):**
```css
.optibiz-bar--plan { fill: var(--optibiz); opacity: 0.4; }
.optibiz-bar--actual { fill: var(--optibiz); }
```

Safari 16.2+ supports CSS custom properties on SVG elements via class selectors.

**Grid Lines:**
```html
<line x1="80" y1="{tickY}" x2="700" y2="{tickY}"
      stroke="var(--border)" stroke-opacity="0.3" />
```

**X-Axis Labels (Hebrew month abbreviations):**
```html
<text x="{groupCenterX}" y="{bottom + 20}"
      text-anchor="middle"
      fill="var(--text-muted)"
      font-family="var(--font-main)"
      font-size="10">{monthLabel}</text>
```

**Running Total Trend Line:**
```javascript
var cumulative = 0;
var points = data.cashFlow.map(function(m, i) {
  cumulative += m.actual;
  var x = leftMargin + (i * groupWidth) + groupWidth / 2;
  var y = chartTop + chartHeight - (cumulative / cumulativeMax * chartHeight);
  return x + ',' + y;
}).join(' ');
// <polyline points="{points}" stroke="var(--optibiz)" fill="none" stroke-width="2" stroke-dasharray="6,3" />
```

Note: The cumulative trend line needs its own y-axis scale (right side) since cumulative values are much larger than monthly values. Alternatively, use a secondary scale or skip the trend line if it clutters the visualization. The AC mentions "running total trend is a `<polyline>`" so include it but with a separate visual treatment (dashed line, lighter color, or mapped to a secondary scale).

**IMPORTANT: Cumulative scale consideration.** The cumulative total reaches ~₪1.22B while monthly values max at ~₪175M. Options:
1. **Dual y-axis** (left for monthly, right for cumulative) — more complex but accurate
2. **Normalized trend** — map cumulative to same visual height as monthly bars (misleading)
3. **Separate cumulative total display** — show as a text label instead of polyline

**Recommended: Dual y-axis** — left axis for monthly values (₪0–₪180M), right axis for cumulative (₪0–₪1.22B). The polyline uses the right axis scale.

### Financial KPI Summary Row

**KPI Card Structure:**
```html
<div class="optibiz-metrics">
  <div class="optibiz-metric">
    <span class="optibiz-metric__label">ניצול תקציב</span>
    <span class="optibiz-metric__value">₪1.22B / ₪1.8B</span>
    <div class="optibiz-metric__bar">
      <div class="optibiz-metric__bar-fill" style="width: 68%"></div>
    </div>
  </div>
  <div class="optibiz-metric">
    <span class="optibiz-metric__label">אחוז ניצול</span>
    <span class="optibiz-metric__value">68%</span>
  </div>
  <div class="optibiz-metric">
    <span class="optibiz-metric__label">קצב שריפה חודשי</span>
    <span class="optibiz-metric__value">₪102M</span>
  </div>
  <div class="optibiz-metric">
    <span class="optibiz-metric__label">סטייה</span>
    <span class="optibiz-metric__value optibiz-metric__value--negative">-₪22M</span>
  </div>
</div>
```

**KPI Values (ALL from data — zero hardcoded):**
```
Budget utilized: OPTIBIZ_DATA.budgetUtilized → format as ₪1.22B
Budget total: OPTIBIZ_DATA.budgetTotal (or PROJECT_DATA.totalBudget) → format as ₪1.8B
Utilization %: OPTIBIZ_DATA.budgetUtilization → 68%
Burn rate: OPTIBIZ_DATA.burnRate → format as ₪102M (101666667 → round to millions)
Variance: OPTIBIZ_DATA.variance → format as -₪22M (negative = under budget = amber/red indicator)
```

**Currency Formatting Helpers:**
```javascript
function formatBillions(val) {
  return '₪' + (val / 1000000000).toFixed(2) + 'B';
}
function formatMillions(val) {
  return '₪' + Math.round(Math.abs(val) / 1000000) + 'M';
}
```
Or use the existing `OptiPlan.utils.formatCurrency()` which uses `Intl.NumberFormat('he-IL')`.

### Content Entrance Animation Pattern

Follow the stagger pattern established in Story 2.3 for module overlay content:
```css
.optibiz-section {
  opacity: 0;
  transform: translateY(12px);
  transition: opacity 300ms var(--transition), transform 300ms var(--transition);
}

.optibiz-section--visible {
  opacity: 1;
  transform: translateY(0);
}
```

In `render()`, add `--visible` class with staggered `setTimeout`:
- Metrics row: 0ms
- Cash flow chart: 100ms
- Legend: 200ms

### Project Structure Notes

- **Single file:** ALL additions go into `index.html`
- **CSS section:** Add after "Component — OptiTrack Module" section, create new section: "Component — OptiBiz Module"
- **JS section:** Replace the `OptiPlan.modules.optibiz` placeholder stub
- **No new HTML elements in body:** Module content is rendered dynamically by `render(data)` into the `.module-overlay__content` container
- **Section delimiter format:**
```javascript
// ================================================================
// MODULE: OptiBiz
// ================================================================
```
```css
/* ================================================================ */
/* SECTION: Component — OptiBiz Module                                */
/* ================================================================ */
```

### Anti-Patterns to AVOID

1. **NO hardcoded hex colors** — use `var(--*)` tokens or CSS classes on SVG elements
2. **NO anonymous arrow functions** — named functions only for event handlers
3. **NO animating width/height/top/left** — only `transform` and `opacity`
4. **NO new files** — edit `index.html` only
5. **NO functions outside `OptiPlan.*` namespace**
6. **NO emojis** — Tabler Icons only
7. **NO breaking existing** entrance animations, touch feedback, theme toggle, card flip, overlay open/close, idle reset, sidebar nav, OptiTrack module
8. **NO canvas/D3.js/charting libraries** — vanilla SVG + HTML only
9. **NO hardcoded display values** — ALL numbers from `OPTIBIZ_DATA` and `PROJECT_DATA`
10. **NO HTML `<canvas>` element** — SVG `<rect>`, `<polyline>`, `<text>`, `<line>` only
11. **NO `setInterval` for animations** — CSS transitions/animations only
12. **NO growing arrays or orphaned listeners** — memory-safe patterns
13. **NO forgetting `-webkit-` prefixes** for Safari: `-webkit-backface-visibility`, `-webkit-perspective`
14. **NO `console.error`** — use `console.warn` for caught errors, `console.log` for state changes
15. **NO multiple module overlays** — only one in DOM at a time (already enforced by Story 2.2)
16. **NO pre-formatted currency strings in data** — format at render time only

### CSS Custom Property Rules

- ALL structural colors MUST use `var(--*)` tokens
- Module color set via `--module` local custom property (already set by overlay from Story 2.2)
- Background: `var(--bg-card)` — adapts to dark/light theme
- Text: `var(--text-primary)`, `var(--text-secondary)`, `var(--text-muted)`
- Borders: `var(--border)`
- Spacing: `var(--gap)`, `var(--gap-sm)`, `var(--gap-lg)`
- Radius: `var(--radius)`, `var(--radius-sm)`
- Font: `var(--font-main)` for Varela Round, `var(--font-mono)` for JetBrains Mono
- Chart-specific: `var(--optibiz)` for bar fills, `var(--border)` for grid lines
- Bar opacity: 0.4 for plan bars (CSS class), 1.0 for actual bars (CSS class)

### RTL Layout Considerations

- HTML `dir="rtl"` is set on `<html>` — all flex/grid auto-reverses
- KPI metrics row flows RTL naturally (first metric appears on right)
- SVG chart: x-axis coordinates are absolute (not affected by RTL) — months render left-to-right chronologically which is correct for a timeline regardless of text direction
- Y-axis labels: positioned on left side of chart (logical start in LTR charts is correct for timelines)
- Hebrew text in SVG `<text>` elements renders RTL automatically
- Currency symbol ₪ positions correctly in RTL text context
- Use `margin-inline-start` / `padding-inline-end` (logical properties) for HTML elements
- Back button positioned via `inset-inline-end` (handled by Story 2.2 overlay)

### Safari WebKit Compatibility

- SVG `<rect>` with CSS class-based fills works in Safari 16.2+
- `<polyline>` with `stroke` attribute supported
- CSS custom properties in SVG (via classes) supported
- SVG `<text>` with `font-family` attribute renders correctly
- `overflow-y: auto` on content container (no deprecated `-webkit-overflow-scrolling`)
- `overscroll-behavior: contain` on scroll container
- Font rendering: Varela Round + JetBrains Mono via Google Fonts CDN
- `Intl.NumberFormat('he-IL')` fully supported in Safari 16.2+
- `color-mix()` supported in Safari 16.2+

### Previous Story Intelligence

**From Story 2.3 (OptiTrack Module — ready-for-dev, immediately prior):**
- SVG-based visualization pattern established (permit matrix uses SVG `<rect>`)
- CSS class-based fills on SVG elements confirmed working approach
- Metrics summary row pattern: `.optitrack-metrics` with 4 metric cards
- Entrance animation stagger pattern: 0ms/100ms/200ms with `--visible` class
- `render(data)` populates `.module-overlay__content` via innerHTML
- Relative timestamp calculation pattern (for activity feed — reusable for date formatting)
- Status badge color mapping pattern (reusable for variance indicator)

**From Story 2.2 (Module Expansion Overlay — review):**
- Overlay container fully functional: `OptiPlan.components.moduleOverlay.expand('optibiz')` opens overlay
- Header already renders: "OptiBiz — ניהול פיננסי" with amber accent bar and ti-chart-bar icon
- Content area: `.module-overlay__content` is the render target — your `render(data)` output goes HERE
- Module stub exists: `OptiPlan.modules.optibiz = { init, render, destroy }` — REPLACE the placeholder
- `MODULE_OVERLAY_CONFIG.optibiz.dataKey` = `'OPTIBIZ_DATA'` — overlay calls `render(window[dataKey])`
- Close/destroy lifecycle: overlay calls `OptiPlan.modules[moduleId].destroy()` on close

**From Story 2.1 (Card Flip — review):**
- Card back shows OptiBiz KPIs: ₪1.22B utilized, 68% budget, burn rate, Q1 variance
- CTA "Full Module" button triggers overlay expand
- Data sourced from `OPTIBIZ_DATA` via `CARD_BACK_CONFIG`
- Currency formatting pattern: `'₪' + (val / 1000000000).toFixed(2) + 'B'`

**From Story 1.5 (Stat Cards):**
- OptiBiz front card shows: ₪1.22B, 68% sublabel
- `STAT_CARD_CONFIG` formatter: `'₪' + (OPTIBIZ_DATA.budgetUtilized / 1000000000).toFixed(2) + 'B'`

**From Story 1.4 (Hero Gauges):**
- Financial Health gauge shows 68% — sourced from `OPTIBIZ_DATA.budgetUtilization`
- SVG ring pattern: `<circle>` with `stroke-dasharray` / `stroke-dashoffset` — different from bar chart but confirms SVG proficiency in codebase

**From Story 1.2 (Data Model):**
- `OPTIBIZ_DATA` is `Object.freeze()`'d — read-only at runtime
- `budgetTotal` matches `PROJECT_DATA.totalBudget` (both 1800000000)
- `cashFlow` array has 12 objects with `month`, `plan`, `actual` fields
- `monthLabels` array has Hebrew month abbreviations
- `burnRate` = 101666667 (≈₪102M/month)
- `variance` = -22000000 (₪-22M, negative = under budget)
- `OptiPlan.utils.formatCurrency()` uses `Intl.NumberFormat('he-IL')`

**From Story 1.1 (Scaffold):**
- `OptiPlan` namespace established with `modules: {}` placeholder
- Section delimiter pattern for code organization
- Design token system in `:root`

### Data Consistency Cross-References

| Location | Value | Must Match |
|----------|-------|------------|
| Stat card front (1.5) | ₪1.22B | `OPTIBIZ_DATA.budgetUtilized` (1220000000) |
| Stat card front (1.5) | 68% | `OPTIBIZ_DATA.budgetUtilization` |
| Card back KPI (2.1) | ₪1.22B utilized | `OPTIBIZ_DATA.budgetUtilized` |
| Card back KPI (2.1) | 68% budget | `OPTIBIZ_DATA.budgetUtilization` |
| Card back KPI (2.1) | Burn rate | `OPTIBIZ_DATA.burnRate` (101666667) |
| Card back KPI (2.1) | Variance | `OPTIBIZ_DATA.variance` (-22000000) |
| Hero gauge (1.4) | 68% financial | `OPTIBIZ_DATA.budgetUtilization` |
| Module KPIs (this story) | All 4 summary values | `OPTIBIZ_DATA.*` |
| Cash flow chart bars | 12 plan + 12 actual | `OPTIBIZ_DATA.cashFlow[].plan/actual` |
| Budget total display | ₪1.8B | `OPTIBIZ_DATA.budgetTotal` AND `PROJECT_DATA.totalBudget` |
| Sum of actual cashFlow | Should approximate ₪1.22B | `OPTIBIZ_DATA.budgetUtilized` |

**IMPORTANT: Actual cashFlow sum check:**
72+85+98+110+118+125+128+135+130+122+55+42 = 1,220 (millions) = ₪1,220,000,000 = ₪1.22B ✓
This matches `OPTIBIZ_DATA.budgetUtilized` exactly.

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Epic-2-Story-2.4]
- [Source: _bmad-output/planning-artifacts/architecture.md#Data-Visualization]
- [Source: _bmad-output/planning-artifacts/architecture.md#Component-Architecture]
- [Source: _bmad-output/planning-artifacts/architecture.md#Animation-System]
- [Source: _bmad-output/planning-artifacts/architecture.md#Internal-Structure-of-index.html]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Layer-3-Module-Expansion]
- [Source: _bmad-output/planning-artifacts/prd.md#FR20-FR21]
- [Source: _bmad-output/implementation-artifacts/2-3-optitrack-module-permit-matrix-activity-feed.md]
- [Source: _bmad-output/implementation-artifacts/2-2-module-expansion-overlay-container-layer-3.md]
- [Source: _bmad-output/implementation-artifacts/2-1-card-flip-interaction-kpi-back-face-layer-2.md]

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
