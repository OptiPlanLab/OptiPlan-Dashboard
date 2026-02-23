---
stepsCompleted: [1, 2, 3]
inputDocuments:
  - _bmad-output/planning-artifacts/prd.md
  - _bmad-output/planning-artifacts/ux-design-specification.md
  - _bmad-output/planning-artifacts/design-system.html
  - _bmad-output/planning-artifacts/optiplan-v2.html
  - user-data/product-brief-v2.md
  - user-data/dashboard-build-document.md
workflowType: 'architecture'
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
