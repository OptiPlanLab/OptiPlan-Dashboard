# Story 4.1: Contextual Tooltips with Hebrew Sales Copy

Status: ready-for-dev

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As an investor,
I want to see brief Hebrew explanatory tooltips on key dashboard elements when I tap them,
so that I understand the significance of each metric without needing anyone to narrate it to me.

## Acceptance Criteria

1. **AC1 — Tooltip Trigger & Display:**
   **Given** the Epic 1 dashboard shell is complete
   **When** an investor taps a hero gauge, the AI agent indicator, or a designated stat card element
   **Then** a tooltip popover appears with Hebrew sales-pitch copy relevant to that element
   **And** the tooltip renders with: `background: var(--bg-card)`, `border: 1px solid var(--border)`, `border-radius: var(--radius-sm)`, `box-shadow: var(--shadow-card-hover)`, max-width 280px, and a CSS triangle arrow pointing to the target element

2. **AC2 — Single Tooltip & Dismiss:**
   **Given** a tooltip is visible
   **When** an investor taps anywhere outside the tooltip or its target element
   **Then** the tooltip fades out (150ms opacity transition) and is removed from view
   **And** only one tooltip is visible at any time — tapping a new target dismisses any open tooltip before showing the new one

3. **AC3 — Tooltip Count & Hebrew Copy:**
   **Given** tooltips are implemented
   **When** a developer counts the tooltip instances
   **Then** up to 9 tooltips exist: 3 hero gauges, 1 AI indicator, and up to 5 distributed across stat card elements
   **And** every tooltip copy is in Hebrew and communicates value without sounding like a sales pitch (the "screaming the pain without saying it" approach)

4. **AC4 — Auto-Positioning:**
   **Given** a tooltip trigger is tapped
   **When** the tooltip auto-positions
   **Then** it appears above the target element by default, falling back to below if the top position would overflow the viewport
   **And** the tooltip never overflows the viewport horizontally

5. **AC5 — Touch Event Handling & Memory Safety:**
   **Given** tooltip interactions are implemented
   **When** a developer inspects the event handling
   **Then** all tooltip triggers use `touchstart` events with `click` fallback — no hover-only behavior
   **And** `-webkit-touch-callout: none` is applied to all tooltip trigger elements to prevent iOS long-press popups
   **And** all event handlers are named functions: `handleTooltipShow`, `handleTooltipDismiss`
   **And** `OptiPlan.components.tooltip` exposes `init()`, `show(target)`, `hide()`, `destroy()` methods

## Tasks / Subtasks

- [ ] **Task 1: Add tooltip CSS section** (AC: 1, 2, 4)
  - [ ] 1.1 Add CSS section `/* SECTION: Component — Tooltip */` after the AI Agent Indicator section (after line ~1843 in index.html)
  - [ ] 1.2 `.tooltip` — container: `position: fixed`, `z-index: 1100` (above module overlay at 1000), `max-width: 280px`, `background: var(--bg-card)`, `border: 1px solid var(--border)`, `border-radius: var(--radius-sm)`, `box-shadow: var(--shadow-card-hover)`, `padding: 12px 16px`, `pointer-events: none`, `opacity: 0`, `transform: translateY(6px)`, `transition: opacity 150ms cubic-bezier(0.16, 1, 0.3, 1), transform 150ms cubic-bezier(0.16, 1, 0.3, 1)`
  - [ ] 1.3 `.tooltip--visible` — `opacity: 1`, `transform: translateY(0)`, `pointer-events: auto`
  - [ ] 1.4 `.tooltip--below` — `transform: translateY(-6px)` (flip direction when positioned below)
  - [ ] 1.5 `.tooltip--below.tooltip--visible` — `transform: translateY(0)`
  - [ ] 1.6 `.tooltip__text` — `font-family: var(--font-main)`, `font-size: 13px`, `line-height: 1.5`, `color: var(--text-primary)`, `direction: rtl`, `text-align: right`
  - [ ] 1.7 `.tooltip__arrow` — CSS triangle using `::before` pseudo-element: `width: 0`, `height: 0`, `border: 6px solid transparent`, `border-top-color: var(--bg-card)` (for above placement); positioned centered below tooltip box via `position: absolute`, `bottom: -12px`, `left: 50%`, `transform: translateX(-50%)`
  - [ ] 1.8 `.tooltip--below .tooltip__arrow` — flip arrow: `border-top-color: transparent`, `border-bottom-color: var(--bg-card)`, `top: -12px`, `bottom: auto`
  - [ ] 1.9 `.tooltip__arrow::after` — 1px border effect on arrow using secondary pseudo or outline technique matching `var(--border)`
  - [ ] 1.10 `[data-tooltip]` — `-webkit-touch-callout: none`, `cursor: pointer` (visual affordance)
  - [ ] 1.11 `@media (prefers-reduced-motion: reduce)` — disable tooltip transition (`transition-duration: 0.01ms !important`)

- [ ] **Task 2: Add `data-tooltip` attributes to HTML elements** (AC: 1, 3)
  - [ ] 2.1 Hero gauge — Financial Health (`data-gauge="financial"`, line ~2392): `data-tooltip="ניטור בריאות פיננסית ב-₪1.8 מיליארד תקציב — סטיות מזוהות בזמן אמת"`
  - [ ] 2.2 Hero gauge — Coordination (`data-gauge="coordination"`, line ~2404): `data-tooltip="תיאום עם 12 רשויות ממשלתיות — בפלטפורמה אחת"`
  - [ ] 2.3 Hero gauge — Schedule (`data-gauge="schedule"`, line ~2416): `data-tooltip="מעקב לוחות זמנים ב-6 קטעי ביצוע — חריגות מזוהות אוטומטית"`
  - [ ] 2.4 AI Agent Indicator (`.ai-indicator`, line ~2328): `data-tooltip="סוכני AI סורקים מיילים, מסמכים ודוחות — מפיקים תובנות בזמן אמת"`
  - [ ] 2.5 Stat card — OptiTrack (`data-module="optitrack"`, line ~2430): `data-tooltip="בקרת תיאומים — 347 פריטים מול 5 רשויות, מכל הקטעים"`
  - [ ] 2.6 Stat card — OptiBiz (`data-module="optibiz"`, line ~2449): `data-tooltip="ניהול תזרים מזומנים — תכנון מול ביצוע, חודש בחודש"`
  - [ ] 2.7 Stat card — OptiRisk (`data-module="optirisk"`, line ~2468): `data-tooltip="מיפוי סיכונים — 23 סיכונים פעילים, 8 קריטיים, עם מגמות"`
  - [ ] 2.8 Stat card — OptiDocs (`data-module="optidocs"`, line ~2487): `data-tooltip="מעקב מסמכים ומשימות — 847 פריטים, 94% הושלמו"`
  - [ ] 2.9 Stat card — OptiGantt (`data-module="optigantt"`, line ~2506): `data-tooltip="לוח זמנים גאנט — 6 קטעים, אבני דרך, ניתוח חריגות"`

- [ ] **Task 3: Create `OptiPlan.components.tooltip` JS component** (AC: 1, 2, 4, 5)
  - [ ] 3.1 Add JS section between Touch & Event Management stub and Component — Sidebar (after line ~2896): `// SECTION: Component — Tooltip` with `// ================================================================` delimiters
  - [ ] 3.2 Create `OptiPlan.components.tooltip` object with properties: `$tooltip: null` (single tooltip DOM element), `_activeTarget: null` (currently open trigger), `_isVisible: false`
  - [ ] 3.3 Implement `init()`: create tooltip DOM element once (`document.createElement('div')`), add class `tooltip`, build inner HTML with `.tooltip__text` span and `.tooltip__arrow` div, append to `document.body`, call `_bindEvents()`
  - [ ] 3.4 Implement `_bindEvents()`: query all `[data-tooltip]` elements, loop with `forEach`, attach `handleTooltipTap` to each via `addEventListener('touchstart', handleTooltipTap, { passive: true })` and `addEventListener('click', handleTooltipTap)`, also attach `handleTooltipDismiss` to `document` on `touchstart` and `click` (for outside taps)
  - [ ] 3.5 Implement `show(target)`: read `target.dataset.tooltip` for text, set `.tooltip__text` innerHTML, calculate position (Task 4), add `tooltip--visible` class, set `_activeTarget = target`, `_isVisible = true`
  - [ ] 3.6 Implement `hide()`: remove `tooltip--visible` and `tooltip--below` classes, `_activeTarget = null`, `_isVisible = false`
  - [ ] 3.7 Implement `destroy()`: remove tooltip element from DOM, remove all event listeners using named function references, null all `$` references

- [ ] **Task 4: Implement tooltip positioning logic** (AC: 4)
  - [ ] 4.1 Create `_position(target)` method: use `target.getBoundingClientRect()` to get trigger position
  - [ ] 4.2 Calculate tooltip dimensions: temporarily make tooltip visible (opacity 0, off-screen) to measure via `this.$tooltip.offsetWidth` and `this.$tooltip.offsetHeight`
  - [ ] 4.3 Default placement (above): `top = triggerRect.top - tooltipHeight - 12` (12px gap for arrow), `left = triggerRect.left + (triggerRect.width / 2) - (tooltipWidth / 2)` (centered)
  - [ ] 4.4 Viewport overflow check — top: if `top < 8`, switch to below placement: `top = triggerRect.bottom + 12`, add `tooltip--below` class
  - [ ] 4.5 Viewport overflow check — horizontal: if `left < 8`, clamp to `left = 8`; if `left + tooltipWidth > window.innerWidth - 8`, clamp to `left = window.innerWidth - tooltipWidth - 8`
  - [ ] 4.6 Apply position: `this.$tooltip.style.top = top + 'px'`, `this.$tooltip.style.left = left + 'px'`
  - [ ] 4.7 Adjust arrow horizontal offset if tooltip was clamped (arrow should still point to trigger center)

- [ ] **Task 5: Implement event handlers** (AC: 2, 5)
  - [ ] 5.1 Create named function `handleTooltipTap(e)`: if tapped target is already active (`_activeTarget === e.currentTarget`), call `hide()` (toggle off); else call `hide()` then `show(e.currentTarget)`. Prevent card flip from also firing by `e.stopPropagation()` only on the tooltip layer (NOT on stat cards — tooltips show on front face tap before flip)
  - [ ] 5.2 **CRITICAL INTERACTION DECISION**: Tooltips on stat cards must NOT conflict with card flip. Solution: attach `data-tooltip` to the `.stat-card__front` child div (not the `.stat-card` container). The card flip handler is on `.stat-card`, so the tooltip tap on `.stat-card__front` fires first via event bubbling. OR: show tooltip on **long-press** (300ms hold) for stat cards, and on regular tap for gauges/AI indicator. **Recommended: Use the `.stat-card__front` target with short delay or handle the interaction such that the first tap shows tooltip, second tap flips.** Let the developer decide the best UX approach — document both options.
  - [ ] 5.3 **ALTERNATIVE (simpler, recommended)**: Tooltips show on stat cards ONLY when card is NOT flipped. When a card flips, tooltip auto-dismisses. Attach tooltip to `.stat-card__front` element. The flip still triggers from `.stat-card` parent. This avoids conflict because tooltip uses `touchstart` (fires first, shows tooltip) and card flip uses `click` (fires after, flips card). Tooltip shows briefly before flip — a micro-interaction reinforcing the sales copy. User sees tooltip text flash as card begins flipping.
  - [ ] 5.4 Create named function `handleTooltipDismiss(e)`: if `_isVisible` and `e.target` is NOT inside the tooltip or the `_activeTarget`, call `hide()`. Check containment via `this.$tooltip.contains(e.target)` and `this._activeTarget.contains(e.target)`
  - [ ] 5.5 Handle idle auto-reset integration: when idle reset fires (`OptiPlan.state.transition('overview')`), call `OptiPlan.components.tooltip.hide()` to dismiss any open tooltip

- [ ] **Task 6: Wire into initialization** (AC: 5)
  - [ ] 6.1 Add `OptiPlan.components.tooltip.init()` call in `OptiPlan.init()` (line ~4613 area), AFTER stat cards and hero gauges are initialized (tooltip needs those DOM elements to exist)
  - [ ] 6.2 Ensure tooltip `destroy()` is called in idle reset cleanup if needed
  - [ ] 6.3 Verify tooltip init runs after all target elements are rendered

- [ ] **Task 7: Verification** (AC: 1, 2, 3, 4, 5)
  - [ ] 7.1 Zero console errors on page load and tooltip interactions
  - [ ] 7.2 Tapping each of 3 hero gauges shows correct Hebrew tooltip above the gauge
  - [ ] 7.3 Tapping AI indicator shows Hebrew tooltip
  - [ ] 7.4 Tapping each of 5 stat card front faces shows Hebrew tooltip
  - [ ] 7.5 Only one tooltip visible at a time — tapping new target dismisses previous
  - [ ] 7.6 Tapping outside tooltip dismisses it with 150ms fade-out
  - [ ] 7.7 Tooltip auto-positions: above by default, below if near viewport top
  - [ ] 7.8 Tooltip never overflows viewport horizontally
  - [ ] 7.9 Tooltip arrow points toward the trigger element
  - [ ] 7.10 All tooltip text is Hebrew, RTL-aligned, communicates value
  - [ ] 7.11 Theme toggle works — tooltip background/border updates with theme (dark/light)
  - [ ] 7.12 Tooltip dismisses when card flips (no tooltip floating over card back)
  - [ ] 7.13 Tooltip dismisses when module overlay opens
  - [ ] 7.14 Tooltip dismisses on idle auto-reset (90s timeout)
  - [ ] 7.15 No hardcoded hex colors in tooltip CSS — all via `var(--*)` tokens
  - [ ] 7.16 Named functions only — no anonymous callbacks in event listeners
  - [ ] 7.17 `prefers-reduced-motion` disables tooltip animation
  - [ ] 7.18 Touch targets meet 44px minimum (gauges and stat cards already meet this)
  - [ ] 7.19 `-webkit-touch-callout: none` prevents iOS long-press on tooltip triggers
  - [ ] 7.20 Existing features unbroken: card flip, module overlay, sidebar nav, theme toggle, AI chat, idle reset

## Dev Notes

### Architecture Patterns & Constraints

**Single File Architecture:** ALL code goes into `index.html` (~5017 lines currently). No separate JS/CSS files. No new files.

**This story creates a NEW component:** `OptiPlan.components.tooltip`. No existing tooltip code exists — this is built from scratch.

**Component Pattern:** Follow `init()`, `show(target)`, `hide()`, `destroy()` lifecycle matching other components (statCards, heroGauges, moduleOverlay).

**Event Pattern:** Named functions for ALL event handlers. No anonymous arrow functions. Use `var` for function-scoped variables, `const` for top-level only.

**CSS Token Rule:** Zero hardcoded hex colors. All visual values via `var(--*)` custom properties.

**Animation Rule:** Only `transform` and `opacity` animated. Tooltip uses opacity + translateY transition (150ms).

### Tooltip-to-Card-Flip Interaction Strategy

**The core challenge:** Stat cards are tappable for BOTH tooltip display and card flip. These must not conflict.

**Recommended approach:**
- Attach `data-tooltip` to the `.stat-card` element itself (same as flip target)
- In `handleTooltipTap`, check if card is already flipped (`classList.contains('stat-card--flipped')`) — if flipped, don't show tooltip
- The tooltip shows on `touchstart` (fires first, ~0ms), the card flip triggers on `click` (fires after, ~50-100ms)
- Tooltip appears briefly, then card flips (tooltip auto-hides when flip animation starts)
- This creates a pleasant micro-interaction: tooltip flashes, then card flips revealing KPIs

**For hero gauges and AI indicator:** No conflict — these elements don't have other tap handlers competing. Tooltip shows and stays until dismissed.

**Integration with card flip handler:**
- In `handleStatCardTap` (existing, line ~3236): add `OptiPlan.components.tooltip.hide()` at the start
- In `handleModuleClose` / `handleBackdropTap` (existing): tooltip.hide() not needed (tooltip only shows on Layer 1)

### Hebrew Tooltip Copy Guide

The tooltip copy follows the "screaming the pain without saying it" philosophy from the UX spec. Each tooltip implies a problem that OptiPlan solves, without explicitly stating it as a sales pitch.

**Copy principles:**
- State what the metric DOES (function), not what it IS (definition)
- Include a specific number or scope to feel real
- Imply the pain of NOT having this (coordination with 12 authorities = normally chaotic)
- Max 2 lines of text in tooltip (concise)

**Tooltip copy map:**

| Target | Hebrew Copy | Pain Implied |
|--------|-------------|-------------|
| Financial Health gauge | ניטור בריאות פיננסית ב-₪1.8 מיליארד תקציב — סטיות מזוהות בזמן אמת | Without this: budget overruns go unnoticed |
| Coordination gauge | תיאום עם 12 רשויות ממשלתיות — בפלטפורמה אחת | Without this: coordination is chaos across authorities |
| Schedule gauge | מעקב לוחות זמנים ב-6 קטעי ביצוע — חריגות מזוהות אוטומטית | Without this: delays discovered too late |
| AI Indicator | סוכני AI סורקים מיילים, מסמכים ודוחות — מפיקים תובנות בזמן אמת | Without this: manual review of thousands of documents |
| OptiTrack card | בקרת תיאומים — 347 פריטים מול 5 רשויות, מכל הקטעים | Without this: permit tracking in spreadsheets |
| OptiBiz card | ניהול תזרים מזומנים — תכנון מול ביצוע, חודש בחודש | Without this: financial visibility only quarterly |
| OptiRisk card | מיפוי סיכונים — 23 סיכונים פעילים, 8 קריטיים, עם מגמות | Without this: risks discovered after damage |
| OptiDocs card | מעקב מסמכים ומשימות — 847 פריטים, 94% הושלמו | Without this: document chaos, missed deadlines |
| OptiGantt card | לוח זמנים גאנט — 6 קטעים, אבני דרך, ניתוח חריגות | Without this: schedule management in separate tools |

### CSS Insertion Point

Insert new CSS section **after** the AI Agent Indicator section (line ~1843), **before** the AI Chat Panel section (line ~1846). Use delimiter:
```css
/* ================================================================ */
/* SECTION: Component — Tooltip                                      */
/* ================================================================ */
```

### JS Insertion Point

Insert new JS section **between** Touch & Event Management stub (line ~2893) and Component — Sidebar (line ~2899). Use delimiter:
```javascript
// ================================================================
// SECTION: Component — Tooltip
// ================================================================
```

### DOM Structure (Created by JS)

```html
<!-- Single tooltip element, appended to document.body by init() -->
<div class="tooltip">
  <span class="tooltip__text">Hebrew tooltip copy here</span>
  <div class="tooltip__arrow"></div>
</div>
```

Only ONE tooltip DOM element exists. Text is updated dynamically when showing for different targets. This prevents DOM bloat.

### Existing Elements to Attach Tooltips To

| Element | Selector | Line (approx) | data-tooltip attribute |
|---------|----------|---------------|----------------------|
| Financial gauge | `.hero-gauge[data-gauge="financial"]` | ~2392 | Add |
| Coordination gauge | `.hero-gauge[data-gauge="coordination"]` | ~2404 | Add |
| Schedule gauge | `.hero-gauge[data-gauge="schedule"]` | ~2416 | Add |
| AI indicator | `.ai-indicator` | ~2328 | Add |
| OptiTrack stat card | `.stat-card[data-module="optitrack"]` | ~2430 | Add |
| OptiBiz stat card | `.stat-card[data-module="optibiz"]` | ~2449 | Add |
| OptiRisk stat card | `.stat-card[data-module="optirisk"]` | ~2468 | Add |
| OptiDocs stat card | `.stat-card[data-module="optidocs"]` | ~2487 | Add |
| OptiGantt stat card | `.stat-card[data-module="optigantt"]` | ~2506 | Add |

### z-index Stack

| Layer | z-index | Element |
|-------|---------|---------|
| Tooltip | 1100 | `.tooltip` |
| Module Overlay | 1000 | `.module-overlay` |
| Backdrop | 999 | `.module-overlay-backdrop` |
| Chat Panel | 1050 | `.chat-panel` |
| Stat Cards | 10 | `.stat-card` |

Tooltip must be above ALL other elements to remain visible regardless of layer state.

### Peer Component Patterns to Follow

**Module Overlay (OptiPlan.components.moduleOverlay)** — closest structural pattern:
- Single DOM element approach (one overlay container, content swapped)
- `init()` caches `$` references, calls `_bindEvents()`
- Named event handlers registered with both `touchstart` and `click`
- `expand(moduleId)` / `close()` mirrors tooltip `show(target)` / `hide()`
- Uses CSS class toggling for visibility (`--expanded`)

**Stat Card Touch Feedback** — reference for touch event registration:
```javascript
// From line ~3375-3376:
$card.addEventListener('touchstart', handleStatCardTouchStart, { passive: true });
$card.addEventListener('touchend', handleStatCardTouchEnd, { passive: true });
```

### Anti-Patterns (DO NOT)

1. NO hardcoded hex colors — use `var(--*)` tokens
2. NO anonymous arrow functions — named functions only
3. NO animating width/height/top/left — only `transform` and `opacity`
4. NO new files — edit `index.html` only
5. NO functions outside `OptiPlan.*` namespace
6. NO emojis in UI — Tabler Icons only
7. NO breaking existing card flip, module overlay, sidebar nav, theme toggle, idle reset, AI chat
8. NO hover-only tooltip trigger — must use touch events
9. NO multiple tooltip DOM elements — single element, text swapped
10. NO tooltip visible during card flip or module expansion — auto-dismiss
11. NO `console.error` — use `console.warn('[OptiPlan Tooltip]', ...)` if needed
12. NO growing arrays or orphaned listeners — memory-safe patterns
13. NO `let`/`const` inside functions — use `var` (top-level constants use `const`)
14. NO tooltip content in JS strings — use `data-tooltip` HTML attributes as source of truth
15. NO blocking card flip — tooltip must coexist, not replace the flip interaction

### Data Consistency Cross-References

| Tooltip Text | Data Source | Must Match |
|-------------|------------|------------|
| "₪1.8 מיליארד" in financial tooltip | PROJECT_DATA.totalBudget / 1000000000 | 1.8 |
| "12 רשויות" in coordination tooltip | 5 authorities in OPTITRACK_DATA columns + implied total | Consistent with permit matrix |
| "6 קטעי ביצוע" in schedule tooltip | PROJECT_DATA.sections.length | 6 |
| "347 פריטים" in OptiTrack tooltip | OPTITRACK_DATA.summary.totalItems | 347 |
| "5 רשויות" in OptiTrack tooltip | OPTITRACK_DATA authority columns count | 5 |
| "23 סיכונים" in OptiRisk tooltip | OPTIRISK_DATA.summary.activeRisks | 23 |
| "8 קריטיים" in OptiRisk tooltip | OPTIRISK_DATA.summary.criticalRisks | 8 |
| "847 פריטים" in OptiDocs tooltip | OPTIDOCS_DATA.summary.totalDocuments | 847 |
| "94%" in OptiDocs tooltip | OPTIDOCS_DATA.summary.completionRate | 94 |
| "6 קטעים" in OptiGantt tooltip | OPTIGANTT_DATA.sections.length | 6 |

**NOTE:** The tooltip copy includes specific numbers from data constants. If data values change, tooltip `data-tooltip` attributes in HTML must be updated to match. Consider adding a comment near the HTML attributes: `<!-- Values must match *_DATA constants -->`

### Previous Story Intelligence

**From Story 2.7 (OptiGantt — most recent implementation):**
- Codebase is ~5017 lines in index.html
- CSS sections follow strict `/* SECTION: */` delimiter pattern
- JS sections follow `// SECTION: //` delimiter pattern
- Entrance animations use CSS class toggling (`.--visible`)
- `_entranceTimeouts` array pattern for cleanup
- All `var` declarations, named functions only

**From Story 3.1 (AI Chat Panel — in progress):**
- Chat panel uses similar show/hide pattern with CSS class toggling
- Event delegation pattern for chip clicks
- Backdrop dismiss pattern (tap outside to close)
- `destroy()` properly removes event listeners

**From Story 3.2 (LLM Integration — ready-for-dev):**
- Confirms code patterns: `var` for function-scoped, `function` keyword, named handlers
- `.then()/.catch()` for promises (no async/await)
- `$` prefix for DOM references
- `console.warn('[OptiPlan ...]')` format

### Git Intelligence

**Recent 10 commits** all follow pattern: one story per commit modifying `index.html` + story `.md` file + `sprint-status.yaml`. Commit messages format: `Complete Story X.Y: {title}`.

**Code conventions confirmed across all commits:**
- `var` declarations (not `let`/`const` inside functions)
- Named handler functions (`function handleXyz(e) {}`)
- `$` prefix for DOM references (`$tooltip`, `$element`)
- Section delimiters with `// ================================================================`
- `{ passive: true }` on touchstart listeners
- `cubic-bezier(0.16, 1, 0.3, 1)` as the standard easing curve

### Project Structure Notes

- **Single file:** `index.html` is the entire application (~5017 lines)
- **CSS:** Tooltip CSS goes at section 15 (after AI Agent Indicator, before AI Chat Panel)
- **JS:** Tooltip JS goes as new component section (after Touch/Event Management stub, before Sidebar)
- **HTML:** `data-tooltip` attributes added to existing hero gauge, stat card, and AI indicator elements
- **No build system:** What you write ships directly
- **iPad Safari 16.2+** is the only target browser
- **RTL:** `<html lang="he" dir="rtl">`, all Hebrew labels

### Functional Requirements Coverage

- **FR36:** Investor can view contextual tooltips on key elements that explain their meaning in sales-oriented language
- **FR37:** Dashboard is self-explanatory — key elements convey purpose without external narration (tooltips enhance this)

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Epic-4-Story-4.1]
- [Source: _bmad-output/planning-artifacts/architecture.md#Component-Architecture]
- [Source: _bmad-output/planning-artifacts/architecture.md#Touch-Event-Handling]
- [Source: _bmad-output/planning-artifacts/architecture.md#Implementation-Patterns]
- [Source: _bmad-output/planning-artifacts/architecture.md#CSS-Section-Order-Component-Tooltip-Section-15]
- [Source: _bmad-output/planning-artifacts/prd.md#FR36-FR37]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Component-13-Tooltip]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Tooltip-Overload-Anti-Pattern]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Touch-Interaction-Patterns]
- [Source: _bmad-output/implementation-artifacts/2-7-optigantt-module-schedule-timeline.md — Latest pattern decisions]
- [Source: _bmad-output/implementation-artifacts/3-2-llm-api-integration-intelligent-hebrew-responses.md — Code patterns]
- [Source: index.html — Current codebase analysis ~5017 lines]

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
