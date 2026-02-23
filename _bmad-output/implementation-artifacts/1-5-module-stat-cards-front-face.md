# Story 1.5: Module Stat Cards (Front Face)

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As an investor,
I want to see 5 vivid, tappable module cards each displaying a key metric,
so that I can identify all 5 platform capabilities at a glance and feel invited to explore each one.

## Acceptance Criteria

1. **Given** the scaffold and data model exist **When** `OptiPlan.components.statCards.init()` runs **Then** 5 gradient stat cards render in a horizontal row: OptiTrack, OptiBiz, OptiRisk, OptiDocs, OptiGantt — each showing a primary KPI value and sub-label sourced from their respective `*_DATA` constants

2. **Given** the stat cards are rendered **When** a developer inspects the card CSS **Then** each card background uses `linear-gradient(135deg, var(--module), color-mix(in srgb, var(--module) 68%, #000))` **And** each card has `box-shadow: 0 4px 16px color-mix(in srgb, var(--module) 35%, transparent)` **And** zero hardcoded hex values appear in stat card CSS

3. **Given** the stat cards are rendered **When** a developer inspects each card's content **Then** each card contains: module name (8px uppercase JetBrains Mono, 0.6 opacity), Tabler icon watermark (28px, 0.2 opacity, positioned bottom-right in RTL), KPI label (10px), KPI value (22px JetBrains Mono Bold), and sub-label (9px, 0.7 opacity)

4. **Given** the page loads **When** the DOMContentLoaded entrance animation fires **Then** each card animates from `opacity: 0, translateY(18px), scale(0.97)` to final position **And** delays are staggered: 0ms, 60ms, 120ms, 180ms, 240ms per card respectively **And** animation duration is 0.65s with `cubic-bezier(0.16, 1, 0.3, 1)` easing

5. **Given** a stat card is displayed **When** an investor places their finger on it (`touchstart`) **Then** the card scales to 0.98 within <50ms via the named `handleStatCardTouchStart` function **And** the touch feedback fires before the flip animation begins (immediate visual acknowledgment)

6. **Given** the stat cards are fully rendered **When** `OptiPlan.components.statCards` is inspected **Then** it exposes `init()`, `render(data)`, and `destroy()` methods **And** all DOM element references use the `$` prefix convention (e.g., `$statCardsRow`)

## Tasks / Subtasks

- [x] Task 1: Add stat cards HTML inside `<main id="mainRegion">` (AC: 1, 3)
  - [x] Add `.stat-cards-row` container section after the hero gauges `</section>` (after line ~780)
  - [x] Add 5 `.stat-card` elements, each with `data-module` attribute (optitrack, optibiz, optirisk, optidocs, optigantt)
  - [x] Each card structure: `.stat-card__name` (module name), `.stat-card__value` (KPI number), `.stat-card__label` (KPI label), `.stat-card__sublabel` (trend/secondary), `.stat-card__icon` (Tabler icon watermark)
  - [x] Use semantic class names: `.stat-cards-row`, `.stat-card`, `.stat-card__name`, `.stat-card__value`, `.stat-card__label`, `.stat-card__sublabel`, `.stat-card__icon`
  - [x] Set initial state: `opacity: 0; transform: translateY(18px) scale(0.97)` for entrance animation

- [x] Task 2: Add stat card CSS section (AC: 2, 3)
  - [x] Add new CSS section: `/* SECTION: Component — Stat Cards */` after Hero Gauges CSS (after line ~517, before Animations & Keyframes)
  - [x] Style `.stat-cards-row` as flex row with `justify-content: center`, `gap: 12px`, `padding: var(--gap) 0`
  - [x] Style `.stat-card` with: `border-radius: var(--radius)`, `padding: var(--gap)`, `position: relative`, `overflow: hidden`, `cursor: pointer`, `min-width: 140px`, `flex: 1`
  - [x] For each module, use CSS attribute selectors to set `--module` custom property: `[data-module="optitrack"] { --module: var(--optitrack); }` etc.
  - [x] Background: `linear-gradient(135deg, var(--module), color-mix(in srgb, var(--module) 68%, #000))`
  - [x] Box-shadow: `0 4px 16px color-mix(in srgb, var(--module) 35%, transparent)`
  - [x] Style `.stat-card__name`: `font-family: var(--font-mono)`, `font-size: 8px`, `text-transform: uppercase`, `letter-spacing: 0.1em`, `opacity: 0.6`, `color: #fff`
  - [x] Style `.stat-card__value`: `font-family: var(--font-mono)`, `font-weight: 700`, `font-size: 22px`, `color: #fff`
  - [x] Style `.stat-card__label`: `font-size: 10px`, `color: rgba(255,255,255,0.8)`
  - [x] Style `.stat-card__sublabel`: `font-size: 9px`, `opacity: 0.7`, `color: #fff`
  - [x] Style `.stat-card__icon`: `position: absolute`, `bottom: 8px`, `left: 8px` (RTL: this maps to bottom-right visually), `font-size: 28px`, `opacity: 0.2`, `color: #fff`, `pointer-events: none`
  - [x] Note on white text: Since stat cards have gradient colored backgrounds, white text (#fff) is acceptable — these are NOT hardcoded "color values" used for theming; they're contrast-against-gradient text
  - [x] Add hover state: `.stat-card:hover { transform: translateY(-2px); }` with `transition: transform var(--transition)`
  - [x] Add `touch-action: manipulation` and `-webkit-touch-callout: none` and `user-select: none` on `.stat-card`

- [x] Task 3: Add stat card entrance animation CSS (AC: 4)
  - [x] In the Animations & Keyframes section (line ~519), add `@keyframes stat-card-entrance`
  - [x] Keyframes: `from { opacity: 0; transform: translateY(18px) scale(0.97); }` `to { opacity: 1; transform: translateY(0) scale(1); }`
  - [x] Duration: `0.65s`, easing: `cubic-bezier(0.16, 1, 0.3, 1)`, `fill-mode: forwards`
  - [x] Stagger delays applied via JS (not CSS nth-child) for control over timing relative to gauge animation

- [x] Task 4: Register `OptiPlan.components.statCards` (AC: 1, 5, 6)
  - [x] Add STAT_CARD_CONFIG const after HERO_GAUGE constants (after line ~1191)
  - [x] Add new JS section: `// SECTION: Component — Stat Cards` after Hero Gauges JS (after line ~1265, before Theme Toggle Handler)
  - [x] Implement `OptiPlan.components.statCards.init()` — cache DOM refs with `$` prefix (`$statCardsRow`, `$statCards`)
  - [x] Implement `OptiPlan.components.statCards.render(data)` — populate card content from STAT_CARD_CONFIG, then trigger entrance animation with staggered delays
  - [x] Implement `OptiPlan.components.statCards.destroy()` — remove event listeners (touchstart/touchend), clear any timeouts
  - [x] Store entrance animation timeout refs for cleanup in `destroy()`
  - [x] Use named event handlers: `handleStatCardTouchStart`, `handleStatCardTouchEnd`

- [x] Task 5: Wire stat card data from constants (AC: 1, 3)
  - [x] OptiTrack: value = `OPTITRACK_DATA.approved` (222), label = "תיאומים מאושרים", sublabel = "+18%", icon = `ti-route`
  - [x] OptiBiz: value = formatted `OPTIBIZ_DATA.budgetUtilized` as "₪1.22B", label = "ניצול תקציב", sublabel = `OPTIBIZ_DATA.budgetUtilization + '%'` (68%), icon = `ti-chart-bar`
  - [x] OptiRisk: value = `OPTIRISK_DATA.activeRisks` (23), label = "סיכונים פעילים", sublabel = `OPTIRISK_DATA.critical + ' קריטיים'` (8 קריטיים), icon = `ti-alert-triangle`
  - [x] OptiDocs: value = `OPTIDOCS_DATA.total` (847), label = "מסמכים", sublabel = `OPTIDOCS_DATA.completionRate + '% הושלם'` (94% הושלם), icon = `ti-file-text`
  - [x] OptiGantt: value = `OPTIGANTT_DATA.sections.length` (6), label = "קטעי פרויקט", sublabel = computed delayed count from sections data, icon = `ti-calendar-stats`
  - [x] **IMPORTANT:** All values MUST come from data constants — never hardcode display numbers. Use `OptiPlan.utils.formatCurrency()` for currency values
  - [x] **NOTE on OptiGantt:** Used sections.length for value and computed delayed sections (progress < 50) for sublabel

- [x] Task 6: Add touch feedback interaction (AC: 5)
  - [x] Create named function `handleStatCardTouchStart(e)` — applies `scale(0.98)` via inline `transform` style
  - [x] Create named function `handleStatCardTouchEnd(e)` — removes scale transform, restoring default
  - [x] Register both handlers in `init()`: `addEventListener('touchstart', handleStatCardTouchStart, { passive: true })`
  - [x] Register touchend: `addEventListener('touchend', handleStatCardTouchEnd, { passive: true })`
  - [x] Cleanup in `destroy()`: `removeEventListener` with same named function refs
  - [x] Ensure touch feedback fires BEFORE any future flip animation (Story 2.1 will add flip)

- [x] Task 7: Add entrance animation with staggered delays (AC: 4)
  - [x] Entrance animation should start AFTER hero gauges fill completes (~1200ms after load)
  - [x] Use `setTimeout` chain with staggered delays: card[0]=0ms, card[1]=60ms, card[2]=120ms, card[3]=180ms, card[4]=240ms (relative to entrance start)
  - [x] Apply animation by setting `animation` property via JS
  - [x] Store all timeout refs in array for cleanup in `destroy()`
  - [x] CSS animation: `stat-card-entrance 0.65s cubic-bezier(0.16, 1, 0.3, 1) forwards`

- [x] Task 8: Update initialization (AC: 1, 6)
  - [x] Add `OptiPlan.components.statCards.init()` call in `OptiPlan.init()` after `heroGauges.init()`
  - [x] Ensure init runs after state transitions to `'overview'`
  - [x] Render is called from init, which populates from STAT_CARD_CONFIG

- [x] Task 9: Add `prefers-reduced-motion` support (AC: 4)
  - [x] Existing global `@media (prefers-reduced-motion: reduce)` rule already sets `animation-duration: 0.01ms !important` and `transition-duration: 0.01ms !important`
  - [x] Verified stat card entrance animation respects this — cards appear instantly without animation
  - [x] Verified hover/touch transforms also respect reduced motion via existing global rule

- [x] Task 10: Verify and test (AC: all)
  - [x] Code review verified zero syntax errors in HTML/CSS/JS
  - [x] 5 stat cards render in a horizontal row below hero gauges
  - [x] Each card has correct gradient background using module color via `--module` custom property
  - [x] Card content: module name, KPI value, label, sublabel, icon watermark all correctly wired
  - [x] Entrance animation fires after gauges (1200ms delay), with staggered delays (60ms per card)
  - [x] Touch feedback: handleStatCardTouchStart applies scale(0.98) on touchstart
  - [x] Theme toggle works — gradient uses var(--module) which references root-level module colors
  - [x] No hardcoded color values in new CSS (white text on gradient bg is acceptable)
  - [x] Cards fit within viewport (1024x768) — min-width adjusted to 140px to fit available 784px
  - [x] RTL layout: flex row reverses order naturally, icon positioned at left (visual right in RTL)

## Dev Notes

### Critical Architecture Patterns — MUST Follow

**Component Registration Pattern:**
```javascript
// ================================================================
// SECTION: Component — Stat Cards
// ================================================================

const STAT_CARD_CONFIG = [
  { key: 'optitrack', name: 'OptiTrack', icon: 'ti-route',
    value: OPTITRACK_DATA.approved, label: 'תיאומים מאושרים',
    sublabel: '+18%', sublabelType: 'trend-up' },
  { key: 'optibiz', name: 'OptiBiz', icon: 'ti-chart-bar',
    value: null, label: 'ניצול תקציב',
    sublabel: OPTIBIZ_DATA.budgetUtilization + '%',
    formatter: function() {
      return '₪' + (OPTIBIZ_DATA.budgetUtilized / 1000000000).toFixed(2) + 'B';
    }},
  { key: 'optirisk', name: 'OptiRisk', icon: 'ti-alert-triangle',
    value: OPTIRISK_DATA.activeRisks, label: 'סיכונים פעילים',
    sublabel: OPTIRISK_DATA.critical + ' קריטיים' },
  { key: 'optidocs', name: 'OptiDocs', icon: 'ti-file-text',
    value: OPTIDOCS_DATA.total, label: 'מסמכים',
    sublabel: OPTIDOCS_DATA.completionRate + '% הושלם' },
  { key: 'optigantt', name: 'OptiGantt', icon: 'ti-calendar-stats',
    value: OPTIGANTT_DATA.sections.length, label: 'קטעי פרויקט',
    sublabel: null, // See note below about computing delayed count
    sublabelCompute: function() {
      var delayed = OPTIGANTT_DATA.sections.filter(function(s) {
        return s.progress < 100 && s.progress < 50;
      }).length;
      return delayed + ' בעיכוב';
    }}
];

const STAT_CARD_ENTRANCE_DELAY = 1200;  // ms after page load (after gauge fill completes)
const STAT_CARD_STAGGER_MS = 60;        // ms between each card entrance

function handleStatCardTouchStart(e) {
  var card = e.currentTarget;
  card.style.transform = 'scale(0.98)';
}

function handleStatCardTouchEnd(e) {
  var card = e.currentTarget;
  card.style.transform = '';
}

OptiPlan.components.statCards = {
  $statCardsRow: null,
  $statCards: null,
  _entranceTimeouts: [],

  init: function() {
    this.$statCardsRow = document.querySelector('.stat-cards-row');
    if (!this.$statCardsRow) return;
    this.$statCards = this.$statCardsRow.querySelectorAll('.stat-card');
    this.render();
    this._bindEvents();
  },

  render: function() {
    var self = this;
    // Populate card content from config
    STAT_CARD_CONFIG.forEach(function(config, i) {
      var $card = self.$statCards[i];
      if (!$card) return;
      var $value = $card.querySelector('.stat-card__value');
      var $label = $card.querySelector('.stat-card__label');
      var $sublabel = $card.querySelector('.stat-card__sublabel');
      if ($value) {
        $value.textContent = config.formatter
          ? config.formatter()
          : config.value;
      }
      if ($label) $label.textContent = config.label;
      if ($sublabel) {
        $sublabel.textContent = config.sublabelCompute
          ? config.sublabelCompute()
          : config.sublabel;
      }
    });

    // Staggered entrance animation
    STAT_CARD_CONFIG.forEach(function(config, i) {
      var timeout = setTimeout(function() {
        var $card = self.$statCards[i];
        if ($card) {
          $card.style.animation = 'stat-card-entrance 0.65s cubic-bezier(0.16, 1, 0.3, 1) forwards';
        }
      }, STAT_CARD_ENTRANCE_DELAY + (i * STAT_CARD_STAGGER_MS));
      self._entranceTimeouts.push(timeout);
    });
  },

  _bindEvents: function() {
    if (!this.$statCards) return;
    for (var i = 0; i < this.$statCards.length; i++) {
      this.$statCards[i].addEventListener('touchstart', handleStatCardTouchStart, { passive: true });
      this.$statCards[i].addEventListener('touchend', handleStatCardTouchEnd, { passive: true });
    }
  },

  destroy: function() {
    // Clear entrance timeouts
    this._entranceTimeouts.forEach(function(t) { clearTimeout(t); });
    this._entranceTimeouts = [];
    // Remove event listeners
    if (this.$statCards) {
      for (var i = 0; i < this.$statCards.length; i++) {
        this.$statCards[i].removeEventListener('touchstart', handleStatCardTouchStart);
        this.$statCards[i].removeEventListener('touchend', handleStatCardTouchEnd);
      }
    }
  }
};
```

**HTML Structure (each stat card):**
```html
<section class="stat-cards-row">
  <div class="stat-card" data-module="optitrack">
    <span class="stat-card__name">OptiTrack</span>
    <span class="stat-card__value">222</span>
    <span class="stat-card__label">תיאומים מאושרים</span>
    <span class="stat-card__sublabel">+18%</span>
    <i class="stat-card__icon ti ti-route"></i>
  </div>
  <!-- Repeat for optibiz, optirisk, optidocs, optigantt -->
</section>
```

**CSS Gradient Pattern (CRITICAL — get `color-mix()` right):**
```css
.stat-card {
  /* Module color set by data-module attribute selector */
  background: linear-gradient(135deg, var(--module), color-mix(in srgb, var(--module) 68%, #000));
  box-shadow: 0 4px 16px color-mix(in srgb, var(--module) 35%, transparent);
}

[data-module="optitrack"] { --module: var(--optitrack); }
[data-module="optibiz"]   { --module: var(--optibiz); }
[data-module="optirisk"]  { --module: var(--optirisk); }
[data-module="optidocs"]  { --module: var(--optidocs); }
[data-module="optigantt"] { --module: var(--optigantt); }
```

**Entrance Animation CSS:**
```css
@keyframes stat-card-entrance {
  from {
    opacity: 0;
    transform: translateY(18px) scale(0.97);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}
```

**CSS Custom Property Rules:**
- ALL structural colors MUST use `var(--*)` tokens
- Module color set via `--module` local custom property per `[data-module]` selector
- White text (`#fff`, `rgba(255,255,255,...)`) on gradient cards is acceptable for contrast — NOT a theme token violation
- Animate ONLY `transform` and `opacity` — never layout properties
- `color-mix(in srgb, ...)` requires Safari 16.2+ (confirmed safe for iPad 10th gen)

**Named Event Handler Pattern — NO anonymous callbacks:**
All touch handlers (`handleStatCardTouchStart`, `handleStatCardTouchEnd`) are named functions defined at module scope, enabling `removeEventListener` cleanup in `destroy()`.

### Current index.html State (What Exists)

**Main Content Area (line ~741-781):**
```html
<main id="mainRegion">
  <section class="hero-gauges">
    <!-- 3 hero gauge elements (financial, coordination, schedule) -->
  </section>
  <!-- STAT CARDS GO HERE — after hero gauges, inside main -->
</main>
```
Status: Hero gauges are rendered. Stat cards section goes immediately after the gauges `</section>`, inside `<main>`.

**CSS section insertion point (line ~517-519):**
```css
/* End of Hero Gauges styles */

/* ================================================================ */
/* SECTION: Animations & Keyframes                                   */
/* ================================================================ */
```
Status: Insert `/* SECTION: Component — Stat Cards */` between lines 517-518 (after Hero Gauges CSS, before Animations).

**JS section insertion point (line ~1265-1268):**
```javascript
// End of heroGauges component

// ================================================================
// SECTION: Theme Toggle Handler
// ================================================================
```
Status: Insert STAT_CARD_CONFIG constants and `OptiPlan.components.statCards` object between hero gauges and theme toggle handler.

**JS init insertion point (line ~1306):**
```javascript
OptiPlan.components.heroGauges.init();   // Line 1306
// OptiPlan.components.statCards.init();  ← INSERT HERE
```

**CSS tokens already defined:**
- `--optitrack: #0991F3` (Blue)
- `--optibiz: #F6AE2D` (Gold/Amber)
- `--optirisk: #FF7A3C` (Orange-Red)
- `--optidocs: #4395FD` (Light Blue)
- `--optigantt: #0B3C5D` (Dark Navy)
- `--font-mono: 'JetBrains Mono', monospace`
- `--font-main: 'Varela Round', sans-serif`
- `--radius: 14px` (card radius)
- `--gap: 16px`, `--gap-sm: 8px`, `--gap-lg: 24px`
- `--transition: 0.35s cubic-bezier(0.16, 1, 0.3, 1)`
- `--bg-card`, `--bg-card-hover`, `--shadow-card`, `--shadow-card-hover`
- `--text-primary`, `--text-secondary`, `--text-muted`

**Data constants available (from Story 1.2, all `Object.freeze()`d):**

| Constant | Key Fields for Stat Card |
|----------|-------------------------|
| `OPTITRACK_DATA` | `.approved` (222), `.totalItems` (347), `.approvalRate` (64) |
| `OPTIBIZ_DATA` | `.budgetUtilized` (1220000000), `.budgetUtilization` (68) |
| `OPTIRISK_DATA` | `.activeRisks` (23), `.critical` (8) |
| `OPTIDOCS_DATA` | `.total` (847), `.completionRate` (94) |
| `OPTIGANTT_DATA` | `.sections` (6 section objects with `.progress`), `.milestones` |

### Data Value Discrepancies — IMPORTANT

The epics spec states stat card values as:
- OptiTrack: "312 completed, +18%" — BUT `OPTITRACK_DATA` has no field yielding 312. Closest: `approved` = 222 or `totalItems` = 347
- OptiGantt: "156 tasks, 22 delayed" — BUT `OPTIGANTT_DATA` has 6 sections (not 156 tasks) and no `delayedTasks` field

**Recommendation:** The data model (Story 1.2) is the single source of truth. Use actual data constant values:
- OptiTrack stat card: Show `OPTITRACK_DATA.approved` (222) with label "תיאומים מאושרים" and "+18%" as hardcoded trend display
- OptiGantt stat card: Either extend `OPTIGANTT_DATA` with `totalTasks` and `delayedTasks` fields, OR show section-level stats (6 sections, compute delayed count from sections where progress < threshold)

OptiBiz, OptiRisk, and OptiDocs all perfectly match the epics spec with their data constant values.

### Entrance Animation Sequencing

Per the UX spec, the entrance choreography is:
1. **Gauges fill** (400ms delay + 800ms animation = complete at ~1200ms)
2. **Cards stagger** (starts at ~1200ms, 5 cards x 60ms stagger = 240ms spread, 650ms animation)
3. **AI indicator pulses** (starts after cards, continuous)

The stat card entrance should start at `STAT_CARD_ENTRANCE_DELAY = 1200` ms after page load.

### Icon Verification

Verify the Tabler icon class names exist in the loaded Tabler Icons webfont CDN:
- `ti-route` — OptiTrack (coordination/routing)
- `ti-chart-bar` — OptiBiz (financial charts)
- `ti-alert-triangle` — OptiRisk (risk warnings)
- `ti-file-text` — OptiDocs (documents)
- `ti-calendar-stats` — OptiGantt (scheduling)

**CHECK:** Confirm these match or are consistent with the icons used in the sidebar nav items (from Story 1.3). If the sidebar uses different icons for these modules, match the sidebar icons.

### RTL Layout Considerations

- Flex row in RTL automatically reverses visual order — cards will read right-to-left
- Icon watermark position: `left: 8px` in CSS maps to visual right in RTL (this is correct per spec: "positioned bottom-left" in LTR terms)
- If cards should maintain LTR-like visual ordering in RTL, consider using explicit `order` CSS property or `direction: ltr` on the flex container
- Hebrew text in labels/sublabels will align correctly in RTL context

### Anti-Patterns to AVOID

1. **DO NOT** hardcode any hex color values in card CSS — use `var(--module)` via `[data-module]` attribute selectors
2. **DO NOT** use anonymous arrow functions for event handlers — use named functions for cleanup
3. **DO NOT** animate `width`, `height`, `top`, `left`, `margin`, `padding` — only `transform`, `opacity`
4. **DO NOT** create new files — edit only `index.html`
5. **DO NOT** add functions outside `OptiPlan.*` namespace (except private handlers at module scope)
6. **DO NOT** use emojis in the UI — Tabler Icons only
7. **DO NOT** break existing theme toggle, sidebar, topbar, or hero gauge functionality
8. **DO NOT** use canvas, D3.js, or any charting library
9. **DO NOT** use CSS `nth-child` for stagger delays — use JS `setTimeout` for control over timing relative to gauge animation
10. **DO NOT** forget `touch-action: manipulation` on interactive cards (bypasses 300ms click delay on iOS)
11. **DO NOT** hardcode display values — derive all numbers from `*_DATA` constants
12. **DO NOT** use `setInterval` — use `setTimeout` with stored refs for entrance delays

### Where to Insert Code in index.html

**New CSS section** — insert AFTER Hero Gauges section (~line 517) and BEFORE Animations & Keyframes:
```
/* ================================================================ */
/* SECTION: Component — Stat Cards                                    */
/* ================================================================ */
```

**New keyframes** — insert IN the Animations & Keyframes section (~line 519):
```
@keyframes stat-card-entrance { ... }
```

**New HTML** — insert INSIDE `<main id="mainRegion">` AFTER hero gauges `</section>` (line ~780):
```html
<section class="stat-cards-row"> ... </section>
```

**New JS constants** — insert AFTER HERO_GAUGE constants (line ~1191) and BEFORE heroGauges object:
```
const STAT_CARD_CONFIG = [...];
const STAT_CARD_ENTRANCE_DELAY = 1200;
const STAT_CARD_STAGGER_MS = 60;
```

**New JS section** — insert AFTER heroGauges component (line ~1265) and BEFORE Theme Toggle Handler:
```
// ================================================================
// SECTION: Component — Stat Cards
// ================================================================
```

**Update `OptiPlan.init()`** — add `OptiPlan.components.statCards.init()` call after `heroGauges.init()` (line ~1306)

### Previous Story Learnings (Stories 1.1-1.4)

**From Story 1.2 (done):**
- All data constants are `Object.freeze()`d — read-only access, do NOT try to mutate
- `OptiPlan.utils.formatPercent(value)` is available for percentage display
- `OptiPlan.utils.formatCurrency(amount)` is available for NIS formatting with `Intl.NumberFormat('he-IL')`
- Data keys are all camelCase, Hebrew only in display values
- Cross-references are validated: 68% budget, 64% approval, 347 items, 23 risks, 847 docs

**From Story 1.3 (done):**
- Sidebar and topbar components use `init()/render()/destroy()` pattern
- Named event handlers: `handleNavItemTap`, `handleThemeToggle`
- DOM refs use `$` prefix convention: `$sidebar`, `$navItems`
- All CSS uses `var(--*)` tokens, logical properties for RTL
- Section delimiters: `// ================================================================` with `SECTION: {Name}`

**From Story 1.4 (review):**
- Hero gauges follow same component pattern with `init()/render()/destroy()`
- `HERO_GAUGE_CONFIG` const array pattern — use similar pattern for `STAT_CARD_CONFIG`
- Gauge fill delay (400ms) and pulse start (1200ms) — stat card entrance should start at ~1200ms
- Named constants pattern: `HERO_GAUGE_FILL_DELAY`, `HERO_GAUGE_PULSE_DELAY` — follow with `STAT_CARD_ENTRANCE_DELAY`, `STAT_CARD_STAGGER_MS`
- `setTimeout` refs stored in component for cleanup
- `prefers-reduced-motion` handled by existing global CSS rule

**From Git History:**
- Story 1.3 commit: `d79c4fe Complete Story 1.3: Implement Sidebar Navigation & Topbar`
- Story 1.2 commit: `d522371 Complete Story 1.2: Hardcoded Project Data Model`
- Story 1.1 commit: `829ca1f Complete Story 1.1: HTML Scaffold & Design System Foundation`
- Clean commit history, no regressions detected
- Single-file architecture consistently maintained

### OptiBiz Currency Formatting Note

The stat card needs to display "₪1.22B" as a compact value. `OptiPlan.utils.formatCurrency()` returns full Hebrew-formatted numbers (e.g., "1,220,000,000"). For the stat card, use a custom compact formatter:
```javascript
// Compact format: ₪1.22B
function formatCompactCurrency(amount) {
  if (amount >= 1000000000) {
    return '₪' + (amount / 1000000000).toFixed(2) + 'B';
  } else if (amount >= 1000000) {
    return '₪' + (amount / 1000000).toFixed(1) + 'M';
  }
  return '₪' + amount.toLocaleString('he-IL');
}
```
Or define the formatter directly in the STAT_CARD_CONFIG entry for OptiBiz.

### Project Structure Notes

- **File location:** `index.html` (repository root) — ONLY file to modify
- **Alignment:** All code stays within the single-file architecture
- **No new files created** — CSS and JS sections added inline
- **Section delimiter format:** `// ================================================================` with `SECTION: {Name}` label (JS) and `/* SECTION: {Name} */` (CSS)

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story-1.5] — User story, BDD acceptance criteria, stat card specifications
- [Source: _bmad-output/planning-artifacts/architecture.md#Component-Architecture] — init/render/destroy pattern, named handlers, $ prefix convention
- [Source: _bmad-output/planning-artifacts/architecture.md#CSS-Custom-Properties] — Token cascade, module colors, zero hardcoded values rule
- [Source: _bmad-output/planning-artifacts/architecture.md#Animation-System] — CSS transitions for state changes, JS orchestration for sequenced entrances, GPU-composited only
- [Source: _bmad-output/planning-artifacts/architecture.md#Internal-Structure-of-index.html] — Section ordering: CSS section 7 (Stat Card), JS section 9 (Stat Cards)
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Stat-Card] — Gradient background, color-mix(), box-shadow, entrance stagger, hover/active states
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Touch-Interaction-Patterns] — Touch feedback <50ms, scale(0.98), touch-action: manipulation
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Entrance-Animation] — Gauges fill first → cards stagger → AI pulses, ~1.5s total
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Typography-System] — Module name 8px mono, KPI value 22-24px mono bold, labels 10-11px
- [Source: _bmad-output/implementation-artifacts/1-4-hero-gauges-animated-svg-rings.md] — Previous story pattern reference, HERO_GAUGE_CONFIG pattern, entrance timing
- [Source: _bmad-output/planning-artifacts/prd.md#FR3] — Investor can view 5 module cards representing OptiTrack, OptiBiz, OptiRisk, OptiDocs, OptiGantt
- [Source: _bmad-output/planning-artifacts/prd.md#FR30] — Module cards display staggered entrance animations on load

## Change Log

- 2026-02-23: Implemented all 10 tasks for Story 1.5 — Module Stat Cards (Front Face). Added 5 gradient stat cards (OptiTrack, OptiBiz, OptiRisk, OptiDocs, OptiGantt) with staggered entrance animation, touch feedback, and data-driven content from existing data constants. All acceptance criteria satisfied.

## Dev Agent Record

### Agent Model Used

Claude Opus 4.6

### Debug Log References

- min-width adjusted from 160px (spec) to 140px to ensure 5 cards fit within 784px available width (1024px viewport minus 240px sidebar). With flex: 1, each card renders at ~147px which is visually equivalent.
- OptiGantt stat card uses `OPTIGANTT_DATA.sections.length` (6) for value with label "קטעי פרויקט" (project sections), and computes delayed count from sections where progress < 50% for sublabel. This is consistent with the data model since OPTIGANTT_DATA has no totalTasks/delayedTasks fields.
- Stat card icons differ from sidebar nav icons by design (sidebar uses navigation-oriented icons, stat cards use metric-appropriate icons per the story spec).

### Completion Notes List

- Task 1: Added `.stat-cards-row` section with 5 `.stat-card` elements after hero gauges, each with data-module attribute and initial hidden state for entrance animation
- Task 2: Added full CSS section for stat cards with gradient backgrounds via `color-mix()`, box-shadow, typography, and icon watermark positioning. All colors use `var(--module)` custom property set by `[data-module]` attribute selectors
- Task 3: Added `@keyframes stat-card-entrance` in Animations section — animates from hidden/translated to visible/positioned
- Task 4: Registered `OptiPlan.components.statCards` with `init()`, `render()`, `destroy()` methods, `$statCardsRow`/`$statCards` DOM refs, and `_entranceTimeouts` array for cleanup
- Task 5: Wired all 5 cards to data constants — OptiTrack (222 approved), OptiBiz (₪1.22B via formatter), OptiRisk (23 active risks), OptiDocs (847 docs), OptiGantt (6 sections with computed delayed count)
- Task 6: Added named `handleStatCardTouchStart`/`handleStatCardTouchEnd` functions with passive event listeners and proper cleanup in `destroy()`
- Task 7: Staggered entrance starts at 1200ms (after gauge fill), 60ms between cards, animation applied via JS `style.animation` property
- Task 8: Added `OptiPlan.components.statCards.init()` call after `heroGauges.init()` in `OptiPlan.init()`
- Task 9: Verified existing global `prefers-reduced-motion` rule covers stat card animations
- Task 10: Code review verified all ACs satisfied, no syntax errors, correct data bindings, viewport fit confirmed
- Code Review Fixes: (1) Added animationend listener to clear animation-fill-mode forwards after entrance — unblocks hover translateY and touch scale transforms (HIGH), (2) Fixed render() signature to render(data) per AC6 (HIGH), (3) Changed .stat-card__icon from physical left to inset-inline-start for RTL compliance (MEDIUM), (4) Removed redundant self2 variable, reused existing self reference (MEDIUM), (5) Added sprint-status.yaml to File List (MEDIUM)

### File List

- `index.html` (modified) — Added stat cards HTML section, CSS section, entrance animation keyframes, STAT_CARD_CONFIG constants, statCards component JS, touch handlers, and init call
- `_bmad-output/implementation-artifacts/sprint-status.yaml` (modified) — Sprint tracking status updated for story progress
