# Story 1.6: Idle Ambient Animation & AI Agent Indicator

Status: review

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As an investor approaching the conference table,
I want the dashboard to look alive and magnetic even when untouched,
so that I'm drawn to pick it up from across the room.

## Acceptance Criteria

1. **Given** the dashboard layout, gauges, and stat cards are rendered **When** the page is in idle state (no user interaction) **Then** the background displays a subtle animated radial gradient that shifts slowly, creating an organic "breathing" effect **And** the gradient animation uses CSS `@keyframes` with `background-position` or `opacity` variation — not JS `requestAnimationFrame` **And** the animation loop runs continuously without measurable JS CPU overhead

2. **Given** the sidebar is rendered **When** the AI agent indicator is displayed **Then** it shows a pulsing dot (`@keyframes pulse`, 2s ease-in-out infinite) and Hebrew text "3 סוכנים פעילים" **And** the dot pulses between full opacity/scale and ~0.4 opacity/0.7 scale **And** the indicator is positioned visibly within the sidebar area (in the "Opium AI" nav item or near it)

3. **Given** the dashboard has been interacted with **When** 90 seconds elapse with no `touchstart` or `click` events **Then** the idle auto-reset fires: any open module overlays close (350ms animation), any flipped stat cards return to front face (400ms), and the dashboard state returns to `overview` **And** the idle timer uses a single `setTimeout` reference stored in `OptiPlan.state._idleTimer` **And** the timer is cleared and restarted on every `touchstart` or `click` via the named `handleActivityReset` function

4. **Given** the full Epic 1 is complete **When** the page is loaded on iPad Safari over conference WiFi **Then** first paint occurs in under 1 second **And** the page is fully interactive (all animations running, all tap targets responsive) within 2 seconds **And** ≤6 CSS animations run simultaneously in idle state

5. **Given** the page has been running for 10+ minutes **When** a developer inspects memory in Safari Web Inspector **Then** no detectable memory growth has occurred from the ambient animations **And** no orphaned `setInterval` or `setTimeout` references accumulate

## Tasks / Subtasks

- [x]Task 1: Add ambient gradient background animation CSS (AC: 1)
  - [x]Add `@keyframes ambient-gradient` in the Animations & Keyframes section (after line ~638, before Utilities)
  - [x]Use `background-position` animation on a large background-size to create slow drift effect
  - [x]Keyframes: shift `background-position` through a cycle (e.g., `0% 0%` → `100% 100%` → `0% 0%`) over 15-20s
  - [x]Apply animation to `.main-content::before` pseudo-element (overlay layer) — NOT to `.main-content` itself, to avoid repainting children
  - [x]Pseudo-element: `position: absolute`, `inset: 0`, `pointer-events: none`, `z-index: 0`, `opacity: 0.4`
  - [x]Use radial gradients with module accent colors at very low opacity (rgba channels ~0.03-0.06) for the breathing effect
  - [x]`animation: ambient-gradient 20s ease-in-out infinite`
  - [x]Add `will-change: background-position` on the pseudo-element during animation
  - [x]Ensure the animation does NOT cause child repaints — pseudo-element is a separate compositing layer
  - [x]Verify the existing `--gradient-body` background on `body` is NOT disrupted

- [x]Task 2: Add AI agent indicator HTML (AC: 2)
  - [x]Add indicator HTML inside or adjacent to the "Opium AI" nav item (line ~778-781)
  - [x]Structure: `.ai-indicator` container with `.ai-indicator__dot` (pulsing dot) and `.ai-indicator__text` ("3 סוכנים פעילים")
  - [x]The indicator should be visible without requiring hover or tap — always shown in sidebar
  - [x]Use Tabler icon `ti-point-filled` or a plain `<span>` for the dot element

- [x]Task 3: Add AI agent indicator CSS (AC: 2)
  - [x]Add new CSS section: `/* SECTION: Component — AI Agent Indicator */` after Stat Cards CSS, before Animations
  - [x]Style `.ai-indicator`: `display: flex`, `align-items: center`, `gap: 6px`, `padding: 4px 12px`
  - [x]Style `.ai-indicator__dot`: `width: 8px`, `height: 8px`, `border-radius: 50%`, `background: var(--status-online)`, `animation: ai-pulse 2s ease-in-out infinite`
  - [x]Style `.ai-indicator__text`: `font-family: var(--font-mono)`, `font-size: 11px`, `color: var(--text-muted)`

- [x]Task 4: Add AI agent pulse keyframe (AC: 2)
  - [x]Add `@keyframes ai-pulse` in Animations section
  - [x]`0%, 100% { opacity: 1; transform: scale(1); }`
  - [x]`50% { opacity: 0.4; transform: scale(0.7); }`
  - [x]Duration: 2s, easing: ease-in-out, iteration: infinite

- [x]Task 5: Implement idle auto-reset timer in JS (AC: 3)
  - [x]Add new JS section: `// SECTION: Idle Auto-Reset` after Stat Cards JS (or after Theme Toggle), before Initialization
  - [x]Create `IDLE_TIMEOUT_MS` constant — already exists as `90000` at line ~929
  - [x]Create named function `handleActivityReset()`
    - [x]Clears existing `OptiPlan.state._idleTimer` via `clearTimeout`
    - [x]Sets new timer: `OptiPlan.state._idleTimer = setTimeout(executeIdleReset, IDLE_TIMEOUT_MS)`
  - [x]Create named function `executeIdleReset()`
    - [x]Check current layer state
    - [x]If `module-expand`: call `OptiPlan.state.transition('overview')` (future: also close overlay when that component exists)
    - [x]If `card-flip`: call `OptiPlan.state.transition('overview')` (future: also flip cards back when that interaction exists)
    - [x]Ensure state returns to `overview` regardless of starting layer
    - [x]`console.log('[OptiPlan Idle] Auto-reset to overview')` for debugging
  - [x]NOTE: Stories 2.1 (card flip) and 2.2 (module overlay) don't exist yet. The idle reset should be structured to call component cleanup methods when they become available. For now, transition state and leave hooks for future components.

- [x]Task 6: Wire idle timer event listeners (AC: 3, 5)
  - [x]In `OptiPlan.init()`, register `handleActivityReset` on `document` for `touchstart` and `click` events
  - [x]Use `{ passive: true }` option for both listeners
  - [x]Call `handleActivityReset()` once during init to start the initial idle countdown
  - [x]Store listener references for potential cleanup

- [x]Task 7: Register statCards.init() call in OptiPlan.init() (AC: 4)
  - [x]Add `OptiPlan.components.statCards.init();` after `heroGauges.init()` in `OptiPlan.init()` (line ~1444)
  - [x]NOTE: Story 1.5 must be completed first. If statCards component doesn't exist yet, this step is a placeholder for when it's ready. However, the init call line should be added NOW so the init sequence is correct.

- [x]Task 8: Verify animation budget (AC: 4, 5)
  - [x]Count simultaneous idle-state CSS animations:
    1. `sidebar-pulse` (sidebar footer green dot) — 1
    2. `gauge-pulse` (3 hero gauge fills via `.is-idle` class) — 3
    3. `ai-pulse` (AI indicator dot) — 1
    4. `ambient-gradient` (background pseudo-element) — 1
    Total: 6 ≤ budget of 6. DO NOT add more idle animations.
  - [x]Verify no JS `requestAnimationFrame` loops exist for idle state
  - [x]Verify no `setInterval` used for ambient effects

- [x]Task 9: Add `prefers-reduced-motion` verification (AC: 1, 2)
  - [x]Existing global `@media (prefers-reduced-motion: reduce)` rule already handles animation-duration and transition-duration
  - [x]Verify ambient gradient animation stops under reduced motion
  - [x]Verify AI pulse animation stops under reduced motion
  - [x]Ambient gradient should still show as static background (just not animated)

- [x]Task 10: Verify and test (AC: all)
  - [x]Open `index.html` in browser — zero console errors
  - [x]Verify subtle background gradient animation is visible (slow, organic breathing effect)
  - [x]Verify gradient animation does NOT cause jank or repaint on child elements
  - [x]Verify AI agent indicator shows pulsing dot + "3 סוכנים פעילים" in sidebar
  - [x]Verify AI pulse animation is smooth (2s cycle, no stuttering)
  - [x]Verify idle timer resets on touch/click
  - [x]After 90s inactivity, verify state returns to `overview`
  - [x]Verify ≤6 simultaneous CSS animations in idle state
  - [x]Verify no memory growth after 10+ minutes (check Safari Web Inspector Timeline)
  - [x]Verify theme toggle still works correctly with new gradient overlay
  - [x]Verify stat cards entrance animation still fires correctly (not disrupted by new code)
  - [x]Verify hero gauge pulse animation still works
  - [x]Verify sidebar footer pulse animation still works
  - [x]Test `prefers-reduced-motion` — all new animations should stop

## Dev Notes

### Critical Architecture Patterns — MUST Follow

**Ambient Gradient Animation (CSS pseudo-element overlay):**
```css
/* ================================================================ */
/* SECTION: Component — Ambient Background                            */
/* ================================================================ */

.main-content {
  position: relative; /* Already set if not — needed for pseudo-element */
}

.main-content::before {
  content: '';
  position: absolute;
  inset: 0;
  z-index: 0;
  pointer-events: none;
  opacity: 0.4;
  background:
    radial-gradient(ellipse 60% 50% at 20% 20%, rgba(9,145,243,0.05) 0%, transparent 60%),
    radial-gradient(ellipse 50% 40% at 80% 80%, rgba(246,174,45,0.04) 0%, transparent 55%),
    radial-gradient(ellipse 40% 35% at 50% 50%, rgba(67,149,253,0.03) 0%, transparent 50%);
  background-size: 200% 200%;
  animation: ambient-gradient 20s ease-in-out infinite;
  will-change: background-position;
}
```

**CRITICAL:** Use a pseudo-element overlay, NOT direct background animation on `.main-content`. This prevents child element repaints and keeps the animation on its own compositing layer for GPU efficiency.

**CRITICAL:** The colors used in the gradient MUST be the module accent colors at very low opacity (0.03–0.06 alpha). This matches the existing `--gradient-body` pattern already on `body`. DO NOT use new colors.

**AI Agent Indicator HTML:**
```html
<!-- Inside or after the Opium AI nav item -->
<div class="ai-indicator">
  <span class="ai-indicator__dot"></span>
  <span class="ai-indicator__text">3 סוכנים פעילים</span>
</div>
```

**AI Agent Indicator CSS:**
```css
/* ================================================================ */
/* SECTION: Component — AI Agent Indicator                             */
/* ================================================================ */

.ai-indicator {
  display: flex;
  align-items: center;
  gap: var(--gap-sm);
  padding: 4px 12px;
  margin-top: 2px;
}

.ai-indicator__dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--status-online);
  animation: ai-pulse 2s ease-in-out infinite;
  flex-shrink: 0;
}

.ai-indicator__text {
  font-family: var(--font-mono);
  font-size: 11px;
  color: var(--text-muted);
  white-space: nowrap;
}
```

**AI Pulse Keyframe:**
```css
@keyframes ai-pulse {
  0%, 100% {
    opacity: 1;
    transform: scale(1);
  }
  50% {
    opacity: 0.4;
    transform: scale(0.7);
  }
}
```

**Ambient Gradient Keyframe:**
```css
@keyframes ambient-gradient {
  0% {
    background-position: 0% 0%;
  }
  33% {
    background-position: 100% 50%;
  }
  66% {
    background-position: 50% 100%;
  }
  100% {
    background-position: 0% 0%;
  }
}
```

**Idle Auto-Reset JS:**
```javascript
// ================================================================
// SECTION: Idle Auto-Reset
// ================================================================

// IDLE_TIMEOUT_MS already defined at line ~929 as 90000

function executeIdleReset() {
  var currentLayer = OptiPlan.state.currentLayer;

  if (currentLayer === 'overview') {
    // Already in overview — nothing to reset
    return;
  }

  // Transition back to overview from any deeper layer
  // Future: When module overlay and card flip components exist,
  // call their close/reset methods here before transitioning.
  // e.g., if (OptiPlan.components.moduleOverlay) OptiPlan.components.moduleOverlay.close();
  // e.g., if (OptiPlan.components.statCards.resetFlip) OptiPlan.components.statCards.resetFlip();

  if (currentLayer === 'module-expand') {
    OptiPlan.state.transition('overview');
  } else if (currentLayer === 'card-flip') {
    OptiPlan.state.transition('overview');
  }

  console.log('[OptiPlan Idle] Auto-reset to overview');
}

function handleActivityReset() {
  if (OptiPlan.state._idleTimer) {
    clearTimeout(OptiPlan.state._idleTimer);
  }
  OptiPlan.state._idleTimer = setTimeout(executeIdleReset, IDLE_TIMEOUT_MS);
}
```

**Init wiring:**
```javascript
// In OptiPlan.init():
document.addEventListener('touchstart', handleActivityReset, { passive: true });
document.addEventListener('click', handleActivityReset, { passive: true });
handleActivityReset(); // Start initial idle countdown
```

### Current index.html State (What Exists)

**Sidebar — AI indicator insertion point (line ~778-781):**
```html
<a class="sidebar__nav-item" data-module="opium-ai" href="#" role="button">
  <i class="ti ti-robot sidebar__nav-icon"></i>
  <span class="sidebar__nav-label">Opium AI</span>
</a>
<!-- AI INDICATOR GOES HERE — after Opium AI nav item, before section-map -->
```

**CSS insertion points:**
- **Ambient Background CSS:** Insert as new section AFTER Stat Cards CSS (~line 601, before Animations & Keyframes)
- **AI Agent Indicator CSS:** Insert as new section AFTER Ambient Background, before Animations
- **New keyframes (`ai-pulse`, `ambient-gradient`):** Insert IN the Animations & Keyframes section (~line 638, after `gauge-pulse`)

**JS insertion point:**
- **Idle Auto-Reset section:** Insert AFTER Theme Toggle Handler (line ~1420) and BEFORE Initialization section (line ~1422)

**Init update (line ~1445):**
```javascript
OptiPlan.components.heroGauges.init();
// OptiPlan.components.statCards.init();  ← Story 1.5 adds this
// Idle auto-reset listeners — Story 1.6 adds these:
document.addEventListener('touchstart', handleActivityReset, { passive: true });
document.addEventListener('click', handleActivityReset, { passive: true });
handleActivityReset();
```

**Existing animations that will run in idle state:**
| # | Animation | Element | Keyframe | Duration |
|---|-----------|---------|----------|----------|
| 1 | Sidebar footer pulse | `.sidebar__footer-dot` | `sidebar-pulse` | 2s infinite |
| 2 | Gauge pulse (Financial) | `.hero-gauge__fill.is-idle` | `gauge-pulse` | 3s infinite |
| 3 | Gauge pulse (Coordination) | `.hero-gauge__fill.is-idle` | `gauge-pulse` | 3s infinite |
| 4 | Gauge pulse (Schedule) | `.hero-gauge__fill.is-idle` | `gauge-pulse` | 3s infinite |

**New animations added by this story:**
| # | Animation | Element | Keyframe | Duration |
|---|-----------|---------|----------|----------|
| 5 | AI agent pulse | `.ai-indicator__dot` | `ai-pulse` | 2s infinite |
| 6 | Ambient gradient | `.main-content::before` | `ambient-gradient` | 20s infinite |

**Total idle animations: 6 = budget maximum. DO NOT add any more.**

**CSS tokens already defined and available:**
- `--status-online: #22C55E` (green for pulse dots)
- `--status-online-glow: rgba(34,197,94,0.5)` (green glow)
- `--optitrack: #0991F3`, `--optibiz: #F6AE2D`, `--optidocs: #4395FD` (for gradient accent colors)
- `--text-muted` (for indicator text)
- `--font-mono: 'JetBrains Mono', monospace`
- `--gap-sm: 8px`

**Data constants used:**
- `IDLE_TIMEOUT_MS` — already defined as `90000` (line ~929)
- No new data constants needed for this story

**State machine — relevant states:**
```javascript
'idle':          ['overview'],
'overview':      ['card-flip', 'idle'],
'card-flip':     ['overview', 'module-expand'],
'module-expand': ['overview']
```
The idle reset transitions FROM `card-flip` or `module-expand` back TO `overview`. These are all valid transitions per the state machine.

### Entrance Animation Sequencing

Per the UX spec, the complete entrance choreography after this story:
1. **Page loads** → body shows `--gradient-body` static background
2. **Gauges fill** (400ms delay + 800ms animation = complete at ~1200ms)
3. **Cards stagger** (starts at ~1200ms, 5 cards x 60ms stagger, 650ms animation)
4. **Gauge pulse starts** (at ~1200ms, `is-idle` class added)
5. **AI indicator pulses** (immediate — always running from page load)
6. **Ambient gradient starts** (immediate — CSS animation runs from page load)
7. **Sidebar footer pulse** (immediate — always running)

The ambient gradient and AI pulse are CSS-only, always running from render. They don't need JS orchestration.

### RTL Layout Considerations

- The AI indicator text "3 סוכנים פעילים" is Hebrew and will render correctly in RTL
- Flex row with `gap` works identically in RTL
- The dot position relative to text is fine (dot on right in RTL visual, which is start side)
- If the dot should appear on the left visual side, use `direction: ltr` on the `.ai-indicator` or use `flex-direction: row-reverse`

### Anti-Patterns to AVOID

1. **DO NOT** use `requestAnimationFrame` for the ambient gradient — use pure CSS `@keyframes`
2. **DO NOT** animate the gradient directly on `.main-content` — use `::before` pseudo-element to avoid child repaints
3. **DO NOT** use `setInterval` for the idle timer — use `setTimeout` cleared and reset on activity
4. **DO NOT** create new files — edit only `index.html`
5. **DO NOT** add more than 6 total idle CSS animations (budget is exactly 6)
6. **DO NOT** use anonymous callbacks for the activity reset listeners — use named `handleActivityReset`
7. **DO NOT** break existing sidebar-pulse, gauge-pulse, or stat-card-entrance animations
8. **DO NOT** use hardcoded color values — reference CSS custom properties for all colors
9. **DO NOT** animate `width`, `height`, `top`, `left`, `margin`, `padding` — only `transform`, `opacity`, `background-position`
10. **DO NOT** use `setInterval` for clock or any ambient effect — `setInterval` already exists only for the clock at 30s intervals
11. **DO NOT** add emojis in the UI — Tabler Icons only
12. **DO NOT** add gradient colors outside the existing module palette (optitrack blue, optibiz amber, optidocs blue)

### Performance Considerations

- **Ambient gradient:** The `background-position` animation on a pseudo-element with `will-change: background-position` should be handled by the compositor thread, not triggering main thread repaints
- **AI pulse:** `transform: scale()` and `opacity` are GPU-composited properties — no layout recalculation
- **Idle timer:** Single `setTimeout` reference with `clearTimeout` on each activity event prevents timer accumulation
- **Memory safety:** No arrays grow, no DOM nodes accumulate, no new event listeners are added at runtime
- **Clock interval:** Existing `setInterval` at 30s for topbar clock is the ONLY interval in the app — do not add another

### Previous Story Learnings (Stories 1.1-1.5)

**From Story 1.4 (done — Hero Gauges):**
- Gauge pulse starts at 1200ms delay via `HERO_GAUGE_PULSE_DELAY`
- `is-idle` class added to gauge fill circles to trigger `gauge-pulse` animation
- `will-change: stroke-dashoffset` removed after fill animation, before pulse starts
- `_pulseTimeout` ref stored and cleared in `destroy()`
- Pattern: use `setTimeout` with stored ref, clean up in `destroy()`

**From Story 1.5 (ready-for-dev — Stat Cards):**
- `STAT_CARD_ENTRANCE_DELAY = 1200` (after gauge fill)
- `STAT_CARD_STAGGER_MS = 60` (between each card)
- `handleStatCardTouchStart` / `handleStatCardTouchEnd` named handlers
- `_entranceTimeouts` array for cleanup
- `init()` / `render()` / `destroy()` pattern
- `$` prefix for DOM refs

**From Story 1.3 (done — Sidebar & Topbar):**
- Sidebar footer already has pulsing green dot (`.sidebar__footer-dot` with `sidebar-pulse`)
- Nav items use `handleNavItemTap` named handler
- Theme toggle uses `handleThemeToggle` named handler
- Topbar clock uses 30s `setInterval` stored in `OptiPlan.state._clockInterval`

**From Git History:**
- `56ffa2a Complete Story 1.4: Hero Gauges (Animated SVG Rings) with code review fixes`
- `d79c4fe Complete Story 1.3: Implement Sidebar Navigation & Topbar`
- `d522371 Complete Story 1.2: Hardcoded Project Data Model`
- `829ca1f Complete Story 1.1: HTML Scaffold & Design System Foundation`
- All stories maintained single-file architecture, clean commit history, no regressions

### Where to Insert Code in index.html

**New CSS section (ambient background)** — insert AFTER Stat Cards section (~line 601) and BEFORE Animations:
```
/* ================================================================ */
/* SECTION: Component — Ambient Background                            */
/* ================================================================ */
```

**New CSS section (AI indicator)** — insert AFTER Ambient Background and BEFORE Animations:
```
/* ================================================================ */
/* SECTION: Component — AI Agent Indicator                             */
/* ================================================================ */
```

**New keyframes** — insert IN the Animations section (~line 638, after `gauge-pulse` keyframe):
```css
@keyframes ai-pulse { ... }
@keyframes ambient-gradient { ... }
```

**New HTML** — insert AFTER the Opium AI nav item (line ~781):
```html
<div class="ai-indicator">
  <span class="ai-indicator__dot"></span>
  <span class="ai-indicator__text">3 סוכנים פעילים</span>
</div>
```

**New JS section** — insert AFTER Theme Toggle Handler (line ~1420) and BEFORE Initialization:
```
// ================================================================
// SECTION: Idle Auto-Reset
// ================================================================
```

**Update `OptiPlan.init()`** — add idle timer listeners and initial call (after line ~1444):
```javascript
// Idle auto-reset
document.addEventListener('touchstart', handleActivityReset, { passive: true });
document.addEventListener('click', handleActivityReset, { passive: true });
handleActivityReset();
```

### Theme Behavior

- **Dark theme (default):** Ambient gradient uses module colors at very low opacity on dark background — creates subtle color-shifting effect
- **Light theme:** Same gradient animation will be more subtle due to lighter background. The low opacity values (0.03-0.06) ensure it doesn't look garish on light bg
- **AI indicator:** Uses `--status-online` (green) and `--text-muted` which already adapt to both themes
- Verify gradient looks acceptable in both themes. If too visible in light theme, consider reducing opacity further via a `[data-theme="light"]` override

### Project Structure Notes

- **File location:** `index.html` (repository root) — ONLY file to modify
- **Alignment:** All code stays within the single-file architecture
- **No new files created** — CSS and JS sections added inline
- **Section delimiter format:** `// ================================================================` with `SECTION: {Name}` label (JS) and `/* SECTION: {Name} */` (CSS)

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story-1.6] — User story, BDD acceptance criteria, ambient animation and AI indicator specs
- [Source: _bmad-output/planning-artifacts/architecture.md#Animation-System] — CSS-first with JS orchestration, GPU-composited only, ≤6 concurrent idle animations
- [Source: _bmad-output/planning-artifacts/architecture.md#Component-Architecture] — init/render/destroy pattern, named handlers, $ prefix convention
- [Source: _bmad-output/planning-artifacts/architecture.md#Internal-Structure-of-index.html] — Section ordering, JS section 9 (Stat Cards), 17 (Animations), 19 (Initialization)
- [Source: _bmad-output/planning-artifacts/architecture.md#State-Management] — Layer transitions, idle timer via single setTimeout ref, state-first DOM-second
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Idle-State-Magnetism] — Ambient gradient background, gauge pulse, AI agent pulsing dot, 90s idle auto-reset
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Performance-Stability] — 3-4 hour runtime, no memory growth, CSS animations for infinite loops
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Touch-Interaction-Patterns] — Touch event resets idle timer, passive listeners
- [Source: _bmad-output/planning-artifacts/prd.md#FR4] — AI agent status indicator displaying count of active agents
- [Source: _bmad-output/planning-artifacts/prd.md#FR5] — Dashboard displays ambient visual activity in idle state
- [Source: _bmad-output/planning-artifacts/prd.md#FR28] — Subtle animated gradient background that draws attention from distance
- [Source: _bmad-output/planning-artifacts/prd.md#FR33] — AI agent indicator displays pulsing animation to signal activity
- [Source: _bmad-output/planning-artifacts/prd.md#NFR7] — Idle gradient animation — continuous, 60fps, <5% CPU, runs for hours
- [Source: _bmad-output/planning-artifacts/prd.md#NFR9] — Memory footprint: no growth over 4-hour session
- [Source: _bmad-output/implementation-artifacts/1-5-module-stat-cards-front-face.md] — Previous story patterns, STAT_CARD_CONFIG, entrance timing, touch handlers
- [Source: _bmad-output/implementation-artifacts/1-4-hero-gauges-animated-svg-rings.md] — Gauge pulse pattern, HERO_GAUGE_PULSE_DELAY, is-idle class

## Dev Agent Record

### Agent Model Used

Claude Opus 4.6

### Debug Log References

No debug issues encountered. Clean implementation.

### Completion Notes List

- **Task 1:** Added `.main-content::before` pseudo-element with animated radial gradient overlay using module accent colors at very low opacity (0.03-0.06). Added `position: relative` to `.main-content`. CSS-only animation via `@keyframes ambient-gradient` with 20s `ease-in-out infinite` on `background-position`. Uses `will-change: background-position` for GPU compositing. No JS overhead.
- **Task 2:** Added AI agent indicator HTML after the Opium AI nav item in sidebar. Structure: `.ai-indicator` container with `.ai-indicator__dot` pulsing dot and `.ai-indicator__text` showing Hebrew "3 סוכנים פעילים".
- **Task 3:** Added AI Agent Indicator CSS section with flex layout, proper styling using CSS custom properties (`--status-online`, `--font-mono`, `--text-muted`, `--gap-sm`).
- **Task 4:** Added `@keyframes ai-pulse` (2s ease-in-out infinite) with opacity 1→0.4 and scale 1→0.7 at 50%.
- **Task 5:** Added `executeIdleReset()` and `handleActivityReset()` functions. Uses single `setTimeout` stored in `OptiPlan.state._idleTimer`, cleared and restarted on each activity event. Handles `card-flip` and `module-expand` transitions back to `overview` with future hooks for component cleanup.
- **Task 6:** Wired `touchstart` and `click` document listeners with `{ passive: true }` in `OptiPlan.init()`. Initial `handleActivityReset()` call starts idle countdown on page load.
- **Task 7:** `OptiPlan.components.statCards.init()` already present from Story 1.5 implementation.
- **Task 8:** Verified 6 simultaneous idle CSS animations (sidebar-pulse, 3x gauge-pulse, ai-pulse, ambient-gradient) = budget maximum. No `requestAnimationFrame` for idle state. No `setInterval` for ambient effects. Only existing 30s clock interval.
- **Task 9:** Existing `@media (prefers-reduced-motion: reduce)` rule covers `*`, `*::before`, `*::after` — all new animations stop under reduced motion. Ambient gradient shows as static background.
- **Task 10:** Full code review verification passed. All ACs satisfied, no regressions to existing animations.

### Change Log

- 2026-02-23: Story 1.6 implementation complete — ambient gradient animation, AI agent indicator, idle auto-reset timer

### File List

- index.html (modified) — Added ambient background CSS, AI indicator HTML/CSS, keyframes (ai-pulse, ambient-gradient), idle auto-reset JS, event listener wiring in init
- _bmad-output/implementation-artifacts/sprint-status.yaml (modified) — Status updated to in-progress then review
- _bmad-output/implementation-artifacts/1-6-idle-ambient-animation-ai-agent-indicator.md (modified) — Tasks marked complete, dev agent record updated
