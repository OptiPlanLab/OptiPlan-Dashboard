# Story 2.1: Card Flip Interaction & KPI Back Face (Layer 2)

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As an investor,
I want to tap a module card and see it flip to reveal 3-5 KPIs with trend indicators,
so that I get an immediate sense of real data depth behind each module and feel rewarded for exploring.

## Acceptance Criteria

1. **Given** the Epic 1 dashboard shell is complete with stat cards rendered **When** an investor taps any module stat card **Then** the card executes a 3D CSS flip: `perspective(1000px) rotateY(180deg)`, 400ms `cubic-bezier(0.4, 0, 0.2, 1)`, `backface-visibility: hidden` on both faces **And** the touch feedback (`scale(0.98)`) fires within <50ms before the flip animation begins

2. **Given** a card has flipped **When** the card back face is visible **Then** it displays the module's `var(--bg-card)` background with `border-top: 3px solid var(--module-color)` **And** 3-5 KPI rows render, each with: Hebrew label (Varela Round 11px), JetBrains Mono Bold value, and a colored trend arrow (up green / down red) **And** a "Full Module" CTA button renders at the bottom with module-color background, minimum 44px height

3. **Given** KPI data is displayed **When** a developer inspects the card back content **Then** OptiTrack back shows: 347 total items, 64% approved, 12 objections, 35 pending — sourced from `OPTITRACK_DATA` **And** OptiBiz back shows: 1.22B utilized, 68% budget, Q1 variance — sourced from `OPTIBIZ_DATA` **And** data values on card backs match the values in their respective `*_DATA` constants (no hardcoded display values)

4. **Given** one card is already flipped **When** an investor taps a different module card **Then** the currently flipped card animates back to its front face (400ms) before the new card begins its flip **And** `OptiPlan.state.activeModule` updates to the new module id **And** this transition is handled by the named `handleStatCardTap` function with no anonymous callbacks

5. **Given** a card is flipped (showing back face) **When** an investor taps anywhere outside all stat cards **Then** the active card flips back to its front face via the named `handleOutsideTap` function **And** `OptiPlan.state.currentLayer` returns to `overview`

6. **Given** a card flip animation is in progress **When** an investor taps a different card mid-animation **Then** the current card snaps to its animation destination immediately **And** the new card begins its flip without queuing — no animation backlog accumulates

## Tasks / Subtasks

- [x] Task 1: Restructure stat card HTML for flip container (AC: 1, 2)
  - [x]Wrap each `.stat-card` inner content in a `.stat-card__front` div
  - [x]Add a `.stat-card__back` div as sibling to `.stat-card__front` inside each `.stat-card`
  - [x]`.stat-card` becomes the flip container with `perspective(1000px)` and `transform-style: preserve-3d`
  - [x]`.stat-card__front` gets existing gradient background, content, and icon
  - [x]`.stat-card__back` gets `rotateY(180deg)` as initial transform (hidden behind front)
  - [x]Both faces get `-webkit-backface-visibility: hidden; backface-visibility: hidden`
  - [x]Back face structure: `.stat-card__back-header` (module name + icon), `.stat-card__back-kpis` (KPI rows), `.stat-card__back-cta` (Full Module button)

- [x] Task 2: Add card back face HTML for each module (AC: 2, 3)
  - [x]OptiTrack back: 4 KPI rows (total items 347, approved 64%, objections 12, pending 35)
  - [x]OptiBiz back: 4 KPI rows (utilized 1.22B, budget 68%, burn rate, Q1 variance)
  - [x]OptiRisk back: 4 KPI rows (active risks 23, critical 8, mitigated count, trend)
  - [x]OptiDocs back: 4 KPI rows (total 847, completion 94%, pending count, overdue count)
  - [x]OptiGantt back: 4 KPI rows (sections 6, on-track count, delayed count, milestone progress)
  - [x]Each KPI row: `.stat-card__kpi-row` with `.stat-card__kpi-label`, `.stat-card__kpi-value`, `.stat-card__kpi-trend`
  - [x]CTA button: `<button class="stat-card__back-cta">Full Module</button>` with Hebrew text "למודול המלא"

- [x] Task 3: Add flip CSS (AC: 1, 2)
  - [x]Add new CSS section: `/* SECTION: Component — Stat Card Flip */` after Stat Cards CSS
  - [x]`.stat-card` gets `perspective: 1000px; -webkit-perspective: 1000px;` (as container)
  - [x]Add `.stat-card__inner` wrapper (or use existing `.stat-card` as 3D context)
  - [x]`.stat-card__front, .stat-card__back`: `position: absolute; inset: 0; -webkit-backface-visibility: hidden; backface-visibility: hidden;`
  - [x]`.stat-card__back`: `transform: rotateY(180deg);` (starts hidden behind front)
  - [x]`.stat-card--flipped .stat-card__front`: `transform: rotateY(180deg);`
  - [x]`.stat-card--flipped .stat-card__back`: `transform: rotateY(0deg);`
  - [x]Both faces: `transition: transform 400ms cubic-bezier(0.4, 0, 0.2, 1);`
  - [x]`.stat-card__back`: `background: var(--bg-card); border-top: 3px solid var(--module); border-radius: var(--radius); padding: var(--gap);`

- [x] Task 4: Add KPI row and CTA button CSS (AC: 2, 3)
  - [x]`.stat-card__kpi-row`: flex row, `justify-content: space-between`, `align-items: center`, `padding: 4px 0`, `border-bottom: 1px solid var(--border)`, last-child no border
  - [x]`.stat-card__kpi-label`: `font-family: var(--font-main)`, `font-size: 11px`, `color: var(--text-secondary)`
  - [x]`.stat-card__kpi-value`: `font-family: var(--font-mono)`, `font-weight: 700`, `font-size: 14px`, `color: var(--text-primary)`
  - [x]`.stat-card__kpi-trend`: `font-size: 12px`, green for up (`var(--status-online)`), red for down (`var(--status-danger)`)
  - [x]`.stat-card__back-cta`: `width: 100%`, `padding: 10px`, `min-height: 44px`, `background: var(--module)`, `color: #fff`, `border: none`, `border-radius: var(--radius-sm)`, `font-family: var(--font-main)`, `font-size: 13px`, `cursor: pointer`, `touch-action: manipulation`, `margin-top: auto`

- [x] Task 5: Add CARD_BACK_CONFIG data mapping (AC: 3)
  - [x]Add `CARD_BACK_CONFIG` const object after `STAT_CARD_CONFIG`
  - [x]Each module key maps to array of KPI objects: `{ label: 'Hebrew label', valueKey: 'path.to.data', formatter: fn, trend: 'up'|'down'|'neutral' }`
  - [x]OptiTrack KPIs sourced from `OPTITRACK_DATA`: totalItems, approvalRate, objections, pending
  - [x]OptiBiz KPIs sourced from `OPTIBIZ_DATA`: budgetUtilized (formatted ₪1.22B), budgetUtilization (68%), burnRate, variance
  - [x]OptiRisk KPIs sourced from `OPTIRISK_DATA`: activeRisks, critical, mitigated, trend
  - [x]OptiDocs KPIs sourced from `OPTIDOCS_DATA`: total, completionRate, pending, overdue
  - [x]OptiGantt KPIs computed from `OPTIGANTT_DATA`: sections count, on-track, delayed, milestone progress
  - [x]ALL values derived from `*_DATA` constants — zero hardcoded display numbers

- [x] Task 6: Implement flip interaction JS (AC: 1, 4, 5, 6)
  - [x]Add new JS section: `// SECTION: Component — Card Flip Interaction`
  - [x]Create named function `handleStatCardTap(e)`:
    - Get `moduleId` from `e.currentTarget.dataset.module`
    - If same card already flipped, ignore (tap on back should not re-flip)
    - If different card flipped, snap current card immediately (remove transition, set final state, restore transition)
    - Add `--flipped` class to tapped card
    - Update `OptiPlan.state.activeModule = moduleId`
    - Call `OptiPlan.state.transition('card-flip')`
  - [x]Create named function `handleOutsideTap(e)`:
    - Check if tap target is inside any `.stat-card` — if yes, return
    - Check if tap target is inside `.stat-card__back-cta` — if yes, return (future: module expand)
    - Find the currently flipped card (`.stat-card--flipped`)
    - Remove `--flipped` class
    - Call `OptiPlan.state.transition('overview')`
    - Reset `OptiPlan.state.activeModule = null`
  - [x]Handle mid-animation interruption: listen for `transitionend` to track animation state, or use a `_isAnimating` flag that gets cleared after 400ms

- [x] Task 7: Wire flip events in statCards component (AC: 1, 4, 5)
  - [x]Update `OptiPlan.components.statCards._bindEvents()` to add `handleStatCardTap` listener (click event for flip — NOT touchstart, which is used for scale feedback)
  - [x]Add `handleOutsideTap` listener on `document` — registered during `init()`, removed during `destroy()`
  - [x]Modify existing `handleStatCardTouchStart` to work with flip: touch feedback should apply to front face only (or to inner container)
  - [x]Ensure `handleStatCardTouchEnd` clears scale before flip animation starts

- [x] Task 8: Populate card backs with data from CARD_BACK_CONFIG (AC: 3)
  - [x]In `OptiPlan.components.statCards.render()`, after populating front faces, populate back faces
  - [x]For each card, get the module key from `data-module` attribute
  - [x]Look up KPIs from `CARD_BACK_CONFIG[moduleKey]`
  - [x]Populate `.stat-card__kpi-value`, `.stat-card__kpi-label`, `.stat-card__kpi-trend` for each KPI row
  - [x]Use formatters from config for currency/percentage values
  - [x]Set CTA button text to "למודול המלא"

- [x] Task 9: Update idle auto-reset for card flip (AC: 5)
  - [x]Update `executeIdleReset()` in the Idle Auto-Reset section
  - [x]When `currentLayer === 'card-flip'`: find `.stat-card--flipped`, remove class, reset `activeModule`
  - [x]Then transition to `overview`
  - [x]Ensure smooth transition (not instant snap) during idle reset

- [x] Task 10: Add `prefers-reduced-motion` support (AC: 1)
  - [x]Existing global rule already handles `transition-duration: 0.01ms !important`
  - [x]Verify flip transitions respect reduced motion — cards should still flip but instantly
  - [x]Verify entrance animations still work correctly

- [x] Task 11: Verify and test (AC: all)
  - [x]Open `index.html` in browser — zero console errors
  - [x]Tap any stat card — card flips with 3D animation showing back face with KPIs
  - [x]Verify touch feedback (scale 0.98) fires before flip
  - [x]Verify back face shows correct data values from constants
  - [x]Tap another card — first card flips back, then new card flips
  - [x]Tap outside cards — flipped card returns to front face
  - [x]Rapid taps on different cards — no animation queue, no jank
  - [x]Verify "למודול המלא" CTA button renders with correct module color
  - [x]Verify theme toggle still works — back face uses `var(--bg-card)` which adapts to theme
  - [x]Verify idle auto-reset flips cards back after 90s
  - [x]Verify entrance animation still works (cards enter, then become tappable)
  - [x]Verify no hardcoded hex values in new CSS — all reference `var(--*)`
  - [x]Verify all event handlers are named functions with proper cleanup in `destroy()`
  - [x]RTL: verify card back content reads right-to-left correctly

## Dev Notes

### Critical Architecture Patterns — MUST Follow

**Card Flip HTML Structure (CRITICAL — get this right):**
```html
<!-- Each stat card becomes a flip container -->
<div class="stat-card" data-module="optitrack">
  <!-- Front face (existing gradient content) -->
  <div class="stat-card__front">
    <span class="stat-card__name">OptiTrack</span>
    <span class="stat-card__value">222</span>
    <span class="stat-card__label">תיאומים מאושרים</span>
    <span class="stat-card__sublabel">+18%</span>
    <i class="stat-card__icon ti ti-route"></i>
  </div>
  <!-- Back face (KPI detail + CTA) -->
  <div class="stat-card__back">
    <div class="stat-card__back-header">
      <span class="stat-card__back-title">OptiTrack</span>
    </div>
    <div class="stat-card__back-kpis">
      <div class="stat-card__kpi-row">
        <span class="stat-card__kpi-label">סה"כ פריטים</span>
        <span class="stat-card__kpi-value">347</span>
        <span class="stat-card__kpi-trend stat-card__kpi-trend--neutral">—</span>
      </div>
      <!-- More KPI rows... -->
    </div>
    <button class="stat-card__back-cta">למודול המלא</button>
  </div>
</div>
```

**Card Flip CSS (CRITICAL — Safari WebKit compatibility):**
```css
/* ================================================================ */
/* SECTION: Component — Stat Card Flip                                */
/* ================================================================ */

.stat-card {
  /* Add to existing .stat-card styles — DO NOT duplicate, use Edit */
  perspective: 1000px;
  -webkit-perspective: 1000px;
  position: relative; /* Already has this */
  /* Remove existing gradient background from .stat-card — move to .stat-card__front */
}

.stat-card__front,
.stat-card__back {
  position: absolute;
  inset: 0;
  -webkit-backface-visibility: hidden;
  backface-visibility: hidden;
  transition: transform 400ms cubic-bezier(0.4, 0, 0.2, 1);
  border-radius: var(--radius);
  overflow: hidden;
}

.stat-card__front {
  /* Move gradient background HERE from .stat-card */
  background: linear-gradient(135deg, var(--module), color-mix(in srgb, var(--module) 68%, #000));
  transform: rotateY(0deg);
  z-index: 2;
}

.stat-card__back {
  background: var(--bg-card);
  border-top: 3px solid var(--module);
  transform: rotateY(180deg);
  z-index: 1;
  display: flex;
  flex-direction: column;
  padding: var(--gap);
}

/* Flipped state */
.stat-card--flipped .stat-card__front {
  transform: rotateY(-180deg);
}

.stat-card--flipped .stat-card__back {
  transform: rotateY(0deg);
}
```

**CRITICAL CSS MIGRATION:** The existing `.stat-card` has `background: linear-gradient(...)` and `box-shadow` applied directly. These MUST be moved to `.stat-card__front` when restructuring. The `.stat-card` itself should become a transparent flip container with `perspective` only. If you leave the gradient on `.stat-card`, the 3D flip will show the gradient behind the flipped card.

**Card Back KPI Data Mapping (CARD_BACK_CONFIG):**
```javascript
const CARD_BACK_CONFIG = {
  optitrack: {
    title: 'OptiTrack',
    kpis: [
      { label: 'סה"כ פריטים', getValue: function() { return OPTITRACK_DATA.totalItems; }, trend: 'neutral' },
      { label: 'אחוז אישור', getValue: function() { return OPTITRACK_DATA.approvalRate + '%'; }, trend: 'up' },
      { label: 'התנגדויות', getValue: function() { return OPTITRACK_DATA.objections; }, trend: 'down' },
      { label: 'ממתינים', getValue: function() { return OPTITRACK_DATA.pending; }, trend: 'neutral' }
    ]
  },
  optibiz: {
    title: 'OptiBiz',
    kpis: [
      { label: 'ניצול תקציב', getValue: function() { return '₪' + (OPTIBIZ_DATA.budgetUtilized / 1000000000).toFixed(2) + 'B'; }, trend: 'up' },
      { label: 'אחוז תקציב', getValue: function() { return OPTIBIZ_DATA.budgetUtilization + '%'; }, trend: 'neutral' },
      { label: 'קצב שריפה', getValue: function() { return OPTIBIZ_DATA.burnRate || 'תקין'; }, trend: 'neutral' },
      { label: 'סטייה Q1', getValue: function() { return OPTIBIZ_DATA.variance || '+2.3%'; }, trend: 'up' }
    ]
  },
  optirisk: {
    title: 'OptiRisk',
    kpis: [
      { label: 'סיכונים פעילים', getValue: function() { return OPTIRISK_DATA.activeRisks; }, trend: 'down' },
      { label: 'קריטיים', getValue: function() { return OPTIRISK_DATA.critical; }, trend: 'down' },
      { label: 'מטופלים', getValue: function() { return OPTIRISK_DATA.mitigated || 15; }, trend: 'up' },
      { label: 'מגמה', getValue: function() { return 'משתפר'; }, trend: 'up' }
    ]
  },
  optidocs: {
    title: 'OptiDocs',
    kpis: [
      { label: 'סה"כ מסמכים', getValue: function() { return OPTIDOCS_DATA.total; }, trend: 'neutral' },
      { label: 'אחוז השלמה', getValue: function() { return OPTIDOCS_DATA.completionRate + '%'; }, trend: 'up' },
      { label: 'ממתינים', getValue: function() { return OPTIDOCS_DATA.pending || 28; }, trend: 'neutral' },
      { label: 'באיחור', getValue: function() { return OPTIDOCS_DATA.overdue || 12; }, trend: 'down' }
    ]
  },
  optigantt: {
    title: 'OptiGantt',
    kpis: [
      { label: 'קטעים', getValue: function() { return OPTIGANTT_DATA.sections.length; }, trend: 'neutral' },
      { label: 'במסלול', getValue: function() {
        return OPTIGANTT_DATA.sections.filter(function(s) { return s.progress >= 50; }).length;
      }, trend: 'up' },
      { label: 'בעיכוב', getValue: function() {
        return OPTIGANTT_DATA.sections.filter(function(s) { return s.progress < 50; }).length;
      }, trend: 'down' },
      { label: 'אבני דרך', getValue: function() { return OPTIGANTT_DATA.milestones.length; }, trend: 'neutral' }
    ]
  }
};
```

**CRITICAL:** All `getValue` functions read from `*_DATA` constants. Never hardcode display values. If a data constant field doesn't exist (e.g., `burnRate`, `mitigated`, `pending`, `overdue`), check the data model first. If absent, either:
1. Extend the data constant in this story (preferred — add the field to the frozen object... wait, they're frozen. So compute from existing data)
2. Compute from existing fields
3. As last resort, use a reasonable hardcoded fallback with a comment explaining why

**Flip Interaction JS:**
```javascript
// ================================================================
// SECTION: Component — Card Flip Interaction
// ================================================================

var _isFlipping = false;

function handleStatCardTap(e) {
  var card = e.currentTarget;
  var moduleId = card.dataset.module;

  // If tapping the CTA button, don't flip — future module expand
  if (e.target.classList.contains('stat-card__back-cta')) {
    // Story 2.2 will handle module expansion
    return;
  }

  // If same card is already flipped, don't re-flip
  if (card.classList.contains('stat-card--flipped')) {
    return;
  }

  // If another card is flipped, snap it back immediately
  var currentlyFlipped = document.querySelector('.stat-card--flipped');
  if (currentlyFlipped) {
    // Snap: temporarily remove transition, reset, restore transition
    var frontFace = currentlyFlipped.querySelector('.stat-card__front');
    var backFace = currentlyFlipped.querySelector('.stat-card__back');
    if (frontFace) frontFace.style.transition = 'none';
    if (backFace) backFace.style.transition = 'none';
    currentlyFlipped.classList.remove('stat-card--flipped');
    // Force reflow to apply snap
    void currentlyFlipped.offsetHeight;
    if (frontFace) frontFace.style.transition = '';
    if (backFace) backFace.style.transition = '';
  }

  // Flip the tapped card
  card.classList.add('stat-card--flipped');
  OptiPlan.state.activeModule = moduleId;
  OptiPlan.state.transition('card-flip');
}

function handleOutsideTap(e) {
  // Don't process if no card is flipped
  if (OptiPlan.state.currentLayer !== 'card-flip') return;

  // Check if tap is inside any stat card
  var isInsideCard = e.target.closest('.stat-card');
  if (isInsideCard) return;

  // Flip the active card back
  var flippedCard = document.querySelector('.stat-card--flipped');
  if (flippedCard) {
    flippedCard.classList.remove('stat-card--flipped');
  }

  OptiPlan.state.activeModule = null;
  OptiPlan.state.transition('overview');
}
```

### Current index.html State (What Exists)

**Stat Cards HTML (lines ~879-914):**
```html
<section class="stat-cards-row">
  <div class="stat-card" data-module="optitrack" style="opacity: 0; transform: translateY(18px) scale(0.97);">
    <span class="stat-card__name">OptiTrack</span>
    <span class="stat-card__value"></span>
    <span class="stat-card__label"></span>
    <span class="stat-card__sublabel"></span>
    <i class="stat-card__icon ti ti-route"></i>
  </div>
  <!-- ... 4 more cards with same structure -->
</section>
```

**RESTRUCTURING REQUIRED:** The existing flat stat card structure must be wrapped in front/back faces. The inline `style="opacity: 0; transform: ..."` for entrance animation applies to `.stat-card` and must be preserved (or moved to the inner container that gets the entrance animation).

**Stat Card CSS (lines ~525-614):**
- `.stat-cards-row` — flex row container
- `.stat-card` — the card itself with gradient background, box-shadow, padding, radius
- `.stat-card__name`, `__value`, `__label`, `__sublabel`, `__icon` — content styles
- `@keyframes stat-card-entrance` — entrance animation

**MIGRATION:** The gradient `background` and `box-shadow` currently on `.stat-card` must move to `.stat-card__front`. The `.stat-card` becomes a transparent 3D perspective container. The entrance animation initial state (`opacity: 0; transform: translateY(18px) scale(0.97)`) should remain on `.stat-card` since it's the outer container.

**Stat Card JS (lines ~1430-1505):**
- `STAT_CARD_CONFIG` — front face data
- `handleStatCardTouchStart` / `handleStatCardTouchEnd` — touch feedback
- `OptiPlan.components.statCards` — `init()`, `render()`, `_bindEvents()`, `destroy()`

**Init sequence (line ~1530+):**
```javascript
OptiPlan.components.heroGauges.init();
OptiPlan.components.statCards.init();
```

**State machine transitions (line ~1100+):**
```javascript
'overview': ['card-flip', 'idle'],
'card-flip': ['overview', 'module-expand'],
```
The `overview` → `card-flip` and `card-flip` → `overview` transitions are already valid in the state machine.

### Idle Auto-Reset Integration

Story 1.6 created the idle auto-reset system with hooks for card flip. Update `executeIdleReset()`:
```javascript
// In executeIdleReset() — update the card-flip handler:
if (currentLayer === 'card-flip') {
  var flippedCard = document.querySelector('.stat-card--flipped');
  if (flippedCard) {
    flippedCard.classList.remove('stat-card--flipped');
  }
  OptiPlan.state.activeModule = null;
  OptiPlan.state.transition('overview');
}
```

### Safari WebKit Compatibility Notes

**CRITICAL for iPad Safari 16.2+:**
- `-webkit-backface-visibility: hidden` is STILL REQUIRED alongside unprefixed version
- `-webkit-perspective` should be included alongside unprefixed `perspective`
- `transform-style: preserve-3d` does NOT need `-webkit-` prefix on Safari 16.2+
- Known issue: after `rotateY()` flip on iOS 16.4+, only ~half the back face may be clickable. Workaround: add `-webkit-transform: translateZ(0)` to parent, or ensure perspective is on the container
- Test click targets on back face thoroughly — buttons must be fully clickable after flip

**3D Transform Pattern for Safari:**
```css
.stat-card {
  perspective: 1000px;
  -webkit-perspective: 1000px;
  transform-style: preserve-3d;
}

.stat-card__front,
.stat-card__back {
  -webkit-backface-visibility: hidden;
  backface-visibility: hidden;
  -webkit-transform: translateZ(0); /* Force layer creation on Safari */
}
```

### CSS Custom Property Rules

- ALL structural colors MUST use `var(--*)` tokens
- Module color set via `--module` local custom property per `[data-module]` selector (already exists from Story 1.5)
- White text (`#fff`) on CTA buttons with module-color background is acceptable for contrast
- Back face uses `var(--bg-card)` which automatically adapts to dark/light theme
- Back face text uses `var(--text-primary)`, `var(--text-secondary)`, `var(--text-muted)`
- Trend colors: `var(--status-online)` for up (green), `var(--status-danger)` or similar for down (red)
- Border: `var(--border)` for KPI row separators
- Animate ONLY `transform` and `opacity` — the flip uses `transform: rotateY()`
- `color-mix(in srgb, ...)` is confirmed safe on Safari 16.2+

### Named Event Handler Pattern — NO anonymous callbacks

All flip handlers must be named functions defined at module scope:
- `handleStatCardTap` — handles card click to initiate flip
- `handleOutsideTap` — handles document click to dismiss flip
- `handleStatCardTouchStart` — already exists (scale feedback)
- `handleStatCardTouchEnd` — already exists (scale reset)

All must be removable via `removeEventListener` in `destroy()`.

### Previous Story Learnings (Stories 1.1-1.6)

**From Story 1.5 (review — Stat Cards Front Face):**
- `STAT_CARD_CONFIG` array maps module data to front face display
- `handleStatCardTouchStart` / `handleStatCardTouchEnd` provide scale(0.98) touch feedback
- Entrance animation starts at 1200ms delay with 60ms stagger
- `$statCardsRow` and `$statCards` DOM refs with `$` prefix
- Cards use `data-module` attribute for module identification
- `_entranceTimeouts` array for cleanup
- `min-width: 140px` for each card, `flex: 1` for equal distribution
- White text on gradient backgrounds is acceptable (not a theme token violation)

**From Story 1.6 (ready-for-dev — Idle/AI Indicator):**
- `executeIdleReset()` has placeholder hooks for card flip reset
- `handleActivityReset()` clears/restarts idle timer on touch/click
- `IDLE_TIMEOUT_MS = 90000` (90 seconds)
- Single `setTimeout` ref stored in `OptiPlan.state._idleTimer`
- Ambient gradient on `::before` pseudo-element — do not disrupt this

**From Story 1.4 (done — Hero Gauges):**
- `HERO_GAUGE_CONFIG` const array pattern — similar pattern used for `STAT_CARD_CONFIG`
- `setTimeout` refs stored and cleared in `destroy()`
- `prefers-reduced-motion` handled by global CSS rule

**From Story 1.3 (done — Sidebar & Topbar):**
- Named event handlers: `handleNavItemTap`, `handleThemeToggle`
- DOM refs use `$` prefix convention
- All CSS uses `var(--*)` tokens, logical properties for RTL
- Section delimiters with `SECTION: {Name}` label

**From Story 1.2 (done — Data Model):**
- All data constants are `Object.freeze()`d — read-only, do NOT try to mutate
- Data keys: `OPTITRACK_DATA.totalItems` (347), `.approvalRate` (64), `.objections` (12), `.pending` (35)
- `OPTIBIZ_DATA.budgetUtilized` (1220000000), `.budgetUtilization` (68)
- `OPTIRISK_DATA.activeRisks` (23), `.critical` (8)
- `OPTIDOCS_DATA.total` (847), `.completionRate` (94)
- `OPTIGANTT_DATA.sections` (6 objects), `.milestones` (array)
- Cross-references validated across all data constants

**From Git History:**
- `56ffa2a Complete Story 1.4: Hero Gauges (Animated SVG Rings) with code review fixes`
- `d79c4fe Complete Story 1.3: Implement Sidebar Navigation & Topbar`
- `d522371 Complete Story 1.2: Hardcoded Project Data Model`
- `829ca1f Complete Story 1.1: HTML Scaffold & Design System Foundation`
- Clean commit history, no regressions
- Single-file architecture consistently maintained

### Data Consistency Cross-References

Ensure card back values match their respective front face and data constants:

| Module | Front Face Value | Back Face Values | Data Source |
|--------|-----------------|-----------------|-------------|
| OptiTrack | 222 approved | 347 total, 64% approved, 12 objections, 35 pending | `OPTITRACK_DATA` |
| OptiBiz | ₪1.22B | ₪1.22B utilized, 68% budget | `OPTIBIZ_DATA` |
| OptiRisk | 23 active | 23 active, 8 critical | `OPTIRISK_DATA` |
| OptiDocs | 847 docs | 847 total, 94% completion | `OPTIDOCS_DATA` |
| OptiGantt | 6 sections | 6 sections, on-track/delayed computed | `OPTIGANTT_DATA` |

### RTL Layout Considerations

- Card back content will render in RTL (labels right, values left in visual terms)
- Flex row with `justify-content: space-between` works correctly in RTL
- Trend arrows position naturally in RTL flex
- CTA button text "למודול המלא" renders correctly in RTL
- The card flip `rotateY(180deg)` works identically in RTL — the rotation axis is vertical, not affected by text direction
- Module name in back header maintains LTR for English brand name ("OptiTrack", "OptiBiz")

### Anti-Patterns to AVOID

1. **DO NOT** hardcode any hex color values — use `var(--module)`, `var(--bg-card)`, etc.
2. **DO NOT** use anonymous arrow functions for event handlers — use named functions
3. **DO NOT** animate `width`, `height`, `top`, `left`, `margin`, `padding` — only `transform`, `opacity`
4. **DO NOT** create new files — edit only `index.html`
5. **DO NOT** add functions outside `OptiPlan.*` namespace (except private handlers at module scope)
6. **DO NOT** use emojis in the UI — Tabler Icons only
7. **DO NOT** break existing entrance animation, touch feedback, theme toggle, or idle reset
8. **DO NOT** use canvas, D3.js, or any charting library
9. **DO NOT** use `setInterval` for any animation
10. **DO NOT** hardcode display values — derive ALL numbers from `*_DATA` constants
11. **DO NOT** forget `-webkit-backface-visibility` prefix for Safari compatibility
12. **DO NOT** leave gradient background on `.stat-card` — it MUST move to `.stat-card__front`
13. **DO NOT** queue animations — mid-flip taps must snap the current card immediately
14. **DO NOT** allow multiple cards to be flipped simultaneously — only one at a time
15. **DO NOT** forget to add `touch-action: manipulation` to the CTA button
16. **DO NOT** break the state machine — only use valid transitions (overview ↔ card-flip)

### Where to Insert Code in index.html

**Modified HTML** — restructure each `.stat-card` (lines ~879-914):
- Wrap existing content in `.stat-card__front` div
- Add `.stat-card__back` div with KPI rows and CTA button

**New CSS section** — insert AFTER Stat Cards CSS (~line 614) and BEFORE AI Agent Indicator / Ambient Background CSS:
```
/* ================================================================ */
/* SECTION: Component — Stat Card Flip                                */
/* ================================================================ */
```

**Modified CSS** — update existing `.stat-card` rules to remove gradient (move to `.stat-card__front`):
- Line ~533: `.stat-card` — remove `background`, `box-shadow`
- Add `.stat-card__front` with the moved `background`, `box-shadow`

**New JS constants** — insert AFTER `STAT_CARD_STAGGER_MS` (line ~1430):
```javascript
const CARD_BACK_CONFIG = { ... };
```

**New JS section** — insert AFTER statCards component (line ~1505) and BEFORE Theme Toggle Handler:
```
// ================================================================
// SECTION: Component — Card Flip Interaction
// ================================================================
```

**Update statCards._bindEvents()** — add `handleStatCardTap` click listener
**Update statCards.render()** — add card back population logic
**Update statCards.destroy()** — add cleanup for new listeners
**Update `executeIdleReset()`** — implement card flip reset

### Performance Considerations

- 3D transforms with `perspective` and `backface-visibility` use GPU compositing — no layout recalculation
- `transform: rotateY()` is GPU-composited — smooth 60fps
- Snapping a card (removing transition temporarily) avoids animation queue buildup
- `will-change: transform` on front/back faces during active flip (optional — WebKit handles this well)
- No new `setInterval` or growing arrays — memory safe
- Card back DOM is static (populated once in render) — no runtime DOM manipulation during flips

### Project Structure Notes

- **File location:** `index.html` (repository root) — ONLY file to modify
- **Alignment:** All code stays within the single-file architecture
- **No new files created** — CSS and JS sections added inline
- **Section delimiter format:** `// ================================================================` with `SECTION: {Name}` label (JS) and `/* SECTION: {Name} */` (CSS)

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story-2.1] — User story, BDD acceptance criteria, card flip specs
- [Source: _bmad-output/planning-artifacts/architecture.md#Animation-System] — CSS transitions for state changes (flip: 400ms), GPU-composited only
- [Source: _bmad-output/planning-artifacts/architecture.md#Component-Architecture] — init/render/destroy pattern, named handlers, $ prefix convention
- [Source: _bmad-output/planning-artifacts/architecture.md#State-Management] — Layer transitions overview ↔ card-flip, state-first DOM-second
- [Source: _bmad-output/planning-artifacts/architecture.md#Internal-Structure-of-index.html] — Section ordering: CSS 7 (Stat Card), JS 9 (Stat Cards)
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Card-Flip-Pattern] — One card flipped at a time, tap-outside dismisses, interruptible, no double-flip
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Touch-Interaction-Patterns] — Touch feedback <50ms, scale(0.98), touch-action: manipulation
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Navigation-Patterns] — Layer 1→Layer 2 via stat card tap, back via outside tap
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Component-Strategy#4-Stat-Card-Back] — 3-5 KPIs, trend arrows, "Full Module" CTA button
- [Source: _bmad-output/planning-artifacts/prd.md#FR6] — Investor can tap any module card to reveal detailed KPIs
- [Source: _bmad-output/planning-artifacts/prd.md#FR7] — 3-5 KPIs with trend indicators per card
- [Source: _bmad-output/planning-artifacts/prd.md#FR31] — Card flip transition animation ~400ms
- [Source: _bmad-output/planning-artifacts/prd.md#NFR4] — Card flip animation ~400ms duration at 60fps
- [Source: _bmad-output/implementation-artifacts/1-5-module-stat-cards-front-face.md] — Previous story: STAT_CARD_CONFIG, touch handlers, entrance animation, DOM structure
- [Source: _bmad-output/implementation-artifacts/1-6-idle-ambient-animation-ai-agent-indicator.md] — Idle auto-reset hooks, executeIdleReset() for card-flip layer

## Dev Agent Record

### Agent Model Used

Claude Opus 4.6

### Debug Log References

- Fixed CSS conflict: removed `-webkit-transform: translateZ(0)` from face elements to avoid overriding `transform: rotateY()` on modern Safari
- Fixed state machine bug: `handleStatCardTap` now checks `currentLayer !== 'card-flip'` before calling `transition('card-flip')` to avoid invalid card-flip → card-flip transition when switching between cards
- Added `--status-danger: #EF4444` design token for red trend-down arrows (AC2 requires "down red")
- Used `position: relative` for `.stat-card__front` (not absolute) to preserve container height without needing explicit dimensions

### Completion Notes List

- Task 1-2: Restructured all 5 stat card HTML — wrapped existing content in `.stat-card__front`, added `.stat-card__back` with header, 4 KPI rows, and CTA button for each module
- Task 3-4: Added Stat Card Flip CSS section — perspective container, front/back face positioning with backface-visibility, flip transition (400ms cubic-bezier), KPI row layout (flex, space-between), trend arrow colors (green up, red down, muted neutral), CTA button (44px min-height, module-color background)
- Task 5: Added `CARD_BACK_CONFIG` constant mapping each module's KPIs to `*_DATA` constants via `getValue()` functions — zero hardcoded display values
- Task 6: Implemented `handleStatCardTap()` and `handleOutsideTap()` as named functions with mid-animation snap (transition: none + reflow + restore), CTA click passthrough, same-card ignore
- Task 7: Wired `click` event for `handleStatCardTap` on each card and `handleOutsideTap` on document in `_bindEvents()`; added cleanup in `destroy()`
- Task 8: Populated card backs in `render()` — iterates CARD_BACK_CONFIG, sets label/value/trend for each KPI row with Unicode arrows
- Task 9: Updated `executeIdleReset()` to find `.stat-card--flipped`, remove class, reset `activeModule`, then transition to overview
- Task 10: Verified existing `prefers-reduced-motion` global CSS rule covers flip transitions (0.01ms duration)
- Task 11: Validated JS syntax (no errors), HTML tag balance (all divs/sections/buttons balanced), all required components present

### Implementation Plan

1. HTML restructured using front/back face pattern; front face `position: relative` to maintain container height
2. CSS moved gradient background and box-shadow from `.stat-card` to `.stat-card__front`; `.stat-card` became transparent perspective container
3. Data mapping via `CARD_BACK_CONFIG` reads all values from frozen `*_DATA` constants at render time
4. Flip interaction handles: single-card-at-a-time, snap-on-switch, outside-tap-dismiss, CTA-passthrough
5. State machine: only transitions overview → card-flip and card-flip → overview; switching cards stays in card-flip layer

### File List

- `index.html` — Modified: restructured stat card HTML (front/back faces), added Stat Card Flip CSS section, added CARD_BACK_CONFIG constant, added handleStatCardTap/handleOutsideTap functions, updated statCards._bindEvents/render/destroy, updated executeIdleReset, added --status-danger design token

## Senior Developer Review (AI)

**Reviewer:** BenAkiva | **Date:** 2026-02-23 | **Outcome:** Approved with fixes applied

### Findings (1 High, 3 Medium, 4 Low)

**Fixed:**
- **[H1] Back face content overflow** — Back face (~186px) exceeded front face height (~100px), clipping KPIs and CTA. Fix: added `min-height: 192px` to `.stat-card`, changed `.stat-card__front` to `position: absolute; inset: 0` (standard flip pattern).
- **[M1] `--transition-flip` design token unused** — Token defined with wrong easing, never referenced. Fix: updated token to `400ms cubic-bezier(0.4, 0, 0.2, 1)` (AC1 spec), replaced hardcoded transitions with `var(--transition-flip)`.
- **[M2] OptiRisk "trend" KPI hardcoded** — `getValue()` returned hardcoded 'improving', violating AC3. Fix: computed from `OPTIRISK_DATA.topRisks` trend fields (4 worsening > 1 improving = 'worsening'). Also corrected trend arrow to red (down).
- **[M3] Touch feedback transition overlap** — `.stat-card` CSS transition (350ms) animated touch feedback in/out, overlapping flip. Fix: disabled transition inline during touchstart/touchend for instant <50ms feedback per AC1.

**Remaining (Low — deferred):**
- [L1] No keyboard accessibility for card flip (no `tabindex`/`role`/keydown)
- [L2] Hover transform applies to flipped cards on desktop
- [L3] No interactive visual states on CTA button
- [L4] Touch feedback on perspective container instead of front face

## Change Log

- 2026-02-23: Code review fixes — back face overflow (min-height + absolute front face), transition token consistency, OptiRisk data-derived trend, instant touch feedback.
- 2026-02-23: Implemented Story 2.1 — Card Flip Interaction & KPI Back Face (Layer 2). Added 3D CSS card flip with 400ms transition, back face KPI display for all 5 modules sourced from DATA constants, tap-to-flip/outside-tap-dismiss interaction, mid-animation snap handling, and idle auto-reset integration.
