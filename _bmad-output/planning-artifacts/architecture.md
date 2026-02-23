---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8]
inputDocuments:
  - _bmad-output/planning-artifacts/prd.md
  - _bmad-output/planning-artifacts/ux-design-specification.md
  - _bmad-output/planning-artifacts/design-system.html
  - _bmad-output/planning-artifacts/optiplan-v2.html
  - user-data/product-brief-v2.md
  - user-data/dashboard-build-document.md
workflowType: 'architecture'
lastStep: 8
status: 'complete'
completedAt: '2026-02-23'
project_name: 'optiplan-dashboard'
user_name: 'BenAkiva'
date: '2026-02-23'
---

# Architecture Decision Document

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together._

## Project Context Analysis

### Requirements Overview

**Functional Requirements:**
44 functional requirements across 8 categories:
- **Dashboard Overview (FR1-5):** Project header, 3 hero gauges, 5 module cards, AI agent indicator, idle-state ambient animation
- **Module Exploration (FR6-10):** Card flip to KPIs, full module expansion with charts/tables, back navigation, zero dead ends across all modules
- **AI-Powered Intelligence (FR11-16):** Chat interface, suggested questions, free-text Hebrew input, project-specific LLM responses, adversarial question handling, cached fallback
- **Data Visualization (FR17-27):** Per-module: permit matrix (OptiTrack), cash flow chart (OptiBiz), risk heat map (OptiRisk), document tracking (OptiDocs), Gantt timeline (OptiGantt)
- **Visual Experience (FR28-33):** Animated gradient background, gauge fill on load, staggered card entrance, card flip transitions, module expand transitions, pulsing AI indicator
- **Content & IA (FR34-39):** Full Hebrew RTL, OptiPlan branding, contextual tooltips, self-explanatory design, realistic fictitious data, cross-module data consistency
- **Resilience (FR40-44):** Single-URL load, long-running stability (3-4 hours), offline capability for non-AI features, graceful AI degradation, state recovery

**Non-Functional Requirements:**
- **Performance:** First paint <1s, fully interactive <2s, touch response <100ms, all animations 60fps, AI response <3s, zero memory growth over 4-hour session
- **Reliability:** 3-4 hours continuous runtime, zero JS errors, animation stability over extended runtime, offline resilience, AI graceful degradation to cached responses
- **Integration:** Single external dependency (LLM REST API), client-side API key, CORS from GitHub Pages, 5-8 pre-cached fallback Q&A pairs
- **Deployment:** GitHub Pages static hosting, single HTML file (or +1 CSS +1 JS max), zero build tools, Google Fonts + LLM API only external dependencies

**Scale & Complexity:**
- Primary domain: Frontend-only static SPA (vanilla HTML/CSS/JS)
- Complexity level: Low infrastructure / High UX complexity
- Estimated architectural components: ~15 custom UI components, 1 state management system, 1 animation orchestration layer, 1 AI integration module, 1 theme system

### Technical Constraints & Dependencies

1. **Zero build toolchain** — No compilation, bundling, transpilation. What is written is what ships.
2. **Single-file architecture** — Entire app in one HTML file (or HTML + 1 CSS + 1 JS). All CSS and JS either inline or in max 2 additional files.
3. **Vanilla only** — No React, Vue, Svelte, Tailwind, PostCSS, Sass, or any framework/library. Pure HTML/CSS/JS.
4. **iPad Safari primary** — Only browser target that matters. WebKit prefixes required for 3D transforms. Must bypass 300ms click delay. Real hardware testing required.
5. **GitHub Pages hosting** — Static files only. No server-side logic, no environment variables, no build steps in deployment.
6. **Google Fonts dependency** — Varela Round + JetBrains Mono loaded via CDN with `font-display: swap`.
7. **Tabler Icons dependency** — Icon webfont loaded via CDN.
8. **LLM API dependency** — REST API call to Gemini/Claude. API key embedded client-side. CORS must work from GitHub Pages origin.
9. **Design system locked** — Palette D + Skin 1 selected. CSS custom properties, color values, typography, spacing, and component patterns are defined in `design-system.html` and `optiplan-v2.html`.
10. **Hardcoded data** — All project data (Gush Dan Metro Extension, 6 sections, 1.8B NIS budget, 347 coordination items, etc.) embedded as JS objects. No API calls for data.

### Cross-Cutting Concerns Identified

1. **Animation Performance & Memory** — Every component uses animations. All must be GPU-composited (transform, opacity only). `will-change` declarations required. CSS animations preferred over JS `requestAnimationFrame` for long-running loops. No growing arrays, no DOM accumulation, no orphaned event listeners.

2. **Theme/Palette Cascade** — CSS custom properties cascade from `:root` to `[data-palette]` to `[data-skin][data-theme]` to components. Every visual value must reference tokens, never hardcoded. Theme toggle affects entire app via single `data-theme` attribute swap.

3. **RTL Layout Compliance** — `dir="rtl"` on `<html>`. Logical CSS properties (`margin-inline-start`, not `margin-left`). Flexbox row auto-reversal. Sidebar on right side. Back button top-right in overlays.

4. **Touch Interaction Model** — All interactive elements: 44x44px minimum. Touch feedback <50ms (scale 0.98). No hover-dependent functionality. Prevent pinch-zoom, pull-to-refresh, iOS context menus. Passive event listeners where possible.

5. **State Management** — Layer transitions (idle to L1 to L2 flip to L3 expansion), active module tracking, chat history, theme state, idle timer — managed via simple JS state object and CSS class toggling. No framework state management.

6. **AI Chat Resilience** — Single network dependency. Must detect connectivity failure silently. Serve cached Hebrew responses seamlessly. Never show error states, loading spinners, or "I don't understand" to the investor.

7. **iPad Safari Quirks** — Known issues with scroll behavior, fixed positioning, double-tap zoom, `-webkit-touch-callout`, CSS `color-mix()` support (Safari 16.2+). All require testing on real hardware.

8. **Data Consistency** — All hardcoded numbers must cross-reference across modules. Budget totals must match. Permit counts must be consistent. The Skeptic investor will check.

## Starter Template Evaluation

### Primary Technology Domain

Static Single-Page Application — Vanilla HTML/CSS/JS deployed on GitHub Pages. Zero build toolchain, zero dependencies beyond CDN fonts/icons and a single LLM REST API.

### Starter Options Considered

Standard starter templates and CLI generators (Next.js, Vite, Create React App, SvelteKit, etc.) were evaluated and **all eliminated** based on hard constraints:

1. **Zero build toolchain** — No compilation, bundling, transpilation, or Node.js in the pipeline
2. **Vanilla only** — No frameworks, no libraries, no npm packages
3. **Single-file architecture** — Max 3 files total (HTML + 1 CSS + 1 JS)
4. **GitHub Pages static hosting** — No server-side rendering, no API routes

No existing starter template matches these constraints. The project requires a hand-crafted HTML scaffold.

### Selected Starter: Hand-Crafted HTML Scaffold

**Rationale for Selection:**
The project's constraints explicitly forbid any build tooling or framework. The "starter" is a manually authored `index.html` file that establishes the foundational HTML structure, CSS custom property system, and JavaScript architecture. This is the only viable approach given the requirement that "what is written is what ships."

**Initialization Command:**
```bash
# No CLI command. Manual file creation.
touch index.html  # (or index.html + styles.css + app.js)
```

**Architectural Decisions Established by Scaffold:**

**Language & Runtime:**
- Vanilla JavaScript (ES2020+ features targeting Safari 16.2+ WebKit)
- HTML5 semantic elements
- CSS3 with custom properties, logical properties for RTL, WebKit-prefixed transforms
- No TypeScript, no JSX, no compilation step

**Styling Solution:**
- CSS custom properties cascade: `:root` → `[data-palette]` → `[data-skin][data-theme]` → components
- Design tokens from design-system.html (Palette D + Skin 1 locked)
- Dark theme (Indigo Night) and light theme (Cool Mist) via `data-theme` attribute
- All colors, spacing, radii, transitions reference CSS variables — zero hardcoded values
- Inline `<style>` block or single external stylesheet

**Build Tooling:**
- None. Zero build step. `git push` to `main` triggers GitHub Pages deployment.
- No minification, no tree-shaking, no source maps
- Development via VS Code Live Server extension for local preview

**Testing Framework:**
- No automated testing infrastructure (no Node.js environment)
- Manual testing on real iPad Safari hardware (primary target)
- Safari Web Inspector for debugging and performance profiling
- Chrome DevTools for secondary development convenience

**Code Organization:**
- Single HTML file (preferred) or HTML + 1 CSS + 1 JS (max 3 files)
- JavaScript organized via namespaced objects (e.g., `OptiPlan.state`, `OptiPlan.animations`, `OptiPlan.modules`)
- CSS organized by component sections within single `<style>` block
- No ES module imports (single-file constraint)

**Development Experience:**
- VS Code with Live Server extension
- Safari Web Inspector (primary debugging target)
- Manual browser refresh (no HMR)
- Git version control, GitHub Pages deployment
- No linting, no formatting tools (vanilla authoring)

**External Dependencies (CDN Only):**
- Google Fonts: `Varela Round` (UI) + `JetBrains Mono` (data) via `fonts.googleapis.com`
- Tabler Icons: Icon webfont via `cdn.jsdelivr.net`
- LLM API: Single REST endpoint (Gemini/Claude) with client-side API key

**Note:** The first implementation story should establish the complete HTML scaffold with CSS custom properties, structural layout, and base JavaScript architecture.

## Core Architectural Decisions

### Decision Priority Analysis

**Critical Decisions (Block Implementation):**
1. Frontend State Architecture — Centralized state object with event-driven CSS class toggling
2. Component Architecture — Namespace-based organization with pre-rendered HTML
3. Animation Architecture — CSS-first with JS orchestration, GPU-composited only
4. Data Visualization — Pure SVG inline generation for charts, HTML tables for tabular data
5. AI Chat Integration — Fetch with fallback cache pattern, fuzzy-matched cached responses
6. File Organization — Single HTML file with comment-delimited sections

**Important Decisions (Shape Architecture):**
7. Touch event handling — Passive listeners, 300ms delay bypass, 44x44px targets
8. Memory management — No growing arrays, capped chat history, cleared timer references
9. Theme switching — Single `data-theme` attribute swap, `localStorage` persistence

**Deferred Decisions (Post-MVP):**
- Multi-device responsive breakpoints (iPad landscape only for demo)
- Service worker for offline (overkill for demo — app loads from single file)
- Analytics/telemetry (not needed for demo)

### Data Architecture

**Decision:** No database. All project data hardcoded as JavaScript objects within `<script>` block.
- Project metadata: single `PROJECT_DATA` constant object
- Module-specific data: per-module data objects (`OPTITRACK_DATA`, `OPTIBIZ_DATA`, etc.)
- Cached AI responses: `AI_CACHE` object with question-answer pairs in Hebrew
- Data consistency enforced by cross-referencing values between module objects (e.g., budget totals match)
- **Rationale:** Zero infrastructure, instant load, no network dependency for data. All numbers are fictitious but internally consistent.

### Authentication & Security

**Decision:** None required.
- Public investor demo — no user accounts, no sessions, no tokens
- LLM API key embedded client-side (accepted trade-off per PRD — demo use only, key is disposable)
- No sensitive data in the application
- **Rationale:** Adding auth to a demo dashboard adds complexity with zero value.

### API & Communication Patterns

**Decision:** Single outbound REST call to LLM API.
- Method: `POST` with JSON body containing conversation history
- Headers: `Content-Type: application/json`, `Authorization: Bearer ${API_KEY}`
- Timeout: 8 seconds via `AbortController`
- Error handling: Silent fallback to cached responses — never expose errors to UI
- CORS: Must work from `*.github.io` origin
- No other API calls in the entire application
- **Rationale:** Minimal network surface. Single point of failure is the AI chat, which has full graceful degradation.

### Frontend Architecture

**State Management:**
- Centralized `OptiPlan.state` object as single source of truth
- State machine for layer transitions: `idle` → `overview` → `card-flip` → `module-expand`
- State changes trigger CSS class toggles (`document.body.classList` / element `classList`)
- No reactive binding, no observers, no pub/sub — direct imperative updates
- Idle timer: single `setTimeout` reference, reset on any touch/click event

**Component Architecture:**
- ~15 components organized under `OptiPlan.components` namespace
- Each component: `init()` for setup, `render(data)` for updates, `destroy()` for cleanup
- HTML pre-rendered in markup, JS enhances with data and interactivity
- Dynamic content via template literals + `innerHTML` (sanitized where user input involved — AI chat)
- CSS scoping via `.component-name` class prefixes (no BEM, no CSS modules)

**Routing Strategy:**
- No router. Single page, layer-based navigation via state machine.
- "Back" navigation reverses layer state (L3 → L1, L2 → L1)
- URL never changes — `index.html` is the only route
- Browser back button: `history.pushState` for layer changes, `popstate` listener for back navigation

**Animation System:**
- CSS `@keyframes` for infinite loops (gradient, pulse, ambient)
- CSS `transition` for state changes (flip: 400ms, expand: 500ms, gauge: 800ms)
- JS orchestration for sequenced entrances (staggered card delays via `setTimeout` chains)
- All animations use `transform` and `opacity` exclusively — GPU-composited
- `will-change: transform` on animated elements during active animations
- Cubic-bezier easing: `cubic-bezier(0.16, 1, 0.3, 1)` as default spring curve
- Performance budget: ≤6 simultaneous CSS animations in idle state

**Data Visualization:**
- Pure SVG generation via JS template literals
- Ring/Gauge: `<circle>` with `stroke-dasharray` / `stroke-dashoffset`
- Line/Area charts: `<polyline>` or `<path>` with computed coordinates
- Bar charts / Heat maps: `<rect>` elements
- Gantt timeline: Horizontal `<rect>` bars on time axis
- Tables (OptiDocs, OptiTrack permit matrix): HTML `<table>` elements
- SVG `viewBox` for responsive scaling within containers
- No Canvas, no D3.js, no charting libraries

### Infrastructure & Deployment

**Decision:** GitHub Pages static hosting with zero CI/CD.
- Single `index.html` file in repository root (or `/docs` folder)
- Deployment: `git push origin main` → GitHub Pages auto-deploys
- No build step, no GitHub Actions, no environment variables
- Custom domain: optional (not required for demo)
- CDN dependencies: Google Fonts + Tabler Icons (both cached by browser)
- **Rationale:** Simplest possible deployment. Zero DevOps. File is the deployment artifact.

### Decision Impact Analysis

**Implementation Sequence:**
1. HTML scaffold with CSS custom properties and structural layout
2. State management system and layer state machine
3. Sidebar navigation and topbar components
4. Dashboard overview with hero gauges and stat cards
5. Card flip interaction (L2)
6. Module expansion overlays (L3) — one module at a time
7. Per-module data visualizations (SVG charts, tables)
8. AI chat panel with LLM integration and cache fallback
9. Animation polish (staggered entrances, ambient effects, idle state)
10. Touch optimization and iPad Safari hardening

**Cross-Component Dependencies:**
- State machine must exist before any component can register transitions
- CSS custom properties must be defined before any component renders
- Animation system must be established before components add their animations
- Data objects must be defined before module visualizations render
- AI cache must be populated before chat panel can degrade gracefully

## Implementation Patterns & Consistency Rules

### Pattern Categories Defined

**Critical Conflict Points Identified:**
12 areas where AI agents could make incompatible choices in a vanilla HTML/CSS/JS single-file project.

### Naming Patterns

**CSS Custom Property Naming:**
```css
/* Module colors: --{module-name} */
--optitrack: #0991F3;
--optibiz: #F6AE2D;

/* Component tokens: --{property}-{element}-{variant} */
--bg-card: #141833;
--bg-card-hover: #1A1F40;
--text-primary: #EAEDF3;
--text-secondary: #8B92A5;

/* Spacing/sizing: --{property}-{size} */
--radius: 14px;
--radius-sm: 8px;
--gap: 16px;
--gap-sm: 8px;

/* Transitions: --transition-{type} */
--transition-default: 0.35s cubic-bezier(0.16, 1, 0.3, 1);
--transition-flip: 0.4s cubic-bezier(0.16, 1, 0.3, 1);
```
**Rule:** All CSS values MUST reference custom properties. Zero hardcoded color values, zero hardcoded radii. If a token doesn't exist, add it to the `:root` block — never inline a raw value.

**CSS Class Naming:**
```css
/* Components: .{component-name} */
.sidebar { }
.topbar { }
.stat-card { }
.module-overlay { }

/* Component children: .{component-name}__{child} */
.stat-card__title { }
.stat-card__value { }
.stat-card__icon { }

/* State modifiers: .{component-name}--{state} */
.stat-card--flipped { }
.stat-card--active { }
.module-overlay--expanded { }

/* Utility classes: .u-{utility} */
.u-hidden { }
.u-rtl-flip { }
```
**Rule:** BEM-like with double-underscore for children, double-dash for modifiers. kebab-case throughout. No camelCase in CSS classes.

**JavaScript Naming:**
```javascript
// Namespaces: PascalCase
OptiPlan.state
OptiPlan.components
OptiPlan.animations
OptiPlan.modules
OptiPlan.ai
OptiPlan.utils
OptiPlan.data

// Functions: camelCase
OptiPlan.components.sidebar.init()
OptiPlan.utils.formatCurrency(amount)
OptiPlan.ai.sendMessage(text)

// Constants: UPPER_SNAKE_CASE
const API_KEY = '...';
const IDLE_TIMEOUT_MS = 90000;
const MAX_CHAT_HISTORY = 5;
const PROJECT_DATA = { ... };

// DOM references: camelCase with $ prefix
const $sidebar = document.querySelector('.sidebar');
const $chatInput = document.querySelector('.chat-panel__input');

// Private/internal: _camelCase prefix
function _resetIdleTimer() { }
function _buildSvgRing(data) { }
```
**Rule:** `$` prefix for DOM element references makes them instantly identifiable. `_` prefix for internal functions not meant to be called from outside their namespace.

**HTML Attributes:**
```html
<!-- IDs: kebab-case, component-scoped -->
<div id="module-optitrack">
<div id="gauge-budget">

<!-- Data attributes: kebab-case -->
<div data-module="optitrack" data-layer="l2">
<div data-theme="dark" data-palette="d" data-skin="1">
```
**Rule:** No IDs for styling — classes only. IDs only for JS targeting.

### Structure Patterns

**Single File Section Organization:**

HTML sections ordered: Meta & CDN → Sidebar → Topbar → Main Content Grid → Module Overlays → AI Chat Panel

CSS sections ordered: Reset & Base → Custom Properties (Design Tokens) → Layout (Sidebar, Topbar, Grid) → Components (one section per component) → Animations & Keyframes → Utilities

JS sections ordered: Data Constants → State Management → Utility Functions → Components (one section per component) → Modules (one section per module) → AI Chat → Animation Orchestration → Initialization

**Rule:** Every section delimited by `// ================================================================` comment blocks with `SECTION: {Name}` label. Agents working on a section MUST preserve delimiter comments. New components get their own delimited section.

### Format Patterns

**Data Object Formats:**
```javascript
// All data keys: camelCase
const PROJECT_DATA = {
  projectName: 'הרחבת קו מטרו, גוש דן',
  client: 'נת"ע',
  totalBudget: 1800000000,
  currency: 'NIS',
  sections: [
    { id: 'a1', name: 'קטע א1', phase: 'execution', progress: 78 }
  ]
};
```
**Rule:** All data keys camelCase. Hebrew strings for display values only. Numbers as numbers, not strings. Status/enum values as English lowercase strings (`'approved'`, `'pending'`, `'blocked'`). Hebrew only in display-facing string values, never in logic or enum values.

**Currency/Number Formatting:**
```javascript
OptiPlan.utils.formatCurrency = (amount) => {
  return new Intl.NumberFormat('he-IL').format(amount);
};
OptiPlan.utils.formatPercent = (value) => `${value}%`;
```
**Rule:** Raw numbers in data objects, formatted strings only at render time. Never store pre-formatted strings in data.

### Communication Patterns

**State Update Pattern:**
```javascript
// ALWAYS update state THEN update DOM — never the reverse
OptiPlan.state.set = function(key, value) {
  this[key] = value;
  OptiPlan.ui.reflect(key, value);
};

// Layer transitions MUST use state machine
OptiPlan.state.transition = function(targetLayer) {
  const allowed = {
    'idle':          ['overview'],
    'overview':      ['card-flip', 'idle'],
    'card-flip':     ['overview', 'module-expand'],
    'module-expand': ['overview']
  };
  if (!allowed[this.currentLayer]?.includes(targetLayer)) return false;
  this.currentLayer = targetLayer;
  OptiPlan.ui.reflectLayer(targetLayer);
  return true;
};
```
**Rule:** State changes are the single source of truth. DOM updates are reactions to state changes, never the cause.

**Event Listener Pattern:**
```javascript
// Named functions for all event handlers (enables cleanup)
function handleCardTap(e) {
  e.preventDefault();
  const moduleId = e.currentTarget.dataset.module;
  OptiPlan.state.transition('card-flip');
  OptiPlan.state.set('activeModule', moduleId);
}

// Registration with passive option where possible
$card.addEventListener('touchstart', handleCardTap, { passive: false });

// Cleanup
$card.removeEventListener('touchstart', handleCardTap);
```
**Rule:** Named functions for all event handlers. No anonymous arrow functions in `addEventListener`. This enables cleanup and prevents memory leaks.

### Process Patterns

**Error Handling:**
```javascript
// The investor NEVER sees an error
try {
  const response = await OptiPlan.ai.sendMessage(userText);
  OptiPlan.ai.displayResponse(response);
} catch (err) {
  console.warn('[OptiPlan AI]', err.message);
  const cached = OptiPlan.ai.findCachedResponse(userText);
  OptiPlan.ai.displayResponse(cached);
}
```
**Rule:** `console.warn` for caught errors, `console.log` for state changes (dev). Never `alert()`, never `console.error` (shows red in inspector). Never show errors in UI. Console format: `[OptiPlan {Subsystem}] message`.

**Loading States:**
- ONLY the AI chat has a loading state (typing indicator with CSS-animated bouncing dots)
- All other content renders instantly from hardcoded data
- No loading spinners, no skeleton screens, no progress bars for local data
- **Rule:** If data is local, render immediately. Only network-dependent features get loading states.

### Enforcement Guidelines

**All AI Agents MUST:**

1. Reference CSS custom properties for ALL visual values — zero hardcoded colors, radii, or transitions
2. Use the `OptiPlan.*` namespace — no global functions, no standalone variables
3. Use `$` prefix for DOM element references
4. Animate ONLY `transform` and `opacity` — never width, height, top, left, margin, padding
5. Use section delimiter comments (`// ===...===`) when adding new code sections
6. Use named functions for event listeners — never anonymous callbacks
7. Update state first, DOM second — never mutate DOM then sync state
8. Keep Hebrew strings in data constants — never inline Hebrew in logic functions
9. Use `console.warn` for errors, never expose errors in UI
10. Return early from invalid state transitions via the state machine

### Pattern Examples

**Good Example — Adding a new chart component:**
```javascript
// ================================================================
// SECTION: Component — Risk Heat Map
// ================================================================

OptiPlan.components.riskHeatMap = {
  init() {
    this.$container = document.querySelector('#module-optirisk .chart-area');
  },
  render(data) {
    const cells = data.risks.map(r =>
      `<rect x="${r.col * 60}" y="${r.row * 40}" width="56" height="36"
             rx="var(--radius-sm)" fill="var(--risk-${r.level})"
             opacity="0" class="risk-cell">
        <animate attributeName="opacity" to="1" dur="0.3s"
                 begin="${r.col * 0.05}s" fill="freeze"/>
      </rect>`
    ).join('');
    this.$container.innerHTML =
      `<svg viewBox="0 0 360 200" class="risk-heatmap">${cells}</svg>`;
  },
  destroy() {
    this.$container.innerHTML = '';
  }
};
```

**Anti-Patterns — What to AVOID:**
```javascript
// ❌ Global function, no namespace
function showRiskMap() { }

// ❌ Hardcoded color
element.style.background = '#FF7A3C';

// ❌ Anonymous event listener (can't remove)
el.addEventListener('click', () => { doStuff(); });

// ❌ Animating layout property
.card { transition: width 0.3s; }

// ❌ DOM-first, no state
document.body.classList.add('expanded');

// ❌ Inline Hebrew in logic
if (status === 'מאושר') { }

// ❌ Error shown to user
alert('API connection failed');
```

## Project Structure & Boundaries

### Complete Project Directory Structure

```
optiplan-dashboard/
├── index.html                          # THE APPLICATION (single file)
├── README.md                           # Project documentation
├── .gitignore                          # Git ignore rules
├── _bmad/                              # BMAD workflow system
│   └── ...
├── _bmad-output/                       # Planning artifacts
│   └── planning-artifacts/
│       ├── architecture.md             # This document
│       ├── prd.md                      # Product requirements
│       ├── ux-design-specification.md  # UX specification
│       ├── design-system.html          # Design system reference
│       └── optiplan-v2.html            # Interactive mockup reference
└── user-data/                          # Source documents
    ├── product-brief-v2.md
    └── dashboard-build-document.md
```

The entire deployable application is `index.html`. Everything else is documentation and planning artifacts.

### Internal Structure of index.html

The internal organization within the single file that AI agents must navigate:

**HTML Body Structure:**
```
<body>
  Sidebar Navigation        — .sidebar with module nav, theme toggle, AI indicator
  Topbar                    — .topbar with project name, breadcrumb, status badges
  Main Content Area         — .main-content containing:
    Hero Gauges             — .hero-gauges (3 ring gauges)
    Stat Cards Row          — .stat-cards-row (5 module cards, front + back)
    Dashboard Grid          — .dashboard-grid (summary charts, activity feed)
  Module Overlays (L3)      — One per module:
    #module-optitrack       — Permit matrix + charts
    #module-optibiz         — Cash flow + financial charts
    #module-optirisk        — Risk heat map + risk table
    #module-optidocs        — Document tracking table
    #module-optigantt       — Gantt timeline
  AI Chat Panel             — .chat-panel with messages, input, suggestions
</body>
```

**CSS `<style>` Block Order:**
1. CSS Reset & Base
2. Design Tokens (Custom Properties) — `:root`, `[data-palette]`, `[data-skin][data-theme]`
3. Layout — Sidebar
4. Layout — Topbar
5. Layout — Main Grid
6. Component — Hero Gauge
7. Component — Stat Card (Front + Back)
8. Component — Module Overlay
9. Component — Permit Matrix (OptiTrack)
10. Component — Cash Flow Chart (OptiBiz)
11. Component — Risk Heat Map (OptiRisk)
12. Component — Document Table (OptiDocs)
13. Component — Gantt Timeline (OptiGantt)
14. Component — AI Chat Panel
15. Component — Tooltip
16. Component — Activity Feed
17. Animations & Keyframes
18. Utilities

**JS `<script>` Block Order:**
1. Data Constants — `PROJECT_DATA`, `OPTITRACK_DATA`, `OPTIBIZ_DATA`, `OPTIRISK_DATA`, `OPTIDOCS_DATA`, `OPTIGANTT_DATA`, `AI_CACHE`
2. Configuration — `API_KEY`, `IDLE_TIMEOUT_MS`, `MAX_CHAT_HISTORY`, `ANIMATION_DURATIONS`
3. OptiPlan Namespace & State Management — `OptiPlan.state`, `.transition()`, `.set()`
4. Utility Functions — `OptiPlan.utils.formatCurrency`, `formatPercent`, `formatDate`
5. UI Reflection Layer — `OptiPlan.ui.reflect()`, `OptiPlan.ui.reflectLayer()`
6. Component — Sidebar — `OptiPlan.components.sidebar`
7. Component — Topbar — `OptiPlan.components.topbar`
8. Component — Hero Gauges — `OptiPlan.components.heroGauges`
9. Component — Stat Cards — `OptiPlan.components.statCards`
10. Component — Module Overlay — `OptiPlan.components.moduleOverlay`
11. Module — OptiTrack — `OptiPlan.modules.optitrack`
12. Module — OptiBiz — `OptiPlan.modules.optibiz`
13. Module — OptiRisk — `OptiPlan.modules.optirisk`
14. Module — OptiDocs — `OptiPlan.modules.optidocs`
15. Module — OptiGantt — `OptiPlan.modules.optigantt`
16. AI Chat — `OptiPlan.ai`
17. Animation Orchestration — `OptiPlan.animations`
18. Touch & Event Management — `OptiPlan.touch`
19. Initialization — `OptiPlan.init()` called on `DOMContentLoaded`

Each section delimited by `// ================================================================` comment blocks.

### Architectural Boundaries

**Component Boundaries:**
- Each component namespace (`OptiPlan.components.*`) owns its DOM subtree and CSS classes
- Components communicate ONLY through the state object — never directly call each other
- Module overlays (`OptiPlan.modules.*`) are children of the module overlay component — they render into a container provided by `moduleOverlay.expand()`

**State Boundaries:**
- `OptiPlan.state` is the ONLY mutable shared state
- Components read state, they don't own it
- Layer transitions go through `OptiPlan.state.transition()` — never direct class manipulation

**Data Boundaries:**
- Data constants (`PROJECT_DATA`, `*_DATA`) are read-only after initialization
- No data mutation at runtime — all data is static
- AI chat history is the only runtime-growing data (capped at `MAX_CHAT_HISTORY`)

**External Integration Boundary:**
- `OptiPlan.ai` is the ONLY module that makes network requests
- All other components are fully offline-capable
- AI module exposes `sendMessage()` — internals (fetch, cache, fallback) are private

### Requirements to Structure Mapping

| FR Category | HTML Section | CSS Section | JS Section |
|---|---|---|---|
| Dashboard Overview (FR1-5) | Main Content Area | Hero Gauge, Stat Card, Main Grid | heroGauges, statCards, sidebar, topbar |
| Module Exploration (FR6-10) | Module Overlays | Module Overlay, Stat Card | moduleOverlay, statCards (flip) |
| AI Intelligence (FR11-16) | AI Chat Panel | AI Chat Panel | ai |
| Data Viz (FR17-27) | Module Overlays | Per-module component sections | modules.optitrack through modules.optigantt |
| Visual Experience (FR28-33) | (all) | Animations & Keyframes | animations |
| Content & IA (FR34-39) | (all) | Design Tokens, Utilities | Data Constants, utils |
| Resilience (FR40-44) | (all) | (none) | state, ai (cache), touch |

### Data Flow

```
User Touch/Click
    → OptiPlan.touch (event handler)
    → OptiPlan.state.transition() or OptiPlan.state.set()
    → OptiPlan.ui.reflect() (CSS class toggle)
    → CSS transition/animation fires
    → Component.render() if data display needed

AI Chat Flow:
    User Input → OptiPlan.ai.sendMessage()
    → fetch(LLM_API) with AbortController timeout
    → Success: displayResponse(apiResponse)
    → Failure: findCachedResponse(userText) → displayResponse(cached)
```

### Development Workflow

- **Edit:** Open `index.html` in VS Code, navigate via section delimiters (Cmd+G or search `SECTION:`)
- **Preview:** VS Code Live Server → opens in Chrome/Safari
- **Test:** Manual on real iPad Safari hardware
- **Deploy:** `git add index.html && git commit && git push origin main` → GitHub Pages auto-deploys
- **Debug:** Safari Web Inspector → Console, Network, Elements, Timeline tabs

## Architecture Validation Results

### Coherence Validation ✅

**Decision Compatibility:**
All decisions are internally consistent. No conflicts found:
- Single-file architecture ↔ Namespace-based JS organization ✅ (namespaces work without modules)
- CSS custom properties ↔ Theme switching via `data-theme` ✅ (cascade designed for this)
- State machine ↔ Layer-based navigation ✅ (states map 1:1 to layers)
- SVG generation ↔ No build tooling ✅ (template literals, no JSX needed)
- Fetch + cache ↔ GitHub Pages CORS ✅ (outbound POST, no server needed)

**Pattern Consistency:**
- BEM-like CSS naming ↔ `OptiPlan.*` JS namespaces ↔ kebab-case HTML attributes — all coherent, no naming collisions
- `$` DOM prefix ↔ named event handlers ↔ cleanup pattern — consistent memory safety approach
- State-first/DOM-second ↔ `ui.reflect()` layer — consistent data flow direction

**Structure Alignment:**
- Internal file section order matches component dependency chain (tokens → layout → components → modules → init)
- JS section order ensures data constants exist before components that reference them
- CSS section order ensures tokens cascade before component styles

### Requirements Coverage Validation ✅

**Functional Requirements Coverage:**

| FR Category | Coverage | Architectural Support |
|---|---|---|
| Dashboard Overview (FR1-5) | ✅ Full | Hero gauges, stat cards, sidebar, topbar, animation system |
| Module Exploration (FR6-10) | ✅ Full | State machine (card-flip, module-expand), moduleOverlay component, back navigation via state reversal |
| AI Intelligence (FR11-16) | ✅ Full | OptiPlan.ai module, fetch+cache pattern, AI_CACHE for fallback, Hebrew input handling |
| Data Visualization (FR17-27) | ✅ Full | Per-module SVG/table components mapped to specific modules |
| Visual Experience (FR28-33) | ✅ Full | CSS animations + JS orchestration, GPU-composited only, animation budget defined |
| Content & IA (FR34-39) | ✅ Full | RTL layout, CSS tokens from design system, Hebrew in data constants, data consistency rules |
| Resilience (FR40-44) | ✅ Full | Single-file load, state machine prevents errors, AI cache fallback, no memory growth patterns |

**Non-Functional Requirements Coverage:**

| NFR | Coverage | How Addressed |
|---|---|---|
| First paint <1s | ✅ | Pre-rendered HTML, single file, no JS-driven initial render |
| Interactive <2s | ✅ | DOMContentLoaded init, no async dependencies for core UI |
| Touch response <100ms | ✅ | Passive listeners, 300ms bypass, CSS transitions (not JS) |
| 60fps animations | ✅ | GPU-composited only (transform/opacity), ≤6 concurrent idle animations |
| AI response <3s | ✅ | 8s timeout with instant cache fallback |
| Zero memory growth | ✅ | Named handlers for cleanup, capped chat history, no growing arrays, CSS animations for loops |
| 3-4 hour runtime | ✅ | CSS infinite animations (no JS timers), single setTimeout ref for idle, no DOM accumulation |
| Offline resilience | ✅ | All data local, only AI needs network, cache fallback seamless |
| GitHub Pages deploy | ✅ | Single file, zero build, git push deploys |

### Implementation Readiness Validation ✅

**Decision Completeness:** All 9 critical/important decisions documented with concrete examples.

**Structure Completeness:** Internal file structure fully specified with 18 CSS sections and 19 JS sections, each with clear namespace assignments.

**Pattern Completeness:** 12 conflict points identified and addressed. Good/bad examples provided. 10 enforcement rules documented.

### Gap Analysis Results

**Critical Gaps:** None found. All blocking decisions are made.

**Important Gaps (addressable during implementation):**

1. **Specific SVG viewBox dimensions** — Not pre-defined for each chart type. Agents will determine appropriate viewBox sizes based on data density. Mockup reference (`optiplan-v2.html`) provides visual targets.
2. **LLM API system prompt** — Architecture specifies fetch+cache but not system prompt content. Content decision for implementation phase.
3. **Exact cached Q&A pairs** — `AI_CACHE` structure defined but specific Hebrew question-answer pairs not pre-authored. Content task for implementation.
4. **Touch gesture specifics** — Standard viewport meta + `touch-action: manipulation` CSS patterns — well-documented Safari solutions.

**Nice-to-Have Gaps:**
- No accessibility specification beyond touch targets (appropriate for demo)
- No performance monitoring strategy (manual Safari Web Inspector sufficient)

### Architecture Completeness Checklist

**✅ Requirements Analysis**
- [x] Project context thoroughly analyzed (44 FRs, 8 categories)
- [x] Scale and complexity assessed (Low infrastructure / High UX)
- [x] Technical constraints identified (10 hard constraints)
- [x] Cross-cutting concerns mapped (8 concerns)

**✅ Starter Template Evaluation**
- [x] Technology domain identified (Vanilla HTML/CSS/JS)
- [x] Standard starters evaluated and eliminated
- [x] Hand-crafted scaffold selected with rationale
- [x] External dependencies enumerated (CDN only)

**✅ Architectural Decisions**
- [x] 6 critical decisions documented
- [x] 3 important decisions documented
- [x] 3 deferred decisions documented with rationale
- [x] State management, animation, visualization, AI patterns all defined

**✅ Implementation Patterns**
- [x] 12 conflict points identified
- [x] Naming conventions: CSS properties, CSS classes, JS, HTML attributes
- [x] Structure patterns: section organization, delimiter format
- [x] Format patterns: data objects, number formatting
- [x] Communication patterns: state updates, event listeners
- [x] Process patterns: error handling, loading states
- [x] 10 enforcement rules documented
- [x] Good examples and anti-patterns provided

**✅ Project Structure**
- [x] Repository file structure defined
- [x] Internal HTML structure mapped (18 CSS + 19 JS sections)
- [x] Architectural boundaries defined (component, state, data, external)
- [x] Requirements mapped to specific code sections
- [x] Data flow documented
- [x] Development workflow specified

### Architecture Readiness Assessment

**Overall Status:** ✅ READY FOR IMPLEMENTATION

**Confidence Level:** HIGH — All 44 functional requirements and all NFRs have clear architectural support. No ambiguous decisions remain.

**Key Strengths:**
1. Extreme simplicity — single file, zero build, zero infrastructure means zero deployment failures
2. Complete state machine prevents impossible UI states
3. GPU-composited animation rules prevent jank across 3-4 hour runtime
4. AI resilience chain (fetch → cache → fallback) ensures investor never sees failure
5. Comprehensive naming/pattern rules prevent agent conflicts in shared single file

**Areas for Future Enhancement (post-demo):**
- Multi-device responsive design (currently iPad landscape only)
- Automated testing (if product evolves beyond demo)
- Service worker for true offline mode
- Multiple project data sets (currently single fictitious project)

### Implementation Handoff

**AI Agent Guidelines:**
1. Follow all architectural decisions exactly as documented
2. Use implementation patterns consistently across all components
3. Respect the `OptiPlan.*` namespace — no globals
4. Animate only `transform` and `opacity` — no exceptions
5. Reference `design-system.html` and `optiplan-v2.html` for visual targets
6. Test on real iPad Safari — Chrome is not sufficient

**First Implementation Priority:**
Create `index.html` with complete HTML scaffold, CSS custom properties (all design tokens from design-system.html), structural layout (sidebar + topbar + main grid), and base JavaScript architecture (`OptiPlan` namespace, state management, utility functions).
