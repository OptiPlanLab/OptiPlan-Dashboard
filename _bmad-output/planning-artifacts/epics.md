---
stepsCompleted:
  - step-01-validate-prerequisites
  - step-02-design-epics
  - step-03-create-stories
  - step-04-final-validation
inputDocuments:
  - _bmad-output/planning-artifacts/prd.md
  - _bmad-output/planning-artifacts/architecture.md
  - _bmad-output/planning-artifacts/ux-design-specification.md
  - _bmad-output/planning-artifacts/optiplan-v2.html
  - _bmad-output/planning-artifacts/design-system.html
---

# optiplan-dashboard - Epic Breakdown

## Overview

This document provides the complete epic and story breakdown for optiplan-dashboard, decomposing the requirements from the PRD, Architecture, UX Design Specification, Interactive Mockup, and Design System into implementable stories.

## Requirements Inventory

### Functional Requirements

FR1: Investor can view a project header displaying the project name, client, and key identifiers
FR2: Investor can view 3 hero gauges showing high-level project health metrics (financial health, coordination completion, schedule adherence)
FR3: Investor can view 5 module cards representing OptiTrack, OptiBiz, OptiRisk, OptiDocs, and OptiGantt
FR4: Investor can see an AI agent status indicator displaying the count of active agents
FR5: Dashboard displays ambient visual activity in idle state without requiring user interaction
FR6: Investor can tap any module card to reveal detailed KPIs for that module
FR7: Investor can view 3–5 KPIs with trend indicators on each revealed card
FR8: Investor can expand any module to view its full data visualization (charts, tables, detailed data)
FR9: Investor can navigate back from an expanded module to the overview
FR10: Investor can interact with all modules in any order without encountering dead ends or disabled states
FR11: Investor can access an AI chat interface from the dashboard
FR12: Investor can select from suggested questions relevant to the displayed project data
FR13: Investor can type free-text questions in Hebrew
FR14: System responds to questions in Hebrew with answers that reference specific project data points
FR15: System handles unexpected or adversarial questions with coherent, project-relevant responses
FR16: System provides pre-cached responses as fallback when the LLM API is unavailable
FR17: Investor can view a coordination permit matrix showing sections vs. regulatory authorities with approval status
FR18: Investor can view coordination progress metrics (items count, approval rate, objections)
FR19: Investor can view a recent activity feed for coordination items
FR20: Investor can view a multi-month cash flow chart showing plan vs. actual expenditure
FR21: Investor can view financial KPIs including budget utilization and overrun/underrun status
FR22: Investor can view a risk severity heat map (likelihood × impact)
FR23: Investor can view top risks with severity rating, trend, and mitigation status
FR24: Investor can view document and task tracking status with categorization
FR25: Investor can view document completion metrics and pending items
FR26: Investor can view a project schedule visualization showing sections and milestones
FR27: Investor can view schedule adherence status per project section
FR28: Dashboard displays a subtle animated gradient background that draws visual attention from a distance
FR29: Gauges animate on initial page load to convey data filling in
FR30: Module cards display staggered entrance animations on load
FR31: Card reveal transitions animate smoothly when investor taps a module card
FR32: Module expansion transitions animate smoothly when investor opens full view
FR33: AI agent indicator displays a pulsing animation to signal activity
FR34: All text content displays in Hebrew (RTL direction) using the OptiPlan design language
FR35: Dashboard displays OptiPlan branding (logo, color palette, typography)
FR36: Investor can view contextual tooltips on key elements that explain their meaning in sales-oriented language
FR37: Dashboard is self-explanatory — key elements convey purpose without external narration
FR38: All displayed data represents a realistic fictitious transit infrastructure project (Gush Dan Metro Extension)
FR39: All data values are internally consistent across modules (numbers cross-reference correctly)
FR40: Operator can load the dashboard from a single URL without configuration
FR41: Dashboard operates in long-running sessions without requiring page refresh
FR42: All non-AI features continue functioning when network connectivity is lost
FR43: AI chat displays a graceful Hebrew-language message when API is unavailable, without breaking the UI
FR44: Dashboard recovers gracefully from any interaction state (no stuck screens or unresponsive states)

### NonFunctional Requirements

NFR1: First paint < 1 second on iPad Safari over WiFi
NFR2: Fully interactive < 2 seconds from URL load
NFR3: Touch response < 100ms from tap to visual feedback
NFR4: Card flip animation ~400ms duration at 60fps
NFR5: Module expand animation ~500ms duration at 60fps
NFR6: Gauge fill animation ~800ms on initial load at 60fps
NFR7: Idle gradient animation — continuous, 60fps, < 5% CPU, runs for hours
NFR8: AI chat response < 3 seconds from question submission to first visible response
NFR9: Memory footprint: no growth over a 4-hour session (no DOM leaks, no animation frame accumulation)
NFR10: Continuous runtime 3–4 hours without page refresh or performance degradation
NFR11: Zero uncaught JS errors across any interaction path
NFR12: All CSS animations and transitions complete without jank or stutter over extended runtime
NFR13: All non-AI features operate normally without network connectivity
NFR14: AI chat silently falls back to cached responses when API is unreachable — no error states visible
NFR15: Single external network dependency — REST API call to LLM provider (Gemini or Claude)
NFR16: LLM API key embedded client-side (accepted trade-off for demo scope)
NFR17: API calls must work from GitHub Pages origin (CORS compliant)
NFR18: 5–8 pre-cached Hebrew Q&A pairs served when API is unreachable
NFR19: Google Fonts (Varela Round + JetBrains Mono) loaded with font-display: swap
NFR20: Deployed to GitHub Pages as static files — zero server infrastructure
NFR21: Single HTML file (or HTML + 1 CSS + 1 JS, max 3 files total)
NFR22: No build tools — no compilation, bundling, or transpilation
NFR23: External CDN dependencies limited to Google Fonts, Tabler Icons, and LLM API only

### Additional Requirements

**From Architecture — Structural & Pattern Requirements:**
- Hand-crafted HTML scaffold as project starter (no CLI, no framework, manual file creation)
- JavaScript organized under `OptiPlan.*` namespace: state, components, animations, modules, ai, utils, data
- CSS custom properties cascade: `:root` → `[data-palette="d"]` → `[data-skin="1"][data-theme]` → component styles
- All CSS visual values MUST reference custom properties — zero hardcoded colors, radii, or transitions
- Animate ONLY `transform` and `opacity` — never width, height, top, left, margin, or padding
- `will-change: transform` on elements during active animation phases
- State machine governs all layer transitions: `idle` → `overview` → `card-flip` → `module-expand`
- State changes are the single source of truth: always update state first, then reflect to DOM
- Named event handler functions only — no anonymous callbacks in `addEventListener` (enables cleanup)
- All sections delimited by `// ================================================================` comment blocks with `SECTION: {Name}` label
- Data constants: `PROJECT_DATA`, `OPTITRACK_DATA`, `OPTIBIZ_DATA`, `OPTIRISK_DATA`, `OPTIDOCS_DATA`, `OPTIGANTT_DATA`, `AI_CACHE`
- AI fetch uses `AbortController` with 8-second timeout; silent fallback to `AI_CACHE` on failure
- `console.warn` for caught errors — never `alert()`, never expose errors in UI
- No ES module imports — single-file constraint requires namespaced globals
- History API: `pushState` for layer changes, `popstate` listener for hardware back-button support
- `MAX_CHAT_HISTORY = 5` — cap chat array to prevent memory growth

**From UX Design & Mockup — Interaction & Visual Requirements:**
- Dark/light theme toggle via `data-theme` attribute swap on `<body>`; knob bounce easing `cubic-bezier(0.34, 1.56, 0.64, 1)`
- Palette D + Skin 1 locked: OptiTrack #0991F3, OptiBiz #F6AE2D, OptiRisk #FF7A3C, OptiDocs #4395FD, OptiGantt #0B3C5D
- Dark theme (Indigo Night): bg-body #0A0E1A, bg-sidebar #0F1328, bg-card #141833
- Light theme (Cool Mist): bg-body #F0F2F8, bg-sidebar #FFFFFF, bg-card #FFFFFF
- Stat card backgrounds: `linear-gradient(135deg, var(--module), color-mix(in srgb, var(--module) 68%, #000))` with matching box-shadow glow
- Card entrance animation: `translateY(18px) scale(0.97) → 0`, 0.65s, staggered 0/60/120/180/240ms per card
- Card flip: one card flipped at a time; tap outside dismisses; mid-animation interruption handled (snap + restart)
- Module overlay: 80% viewport width, 85% height, centered fixed, backdrop rgba(0,0,0,0.6) closes on tap
- Touch prevention: `user-scalable=no`, `-webkit-touch-callout: none`, `user-select: none`, pull-to-refresh disabled
- Touch feedback: `scale(0.98)` on `touchstart` within <50ms
- Idle auto-reset after 90 seconds of inactivity: collapse overlays, flip cards back, return to Layer 1
- Session isolation: no `localStorage`, `sessionStorage`, or cookies — fully ephemeral state
- Clock update interval: 30 seconds (not 1 second) for battery efficiency
- `@media (prefers-reduced-motion: reduce)` disables all non-essential animations
- OptiBiz amber (#F6AE2D) used only on dark/gradient backgrounds in light theme (contrast fails on white)
- No emojis anywhere in UI — Tabler Icons webfont only
- Defensive responsive breakpoints: tablet portrait (sidebar collapses to 60px icon-only), phone screen ("optimize for iPad" message), portrait iPad (rotation prompt overlay)
- Sidebar: 240px, active item = `border-right: 3px solid var(--optitrack)` + sidebar-active background
- Topbar: 58px height; footer bar: 36px height
- Grid gap: 14px (dashboard cards), 12px (stat card row)
- `color-mix()` for gradient stat card endpoints (requires Safari 16.2+ — confirmed safe for iPad 10th gen)
- Sidebar footer: pulsing green dot + API latency + version string
- `font-display: swap` on both web fonts

### FR Coverage Map

FR1: Epic 1 — Project header (project name, client, identifiers)
FR2: Epic 1 — 3 hero gauges (financial health, coordination completion, schedule adherence)
FR3: Epic 1 — 5 module cards front face (OptiTrack, OptiBiz, OptiRisk, OptiDocs, OptiGantt)
FR4: Epic 1 — AI agent status indicator with active agent count
FR5: Epic 1 — Idle-state ambient visual activity
FR6: Epic 2 — Card flip to reveal KPIs (Layer 2)
FR7: Epic 2 — 3–5 KPIs with trend indicators per card
FR8: Epic 2 — Full module expansion with charts, tables, data (Layer 3)
FR9: Epic 2 — Back navigation from expanded module to overview
FR10: Epic 2 — Zero dead ends across all modules in any interaction order
FR11: Epic 3 — AI chat panel accessible from dashboard
FR12: Epic 3 — Suggested question chips
FR13: Epic 3 — Free-text Hebrew input
FR14: Epic 3 — Hebrew responses referencing specific project data points
FR15: Epic 3 — Adversarial/unexpected question handling
FR16: Epic 3 — Pre-cached fallback responses when LLM API unavailable
FR17: Epic 2 — OptiTrack permit matrix (sections × regulatory authorities)
FR18: Epic 2 — OptiTrack coordination metrics (count, approval rate, objections)
FR19: Epic 2 — OptiTrack recent activity feed
FR20: Epic 2 — OptiBiz multi-month cash flow chart (plan vs. actual)
FR21: Epic 2 — OptiBiz financial KPIs (budget utilization, overrun/underrun)
FR22: Epic 2 — OptiRisk severity heat map (likelihood × impact)
FR23: Epic 2 — OptiRisk top risks table (severity, trend, mitigation)
FR24: Epic 2 — OptiDocs document tracking table with categorization
FR25: Epic 2 — OptiDocs completion metrics and pending items
FR26: Epic 2 — OptiGantt project schedule visualization (sections and milestones)
FR27: Epic 2 — OptiGantt schedule adherence status per section
FR28: Epic 1 — Animated gradient background (idle magnetism)
FR29: Epic 1 — Gauge fill animation on initial page load
FR30: Epic 1 — Staggered card entrance animations on load
FR31: Epic 2 — Card flip transition animation (~400ms)
FR32: Epic 2 — Module expansion transition animation (~500ms)
FR33: Epic 3 — AI agent indicator pulsing animation
FR34: Epic 1 — Full Hebrew RTL (dir="rtl", Varela Round font)
FR35: Epic 1 — OptiPlan branding (logo badge, Palette D, design system)
FR36: Epic 4 — Contextual tooltips with sales-oriented Hebrew copy
FR37: Epic 1 — Self-explanatory layout (investor understands within 30s without narration)
FR38: Epic 1 — Hardcoded Gush Dan Metro Extension project data model
FR39: Epic 4 — Cross-module data consistency audit (numbers cross-reference correctly)
FR40: Epic 1 — Single URL load (GitHub Pages, no configuration required)
FR41: Epic 4 — Long-running session stability (3–4 hours, no page refresh)
FR42: Epic 4 — All non-AI features functional without network
FR43: Epic 3 — Graceful Hebrew-language AI fallback (no broken UI on API failure)
FR44: Epic 4 — State recovery from any interaction (no stuck screens)

## Epic List

### Epic 1: Live Dashboard Shell
Investors can load the dashboard from a single URL and immediately encounter a fully branded, animated Hebrew command center — project header, 3 hero gauges filling with data, 5 module cards staggering into view, a pulsing AI indicator, and a subtle ambient gradient that draws attention from across a conference room. The Operator can verify setup in under 2 minutes on real iPad hardware.

**FRs covered:** FR1, FR2, FR3, FR4, FR5, FR28, FR29, FR30, FR34, FR35, FR37, FR38, FR40

> ⚠️ **Starter Template Note:** Story 1.1 creates the hand-crafted HTML scaffold — complete CSS custom property system (Palette D + Skin 1), OptiPlan namespace, state machine, and structural layout. All subsequent stories in this epic and all future epics build within this single file.

---

### Epic 2: Module Exploration
Investors can flip any of the 5 module cards to reveal detailed KPIs (Layer 2), then expand to full module views containing all data visualizations: OptiTrack permit matrix, OptiBiz cash flow chart, OptiRisk heat map, OptiDocs tracking table, and OptiGantt timeline. Investors can navigate freely between modules and return to the overview at any time. Zero dead ends.

**FRs covered:** FR6, FR7, FR8, FR9, FR10, FR17, FR18, FR19, FR20, FR21, FR22, FR23, FR24, FR25, FR26, FR27, FR31, FR32

---

### Epic 3: AI Intelligence
Investors can access the AI chat panel, select from suggested Hebrew question chips or type freely, and receive project-specific Hebrew-language responses from a live LLM API. If the API is unavailable, the system silently serves cached responses — the investor never sees an error state or loading failure.

**FRs covered:** FR11, FR12, FR13, FR14, FR15, FR16, FR33, FR43

---

### Epic 4: Conference Readiness & Polish
The dashboard is hardened for 3–4 hours of unattended conference use. Data values are cross-verified for consistency across all modules, contextual tooltips add silent sales narrative to key elements, idle auto-reset returns the dashboard to its magnetic state after 90 seconds, and all touch, performance, and stability targets are met for iPad Safari.

**FRs covered:** FR36, FR39, FR41, FR42, FR44

---

## Epic 1: Live Dashboard Shell

Investors can load the dashboard from a single URL and immediately encounter a fully branded, animated Hebrew command center — project header, 3 hero gauges filling with data, 5 module cards staggering into view, a pulsing AI indicator, and a subtle ambient gradient that draws attention from across a conference room. The Operator can verify setup in under 2 minutes on real iPad hardware.

### Story 1.1: HTML Scaffold & Design System Foundation

As a developer,
I want a complete `index.html` with all CSS custom properties, structural layout, and base JavaScript architecture,
So that every subsequent story has a consistent, pattern-compliant foundation to build within.

**Acceptance Criteria:**

**Given** the repository contains no application file
**When** `index.html` is created
**Then** the file opens in Safari with `<html lang="he" dir="rtl">`, zero JS console errors, and a dark-themed page background matching `--bg-body: #0A0E1A`
**And** all Palette D CSS tokens are defined on `:root` and `[data-theme]` (OptiTrack #0991F3, OptiBiz #F6AE2D, OptiRisk #FF7A3C, OptiDocs #4395FD, OptiGantt #0B3C5D)
**And** Skin 1 dark/light surface tokens are defined (`--bg-body`, `--bg-sidebar`, `--bg-card`, `--bg-input`, `--border`, `--text-primary`, `--text-secondary`, `--text-muted`)

**Given** the HTML scaffold is created
**When** the page loads
**Then** the `OptiPlan` namespace object exists with sub-namespaces: `state`, `components`, `animations`, `modules`, `ai`, `utils`, `data`
**And** `OptiPlan.state.transition()` enforces the valid state machine: `idle` → `overview` → `card-flip` → `module-expand`, rejecting invalid transitions

**Given** the scaffold is in place
**When** a developer inspects the source
**Then** all JS sections are delimited by `// ================================================================` comment blocks with `SECTION: {Name}` labels
**And** CDN links are loaded: Varela Round, JetBrains Mono, Tabler Icons webfont
**And** the layout HTML shell exists: `.sidebar` (240px), `.topbar` (58px), `.main-content` — structural containers only, no content yet

**Given** the page is loaded
**When** the theme toggle is tapped
**Then** `data-theme` attribute on `<body>` switches between `dark` and `light`
**And** all CSS custom property values cascade correctly for the new theme with no hardcoded color values remaining

**Given** the file is pushed to the `main` branch of the GitHub repository
**When** GitHub Pages serves the file
**Then** the page loads as a static HTML file with no build step, no server-side logic, and no 404 errors on any CDN dependency

---

### Story 1.2: Hardcoded Project Data Model

As a developer,
I want all project data constants defined as JavaScript objects within the scaffold,
So that every module can render realistic, internally consistent data with zero network dependency.

**Acceptance Criteria:**

**Given** the HTML scaffold from Story 1.1 exists
**When** the data constants section is added to the `<script>` block
**Then** `PROJECT_DATA` contains: project name in Hebrew, client "נת״ע", total budget 1800000000 (NIS), 6 sections (A1–A6) each with id, Hebrew name, phase, and progress percentage

**Given** the data constants are defined
**When** a developer cross-checks module data objects
**Then** `OPTIBIZ_DATA` budget utilized (1220000000) equals exactly 68% of `PROJECT_DATA.totalBudget`
**And** `OPTITRACK_DATA` total items (347) matches the value that will appear on the OptiTrack stat card
**And** `OPTIRISK_DATA` active risks (23) and critical count (8) match the values that will appear on the OptiRisk stat card
**And** `OPTIDOCS_DATA` total documents (847) and completion rate (94%) match the OptiDocs stat card values

**Given** the data constants are defined
**When** `OPTITRACK_DATA` is inspected
**Then** it contains: total coordination items, approval rate, objection count, authority breakdown array (Municipality, Israel Electric Corp, Water Authority, Communications, Antiquities Authority), and 5+ activity feed entries with Hebrew descriptions

**Given** the data constants are defined
**When** `OPTIBIZ_DATA` is inspected
**Then** it contains: a 12-element monthly cash flow array with plan and actual NIS values, financial KPIs (budget utilization, burn rate, variance), and month labels in Hebrew abbreviations

**Given** the data constants are defined
**When** `OPTIGANTT_DATA` is inspected
**Then** it contains: 6 section rows with planned start/end dates, actual start/end dates, progress percentages, and at least 3 milestone objects with dates and Hebrew labels

**Given** the data constants are defined
**When** `AI_CACHE` is inspected
**Then** it contains 6–8 Hebrew question-answer pairs covering topics: budget, schedule, risks, permits, section status, and at least one adversarial/unexpected question pattern

**Given** all data constants are defined
**When** `OptiPlan.utils.formatCurrency(1220000000)` is called
**Then** it returns a formatted Hebrew currency string using `Intl.NumberFormat('he-IL')`
**And** raw numbers are never pre-formatted in the data objects themselves

---

### Story 1.3: Sidebar Navigation & Topbar

As an investor,
I want to see an OptiPlan branded sidebar and topbar when the dashboard loads,
So that I immediately understand this is a professional, authoritative infrastructure platform.

**Acceptance Criteria:**

**Given** the scaffold and data model exist
**When** `OptiPlan.components.sidebar.init()` and `OptiPlan.components.topbar.init()` are called on `DOMContentLoaded`
**Then** the sidebar renders at 240px width with: brand badge pill ("OPTIPLAN" gradient), user avatar (initials "בע", role "מנהל פרויקט"), 5 module nav items each with Tabler icon and colored module dot, "כלים" section (Opium AI, section map, reports), "מערכת" section (settings), and footer row

**Given** the sidebar is rendered
**When** a developer inspects the sidebar footer
**Then** it shows a pulsing green dot (CSS `@keyframes pulse`, infinite), "API 18ms", and version string "v2.4.1" in JetBrains Mono
**And** the green dot uses `box-shadow: 0 0 6px rgba(34,197,94,0.5)` for the glow effect

**Given** the topbar is rendered
**When** the page loads
**Then** the topbar shows "מרכז בקרה" as the page title and "הרחבת קו מטרו, גוש דן" as subtitle
**And** a live clock displays current time in HH:MM format (JetBrains Mono), updating every 30 seconds via a single `setInterval` reference
**And** a cosmetic search input and bell icon are visible

**Given** the sidebar nav items are rendered
**When** "לוח בקרה" (Dashboard) nav item is in its default active state
**Then** it shows `border-inline-start: 3px solid var(--optitrack)` (RTL logical property) and `background: var(--sidebar-active)`
**And** all other nav items show no border and secondary text color

**Given** a nav item is tapped
**When** the named `handleNavItemTap` function fires
**Then** the tapped item becomes active (border + highlight), all others revert to default state
**And** no anonymous arrow functions are used as event handlers anywhere in the sidebar or topbar components

**Given** the sidebar and topbar are fully rendered
**When** the page is inspected
**Then** zero hardcoded color values appear in sidebar or topbar CSS — all colors reference `var(--*)` tokens

---

### Story 1.4: Hero Gauges (Animated SVG Rings)

As an investor,
I want to see 3 large animated ring gauges showing high-level project health metrics,
So that I can instantly assess overall project status without reading detailed text.

**Acceptance Criteria:**

**Given** the scaffold, data model, and layout exist
**When** `OptiPlan.components.heroGauges.init()` runs on `DOMContentLoaded`
**Then** 3 SVG ring gauges render in the hero area: Financial Health (68%), Coordination Completion (64%), Schedule Adherence (52%)
**And** each gauge has: a track `<circle>` (`stroke: var(--ring-track)`) and a fill `<circle>` using `stroke-dasharray`/`stroke-dashoffset`

**Given** the gauges are rendered
**When** 400ms have elapsed after page load
**Then** all three gauge fill circles animate from `stroke-dashoffset` = full circumference to the target value
**And** the animation duration is ~800ms using `cubic-bezier(0.16, 1, 0.3, 1)` easing
**And** the animation uses CSS `transition` on `stroke-dashoffset`, not JS `requestAnimationFrame`

**Given** the gauges are animated
**When** a developer inspects each gauge center
**Then** the percentage value displays in JetBrains Mono Bold at ≥24px
**And** a Hebrew label renders below the SVG (e.g., "בריאות פיננסית", "השלמת תיאומים", "עמידה בלוח זמנים")
**And** the gauge colors are: Financial=`var(--optibiz)`, Coordination=`var(--optitrack)`, Schedule=`var(--optigantt)`

**Given** the page is in idle state
**When** no interaction has occurred for several seconds
**Then** the gauges show a subtle slow-pulse animation (opacity or scale variation, CSS `@keyframes`, infinite loop)
**And** the pulse animation uses `transform: scale()` or `opacity` only — no layout properties animated

**Given** the page layout is RTL
**When** the gauges render
**Then** gauge elements do not reflect horizontally — SVG arcs maintain correct clockwise fill direction
**And** the gauge section fits within the main content area without horizontal overflow

---

### Story 1.5: Module Stat Cards (Front Face)

As an investor,
I want to see 5 vivid, tappable module cards each displaying a key metric,
So that I can identify all 5 platform capabilities at a glance and feel invited to explore each one.

**Acceptance Criteria:**

**Given** the scaffold and data model exist
**When** `OptiPlan.components.statCards.init()` runs
**Then** 5 gradient stat cards render in a horizontal row: OptiTrack (312 completed, +18%), OptiBiz (₪1.22B, 68%), OptiRisk (23 active, 8 critical), OptiDocs (847, 94% complete), OptiGantt (156 tasks, 22 delayed)

**Given** the stat cards are rendered
**When** a developer inspects the card CSS
**Then** each card background uses `linear-gradient(135deg, var(--module), color-mix(in srgb, var(--module) 68%, #000))`
**And** each card has `box-shadow: 0 4px 16px color-mix(in srgb, var(--module) 35%, transparent)`
**And** zero hardcoded hex values appear in stat card CSS

**Given** the stat cards are rendered
**When** a developer inspects each card's content
**Then** each card contains: module name (8px uppercase JetBrains Mono, 0.6 opacity), Tabler icon watermark (28px, 0.2 opacity, positioned bottom-left), KPI label (10px), KPI value (22px JetBrains Mono Bold), and sub-label (9px, 0.7 opacity)

**Given** the page loads
**When** the DOMContentLoaded entrance animation fires
**Then** each card animates from `opacity: 0, translateY(18px), scale(0.97)` to final position
**And** delays are staggered: 0ms, 60ms, 120ms, 180ms, 240ms per card respectively
**And** animation duration is 0.65s with `cubic-bezier(0.16, 1, 0.3, 1)` easing

**Given** a stat card is displayed
**When** an investor places their finger on it (`touchstart`)
**Then** the card scales to 0.98 within <50ms via the named `handleStatCardTouchStart` function
**And** the touch feedback fires before the flip animation begins (immediate visual acknowledgment)

**Given** the stat cards are fully rendered
**When** `OptiPlan.components.statCards` is inspected
**Then** it exposes `init()`, `render(data)`, and `destroy()` methods
**And** all DOM element references use the `$` prefix convention (e.g., `$statCardsRow`)

---

### Story 1.6: Idle Ambient Animation & AI Agent Indicator

As an investor approaching the conference table,
I want the dashboard to look alive and magnetic even when untouched,
So that I'm drawn to pick it up from across the room.

**Acceptance Criteria:**

**Given** the dashboard layout, gauges, and stat cards are rendered
**When** the page is in idle state (no user interaction)
**Then** the background displays a subtle animated radial gradient that shifts slowly, creating an organic "breathing" effect
**And** the gradient animation uses CSS `@keyframes` with `background-position` or `opacity` variation — not JS `requestAnimationFrame`
**And** the animation loop runs continuously without measurable JS CPU overhead

**Given** the sidebar is rendered
**When** the AI agent indicator is displayed
**Then** it shows a pulsing dot (`@keyframes pulse`, 2s ease-in-out infinite) and Hebrew text "3 סוכנים פעילים"
**And** the dot pulses between full opacity/scale and ~0.4 opacity/0.7 scale
**And** the indicator is positioned visibly within the sidebar or topbar area

**Given** the dashboard has been interacted with
**When** 90 seconds elapse with no `touchstart` or `click` events
**Then** the idle auto-reset fires: any open module overlays close (350ms animation), any flipped stat cards return to front face (400ms), and the dashboard state returns to `overview`
**And** the idle timer uses a single `setTimeout` reference stored in `OptiPlan.state._idleTimer`
**And** the timer is cleared and restarted on every `touchstart` or `click` via the named `handleActivityReset` function

**Given** the full Epic 1 is complete
**When** the page is loaded on iPad Safari over conference WiFi
**Then** first paint occurs in under 1 second
**And** the page is fully interactive (all animations running, all tap targets responsive) within 2 seconds
**And** ≤6 CSS animations run simultaneously in idle state

**Given** the page has been running for 10+ minutes
**When** a developer inspects memory in Safari Web Inspector
**Then** no detectable memory growth has occurred from the ambient animations
**And** no orphaned `setInterval` or `setTimeout` references accumulate

---

## Epic 2: Module Exploration

Investors can flip any of the 5 module cards to reveal detailed KPIs (Layer 2), then expand to full module views containing all data visualizations: OptiTrack permit matrix, OptiBiz cash flow chart, OptiRisk heat map, OptiDocs tracking table, and OptiGantt timeline. Investors can navigate freely between modules and return to the overview at any time. Zero dead ends.

### Story 2.1: Card Flip Interaction & KPI Back Face (Layer 2)

As an investor,
I want to tap a module card and see it flip to reveal 3–5 KPIs with trend indicators,
So that I get an immediate sense of real data depth behind each module and feel rewarded for exploring.

**Acceptance Criteria:**

**Given** the Epic 1 dashboard shell is complete with stat cards rendered
**When** an investor taps any module stat card
**Then** the card executes a 3D CSS flip: `perspective(1000px) rotateY(180deg)`, 400ms `cubic-bezier(0.4, 0, 0.2, 1)`, `backface-visibility: hidden` on both faces
**And** the touch feedback (`scale(0.98)`) fires within <50ms before the flip animation begins

**Given** a card has flipped
**When** the card back face is visible
**Then** it displays the module's `var(--bg-card)` background with `border-top: 3px solid var(--module-color)`
**And** 3–5 KPI rows render, each with: Hebrew label (Varela Round 11px), JetBrains Mono Bold value, and a colored trend arrow (↑ green / ↓ red)
**And** a "למודול המלא" CTA button renders at the bottom with module-color background, minimum 44px height

**Given** KPI data is displayed
**When** a developer inspects the card back content
**Then** OptiTrack back shows: 347 total items, 64% approved, 12 objections, 35 pending — sourced from `OPTITRACK_DATA`
**And** OptiBiz back shows: ₪1.22B utilized, 68% budget, Q1 variance — sourced from `OPTIBIZ_DATA`
**And** data values on card backs match the values in their respective `*_DATA` constants (no hardcoded display values)

**Given** one card is already flipped
**When** an investor taps a different module card
**Then** the currently flipped card animates back to its front face (400ms) before the new card begins its flip
**And** `OptiPlan.state.activeModule` updates to the new module id
**And** this transition is handled by the named `handleStatCardTap` function with no anonymous callbacks

**Given** a card is flipped (showing back face)
**When** an investor taps anywhere outside all stat cards
**Then** the active card flips back to its front face via the named `handleOutsideTap` function
**And** `OptiPlan.state.currentLayer` returns to `overview`

**Given** a card flip animation is in progress
**When** an investor taps a different card mid-animation
**Then** the current card snaps to its animation destination immediately
**And** the new card begins its flip without queuing — no animation backlog accumulates

---

### Story 2.2: Module Expansion Overlay Container (Layer 3)

As an investor,
I want to tap "Full Module" on a card back and see the module open as a large cinematic overlay,
So that I discover there is deep, real data behind every module and feel the platform's full capability.

**Acceptance Criteria:**

**Given** a module card is showing its back face (Layer 2)
**When** an investor taps the "למודול המלא" CTA button
**Then** a module expansion overlay opens: 80% viewport width, 85% viewport height, centered fixed position, `border-radius: var(--radius)`, `background: var(--bg-card)`
**And** a semi-transparent backdrop (`rgba(0,0,0,0.6)`) covers the rest of the screen
**And** the opening animation is `scale(0.95)→scale(1)` + `opacity 0→1` over 500ms `cubic-bezier(0.16, 1, 0.3, 1)`

**Given** the overlay is open
**When** an investor views the module header
**Then** it shows a 3px top accent bar in `var(--module-color)`, the module's Tabler icon, and the Hebrew module name (e.g., "OptiTrack — תיאומים")
**And** a back button renders at top-right (RTL: `inset-inline-end`) with `ti-arrow-right` icon, 44×44px minimum touch target

**Given** the overlay is open
**When** an investor taps the back button
**Then** the named `handleModuleClose` function fires, triggering the reverse animation (500ms→350ms), backdrop fades, and `OptiPlan.state.transition('overview')` is called
**And** the sidebar active state returns to "לוח בקרה"

**Given** the overlay is open
**When** an investor taps the semi-transparent backdrop
**Then** the overlay closes identically to tapping the back button

**Given** one module overlay is open
**When** an investor taps a different module in the sidebar navigation
**Then** the current overlay closes (350ms) and the new module overlay opens (500ms) — cross-fade, not simultaneous display
**And** only one overlay exists in the DOM at any time

**Given** the overlay container is established
**When** `OptiPlan.components.moduleOverlay.expand(moduleId)` is called
**Then** the overlay container renders and calls `OptiPlan.modules[moduleId].render(data)` to populate module-specific content
**And** content within the overlay is independently scrollable via standard touch scroll

---

### Story 2.3: OptiTrack Module — Permit Matrix & Activity Feed

As an investor,
I want to see the full OptiTrack module with a coordination permit matrix and activity feed,
So that I understand how the platform manages complex multi-authority infrastructure approvals at scale.

**Acceptance Criteria:**

**Given** the module overlay container is open for OptiTrack
**When** `OptiPlan.modules.optitrack.render(OPTITRACK_DATA)` is called
**Then** a metrics summary row renders at the top: 347 total items, 64% approved, 12 objections, 35 pending — all values sourced from `OPTITRACK_DATA`

**Given** the OptiTrack overlay renders
**When** a developer inspects the permit matrix
**Then** it shows sections (A1–A6) as rows and regulatory authorities (עירייה, חברת חשמל, מקורות, בזק, רשות העתיקות) as columns
**And** each cell is color-coded: green = approved, amber = pending, red = rejected, gray = not submitted
**And** each cell also displays a text label or count — never color as the sole status indicator
**And** cells are built with SVG `<rect>` elements with `rx="var(--radius-sm)"`

**Given** the permit matrix is rendered
**When** the overlay content is scrolled
**Then** the matrix scrolls naturally within the overlay content area
**And** the back button and module header remain fixed (do not scroll with content)

**Given** the OptiTrack overlay renders
**When** a developer inspects the activity feed
**Then** 5+ feed items render, each with: colored Hebrew initial avatar, Hebrew name, relative timestamp in JetBrains Mono (e.g., "לפני 3 דק׳"), Hebrew description, and a status badge
**And** feed data is sourced from `OPTITRACK_DATA.activityFeed` — no hardcoded content in the component
**And** `OptiPlan.modules.optitrack` is namespaced with `init()`, `render(data)`, `destroy()` methods

---

### Story 2.4: OptiBiz Module — Cash Flow Chart & Financial KPIs

As an investor,
I want to see the full OptiBiz module with a 12-month cash flow chart and financial summary,
So that I can assess the project's financial health and understand the platform's financial planning capability.

**Acceptance Criteria:**

**Given** the module overlay is open for OptiBiz
**When** `OptiPlan.modules.optibiz.render(OPTIBIZ_DATA)` is called
**Then** a financial KPI row renders: ₪1.22B utilized of ₪1.8B total (68%), burn rate, and variance indicator — all sourced from `OPTIBIZ_DATA`
**And** the ₪1.8B total cross-references `PROJECT_DATA.totalBudget` (same constant)

**Given** the OptiBiz overlay renders
**When** a developer inspects the cash flow chart
**Then** a 12-month grouped bar chart renders with plan bars (`var(--optibiz)` at 40% opacity) and actual bars (solid `var(--optibiz)`)
**And** Hebrew month abbreviations render on the x-axis
**And** the y-axis auto-scales to the data range with NIS-formatted labels via `OptiPlan.utils.formatCurrency()`
**And** grid lines use `stroke: var(--grid-line)` at the defined opacity

**Given** the cash flow chart SVG is generated
**When** a developer inspects the implementation
**Then** bars are `<rect>` elements, the running total trend is a `<polyline>`, and the SVG has a `viewBox` for responsive scaling
**And** no canvas elements, no chart libraries, and no external chart dependencies are used
**And** `OptiPlan.modules.optibiz` follows the `init()`, `render(data)`, `destroy()` pattern

---

### Story 2.5: OptiRisk Module — Risk Heat Map & Top Risks Table

As an investor,
I want to see the full OptiRisk module with a risk severity heat map and top risks table,
So that I can assess the project's risk exposure and the platform's risk management maturity.

**Acceptance Criteria:**

**Given** the module overlay is open for OptiRisk
**When** `OptiPlan.modules.optirisk.render(OPTIRISK_DATA)` is called
**Then** a 5×5 risk heat map renders with probability as rows (Hebrew labels: נמוכה → גבוהה) and impact as columns (Hebrew labels: נמוכה → קריטי)
**And** cell colors transition from green (low probability/low impact) through yellow and orange to red (high probability/high impact)
**And** each cell displays the count of risks in that severity zone

**Given** the heat map is rendered
**When** a developer inspects the implementation
**Then** cells are SVG `<rect>` elements with `rx="var(--radius-sm)"`
**And** a color-scale legend renders alongside the heat map mapping color bands to severity labels
**And** color is always paired with a numeric label — never color alone as the sole indicator

**Given** the OptiRisk overlay renders
**When** a developer inspects the top risks table
**Then** 5+ risk entries render with: Hebrew risk name, severity badge (colored + text), trend arrow (↑/↓ or →), and mitigation status
**And** the total count (23 active, 8 critical) matches `OPTIRISK_DATA` and the stat card value from Epic 1
**And** `OptiPlan.modules.optirisk` follows the `init()`, `render(data)`, `destroy()` pattern

---

### Story 2.6: OptiDocs Module — Document Tracking Table

As an investor,
I want to see the full OptiDocs module with a document tracking table and completion summary,
So that I understand the platform's document management and approval workflow at project scale.

**Acceptance Criteria:**

**Given** the module overlay is open for OptiDocs
**When** `OptiPlan.modules.optidocs.render(OPTIDOCS_DATA)` is called
**Then** a metrics summary renders: 847 total documents, 94% completion rate, pending count, overdue count — all sourced from `OPTIDOCS_DATA`
**And** the 847 total and 94% figures match the values displayed on the OptiDocs stat card from Epic 1

**Given** the OptiDocs overlay renders
**When** a developer inspects the document table
**Then** rows contain: Hebrew document name, document type, status badge (pill: green=אושר, amber=ממתין, red=נדחה), date in DD/MM/YY format (JetBrains Mono), and assigned person initials
**And** alternating rows use `background: var(--bar-track)` and row hover uses `background: var(--feed-hover)`
**And** status badges always combine color with Hebrew text — never color alone

**Given** the document table has more rows than fit in the overlay
**When** an investor scrolls within the overlay
**Then** the table scrolls naturally; column headers remain visible (sticky or fixed within the scroll container)
**And** `OptiPlan.modules.optidocs` follows the `init()`, `render(data)`, `destroy()` pattern

---

### Story 2.7: OptiGantt Module — Schedule Timeline

As an investor,
I want to see the full OptiGantt module with a Gantt timeline showing all 6 project sections,
So that I can visualize the project schedule and understand current progress against the original plan.

**Acceptance Criteria:**

**Given** the module overlay is open for OptiGantt
**When** `OptiPlan.modules.optigantt.render(OPTIGANTT_DATA)` is called
**Then** a horizontal Gantt timeline renders with 6 rows (one per section A1–A6, Hebrew labels on y-axis) and monthly columns on the x-axis

**Given** the Gantt timeline renders
**When** a developer inspects the bars
**Then** planned duration bars render in `var(--optigantt)` at 40% opacity
**And** actual progress bars render in `var(--optigantt)` at full opacity, sized to the actual completion date or current progress
**And** diamond milestone markers (`<polygon>`) render at key project dates from `OPTIGANTT_DATA.milestones`

**Given** the Gantt timeline renders
**When** a developer inspects the today indicator
**Then** a vertical dashed line (`<path>` with `stroke-dasharray`) marks today's date position on the timeline
**And** the today position is calculated from `new Date()` at render time — not hardcoded

**Given** the timeline is rendered
**When** a developer inspects the SVG implementation
**Then** bars use `<rect>` elements, the today line uses `<path>`, text labels use `<text>`, and the SVG has a `viewBox` for responsive scaling
**And** no canvas, no chart libraries, no external dependencies
**And** `OptiPlan.modules.optigantt` follows the `init()`, `render(data)`, `destroy()` pattern

---

## Epic 3: AI Intelligence

Investors can access the AI chat panel, select from suggested Hebrew question chips or type freely, and receive project-specific Hebrew-language responses from a live LLM API. If the API is unavailable, the system silently serves cached responses — the investor never sees an error state or loading failure.

### Story 3.1: AI Chat Panel UI & Interaction Shell

As an investor,
I want to see an AI chat panel with Hebrew question chips and a text input,
So that engaging with the platform's intelligence feels natural and requires zero effort to start.

**Acceptance Criteria:**

**Given** the Epic 1 dashboard shell is complete
**When** an investor accesses the AI chat (via "Opium AI" sidebar nav item or AI indicator)
**Then** the AI chat panel renders in RTL layout with: 3–4 suggested question chips in Hebrew (tappable pills, min 44px height), a Hebrew free-text input field, and a send button (min 44×44px touch target)

**Given** the chat panel is open
**When** a developer inspects the AI agent indicator
**Then** it shows a pulsing dot (`@keyframes pulse`, 2s ease-in-out infinite) and Hebrew text "3 סוכנים פעילים"
**And** the indicator is the same one established in Story 1.6, now also accessible as a tap target to open the AI panel

**Given** the chat panel is open and an investor has tapped a suggested question chip
**When** the question is submitted
**Then** the user's question appears as a right-aligned message bubble in the chat thread (Hebrew, Varela Round)
**And** a typing indicator renders: 3 CSS-animated bouncing dots (`@keyframes bounce`, staggered delays) below the user bubble
**And** the suggested chips remain visible for the next question after a response is received

**Given** the chat panel receives a response
**When** the response text is available
**Then** it appears as a left-aligned AI response bubble with JetBrains Mono for any numeric data citations
**And** the typing indicator disappears before the response renders
**And** the chat scrolls to show the latest message

**Given** the chat history grows
**When** the number of stored message pairs exceeds `MAX_CHAT_HISTORY` (5)
**Then** the oldest pair is removed from `OptiPlan.ai.history` before adding the new pair
**And** the chat DOM display shows the most recent messages — no unbounded array growth

**Given** the chat panel is fully rendered
**When** a developer inspects `OptiPlan.ai`
**Then** it exposes `sendMessage(text)`, `displayResponse(text)`, `findCachedResponse(text)`, and `showTypingIndicator(bool)` methods
**And** all event handlers are named functions — no anonymous callbacks

---

### Story 3.2: LLM API Integration & Intelligent Hebrew Responses

As an investor,
I want to ask questions about the project and receive substantive, project-specific Hebrew answers from a live AI,
So that the platform feels genuinely intelligent and the demo's credibility reaches its peak moment.

**Acceptance Criteria:**

**Given** the AI chat panel UI from Story 3.1 is complete
**When** `OptiPlan.ai.sendMessage(text)` is called
**Then** it sends a `POST` request to the configured LLM REST endpoint with a JSON body containing: the user's message, conversation history (up to `MAX_CHAT_HISTORY`), and a Hebrew system prompt that provides project context (project name, budget, section count, key metrics)
**And** the `Authorization: Bearer ${API_KEY}` header is included
**And** an `AbortController` with an 8-second timeout is attached to the fetch

**Given** the LLM API returns a successful response
**When** the response JSON is parsed
**Then** the Hebrew response text is extracted and passed to `OptiPlan.ai.displayResponse(text)`
**And** the response references at least one specific project data point (e.g., section name, budget figure, or authority name) — validated by system prompt construction
**And** the total time from question submission to first visible response is under 3 seconds on a reasonable connection

**Given** an investor types or taps an adversarial or unexpected question
**When** the LLM receives it
**Then** the system prompt guides the model to respond in Hebrew using available project context — never "I don't know" or an empty response
**And** the response is coherent and project-relevant even if the specific question has no direct answer in the data

**Given** the LLM API request fails for any reason (timeout, network error, CORS error, quota exceeded)
**When** the `catch` block executes
**Then** `console.warn('[OptiPlan AI] ', err.message)` is called — the only error output
**And** `OptiPlan.ai.findCachedResponse(text)` is called, fuzzy-matching the user's question against `AI_CACHE` keys
**And** the best-matching cached Hebrew response is passed to `displayResponse()` — the investor sees a real answer, not an error

**Given** no `AI_CACHE` entry matches the user's question
**When** `findCachedResponse()` finds no adequate match
**Then** a pre-written generic Hebrew fallback string is displayed referencing the project by name (e.g., "על בסיס נתוני פרויקט הרחבת קו המטרו...")
**And** the UI shows no error state, no spinner, no "API unavailable" message — ever

**Given** the API is confirmed working
**When** a developer validates CORS configuration
**Then** the fetch succeeds from a `*.github.io` origin with no CORS errors in the Safari console
**And** the `API_KEY` is stored as `const API_KEY = '...'` at the top of the data constants section — never in a config file, never in localStorage

---

## Epic 4: Conference Readiness & Polish

The dashboard is hardened for 3–4 hours of unattended conference use. Data values are cross-verified for consistency across all modules, contextual tooltips add silent sales narrative to key elements, idle auto-reset returns the dashboard to its magnetic state after 90 seconds, and all touch, performance, and stability targets are met for iPad Safari.

### Story 4.1: Contextual Tooltips with Hebrew Sales Copy

As an investor,
I want to see brief Hebrew explanatory tooltips on key dashboard elements when I tap them,
So that I understand the significance of each metric without needing anyone to narrate it to me.

**Acceptance Criteria:**

**Given** the Epic 1 dashboard shell is complete
**When** an investor taps a hero gauge, the AI agent indicator, or a designated stat card element
**Then** a tooltip popover appears with Hebrew sales-pitch copy relevant to that element (e.g., for the OptiTrack gauge: "תיאום עם 12 רשויות ממשלתיות — בפלטפורמה אחת")
**And** the tooltip renders with: `background: var(--bg-card)`, `border: 1px solid var(--border)`, `border-radius: var(--radius-sm)`, `box-shadow: var(--shadow-card-hover)`, max-width 280px, and a CSS triangle arrow pointing to the target element

**Given** a tooltip is visible
**When** an investor taps anywhere outside the tooltip or its target element
**Then** the tooltip fades out (150ms opacity transition) and is removed from view
**And** only one tooltip is visible at any time — tapping a new target dismisses any open tooltip before showing the new one

**Given** tooltips are implemented
**When** a developer counts the tooltip instances
**Then** up to 9 tooltips exist: 3 hero gauges, 1 AI indicator, and up to 5 distributed across stat card elements
**And** every tooltip copy is in Hebrew and communicates value without sounding like a sales pitch (the "screaming the pain without saying it" approach)

**Given** a tooltip trigger is tapped
**When** the tooltip auto-positions
**Then** it appears above the target element by default, falling back to below if the top position would overflow the viewport
**And** the tooltip never overflows the viewport horizontally

**Given** tooltip interactions are implemented
**When** a developer inspects the event handling
**Then** all tooltip triggers use `touchstart` events — no hover-only behavior
**And** `-webkit-touch-callout: none` is applied to all tooltip trigger elements to prevent iOS long-press popups
**And** all event handlers are named functions: `handleTooltipShow`, `handleTooltipDismiss`

---

### Story 4.2: Cross-Module Data Consistency Audit

As an investor (playing the Skeptic),
I want every number visible across the entire dashboard to cross-reference correctly,
So that I cannot find a single inconsistency that reveals this is a demo rather than the real product.

**Acceptance Criteria:**

**Given** all 5 module expansions are complete (Epics 1–3 done)
**When** a developer performs a full data consistency audit
**Then** `OPTIBIZ_DATA.utilized` (1,220,000,000) ÷ `PROJECT_DATA.totalBudget` (1,800,000,000) = 67.8%, displayed as 68% on both the stat card and the OptiBiz KPI row

**Given** the data audit is in progress
**When** OptiTrack values are cross-checked
**Then** `OPTITRACK_DATA.totalItems` (347) equals the value displayed on the OptiTrack stat card front face
**And** `OPTITRACK_DATA.approved` count when divided by `totalItems` equals the 64% approval rate shown on the card back and in the module expansion

**Given** the data audit is in progress
**When** OptiRisk values are cross-checked
**Then** `OPTIRISK_DATA.activeRisks` (23) and `OPTIRISK_DATA.critical` (8) match the OptiRisk stat card values exactly
**And** the sum of all risk counts in the heat map grid equals `OPTIRISK_DATA.activeRisks`

**Given** the data audit is in progress
**When** OptiDocs values are cross-checked
**Then** `OPTIDOCS_DATA.total` (847) and `OPTIDOCS_DATA.completionRate` (94%) match the OptiDocs stat card values
**And** `OPTIDOCS_DATA.approved` count ÷ `OPTIDOCS_DATA.total` equals 94% (within rounding)

**Given** the data audit is in progress
**When** OptiBiz cash flow is cross-checked
**Then** the sum of all actual monthly values in `OPTIBIZ_DATA.cashFlow` array equals `OPTIBIZ_DATA.utilized` (1,220,000,000) within reasonable rounding
**And** the 12-month plan total in the array reflects a realistic planned spend trajectory toward `PROJECT_DATA.totalBudget`

**Given** all cross-references are verified
**When** any discrepancy is found between a data constant and its display value
**Then** the data constant is corrected to be the single source of truth — display values are always derived from constants, never independently hardcoded
**And** cross-reference relationships are documented in adjacent code comments (e.g., `// Consistent with PROJECT_DATA.totalBudget`)

---

### Story 4.3: iPad Safari Hardening & Touch Optimization

As an investor at a conference using an iPad,
I want every interaction to respond instantly with no platform-specific quirks or jank,
So that the experience feels like precision-engineered native software, not a web app.

**Acceptance Criteria:**

**Given** the complete dashboard is built
**When** a developer reviews the HTML `<head>`
**Then** the viewport meta tag reads: `<meta name="viewport" content="width=1024, user-scalable=no">` — preventing pinch-zoom
**And** `<meta name="robots" content="noindex">` is present to prevent accidental search indexing

**Given** all interactive elements are implemented
**When** a developer inspects their CSS
**Then** `touch-action: manipulation` is applied to all tappable elements — bypassing the 300ms click delay on iOS Safari
**And** `-webkit-touch-callout: none` is applied to all interactive elements — preventing the iOS long-press context menu
**And** `user-select: none` is applied to all interactive elements — preventing text selection during rapid taps

**Given** all 3D animated elements are implemented
**When** a developer inspects their CSS
**Then** `-webkit-transform`, `-webkit-backface-visibility`, and `-webkit-perspective` prefixes are applied alongside unprefixed versions on all 3D animation elements
**And** `will-change: transform` is declared on elements during their active animation phases

**Given** the full page is loaded on iPad Safari
**When** an investor taps any interactive element
**Then** visual feedback (scale, color change, or animation start) occurs within 100ms of the touch event — verified by Safari Web Inspector's timeline

**Given** the `prefers-reduced-motion` media query is implemented
**When** the system accessibility setting "Reduce Motion" is enabled
**Then** all non-essential animations are disabled: entrance animations, card flip transitions, module expand transitions, ambient gradient
**And** the CSS rule targets `*, *::before, *::after` with `animation-duration: 0.01ms !important` and `transition-duration: 0.01ms !important`

**Given** the defensive responsive breakpoints are implemented
**When** an iPad is rotated to portrait orientation
**Then** a full-screen rotation prompt overlay appears with Hebrew text and a rotation icon — the dashboard is hidden
**And** when the device returns to landscape, the prompt disappears and the dashboard is visible
**And** when the viewport is below 768px (phone), a full-screen Hebrew message displays: "הדשבורד מותאם למסך iPad לרוחב"

---

### Story 4.4: Long-Running Stability & Session Management

As an operator running the conference demo for 3–4 hours unattended,
I want the dashboard to perform identically at hour 4 as it did at load, with each investor session starting clean,
So that I never need to touch or refresh the iPad during the conference.

**Acceptance Criteria:**

**Given** the complete dashboard has been running for 30+ simulated investor sessions
**When** a developer inspects the JS heap in Safari Web Inspector
**Then** no measurable memory growth has occurred — the heap size after 30 sessions is within 5% of the heap size after the first session

**Given** all components implement the `destroy()` method
**When** `OptiPlan.components.moduleOverlay.close()` is called
**Then** `OptiPlan.modules[moduleId].destroy()` is called, which removes all event listeners and clears the module's DOM content
**And** `removeEventListener` is called with the same named function reference that was passed to `addEventListener`

**Given** the idle auto-reset fires after 90 seconds
**When** a new investor approaches and begins interacting
**Then** the dashboard state is `overview`, all cards show front faces, no overlay is open, and the ambient animations are running — a completely clean starting state for each investor
**And** no `localStorage`, `sessionStorage`, or cookie state persists between investor sessions

**Given** the `setInterval` for the clock is running
**When** the page has been open for 4 hours
**Then** only one `setInterval` reference exists in `OptiPlan.state._clockInterval`
**And** the clock displays the correct current time

**Given** an investor presses the hardware back button on the iPad
**When** `popstate` event fires
**Then** the `handlePopState` named function reverses the current layer transition (e.g., `module-expand` → `overview`)
**And** no stuck states or blank screens occur — the dashboard always reaches a valid, interactive state

**Given** the network is disconnected during a session
**When** an investor interacts with any non-AI feature (cards, modules, gauges, navigation)
**Then** all interactions work identically to the connected state — zero dependency on network for any non-AI feature
**And** the only degraded experience is the AI chat, which silently uses `AI_CACHE` responses

**Given** the full test suite checklist is run on real iPad hardware
**When** all 10 items are verified (load time, card renders, theme toggle, card flip, module expansion, back button, sidebar nav, AI chat, idle reset, 10-minute stability)
**Then** all 10 pass without requiring any page refresh or developer intervention

