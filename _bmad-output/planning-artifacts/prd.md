---
stepsCompleted:
  - step-01-init
  - step-02-discovery
  - step-02b-vision
  - step-02c-executive-summary
  - step-03-success
  - step-04-journeys
  - step-05-domain
  - step-06-innovation
  - step-07-project-type
  - step-08-scoping
  - step-09-functional
  - step-10-nonfunctional
  - step-11-polish
inputDocuments:
  - user-data/product-brief-v2.md
  - user-data/dashboard-build-document.md
documentCounts:
  briefs: 1
  research: 0
  brainstorming: 0
  projectDocs: 1
workflowType: 'prd'
projectName: 'optiplan-dashboard'
classification:
  projectType: web_app
  domain: general
  complexity: low
  projectContext: brownfield
---

# Product Requirements Document - optiplan-dashboard

**Author:** BenAkiva
**Date:** 2026-02-22

## Executive Summary

The OptiPlan Interactive Dashboard is a high-fidelity, touch-first demo application designed to showcase the OptiPlan platform to angel investors at a live conference (Jaffa, February 24, 2026). It is not a production application — it is an investor seduction tool that simulates the OptiPlan Hub Dashboard, the central command center for managing civil engineering infrastructure projects.

The demo presents a single fictitious transit project (Metro Line Extension — Gush Dan, ~22 km, 6 sections, NTA client, ₪1.8B budget) and allows investors to explore all five platform modules — OptiTrack (coordination control), OptiBiz (financial planning), OptiRisk (risk management), OptiDocs (documents & tasks), and OptiGantt (scheduling) — through a 3-layer interaction model: overview gauges, card-flip KPI details, and full module expansion with charts and tables.

The primary goal is emotional, not functional: investors must leave the table feeling OptiPlan is a revolutionary, investable product. The dashboard achieves this through cinematic-quality animations, fluid touch interactions, premium visual design ("Calvin Klein, not Ralph Lauren"), and a mandatory live AI chat via LLM API that responds in Hebrew with project-relevant intelligence — the "holy shit this is real" moment.

Target deployment: GitHub Pages as static files. Vanilla HTML/CSS/JS chosen for zero-dependency deployment and fastest AI-assisted build velocity — no framework overhead justified at this scope. iPad landscape optimized, Hebrew RTL, all data hardcoded.

### What Makes This Special

- **The medium is the message.** Investors have seen slide decks. They've never *touched* a product at a conference table. The interactive format itself communicates innovation and execution capability.
- **Polish as proof of competence.** Every animation, every transition, every data visualization signals that this team builds premium software. The demo quality implies product quality.
- **Live AI as the kicker.** A mandatory LLM API call (Gemini/Claude) that responds contextually in Hebrew about the fictitious project — transforming the demo from "impressive mockup" to "this is actually intelligent."
- **Depth over breadth.** Three interaction layers mean investors can go as deep as they want. Surface-level browsers see polished gauges. Curious investors find real KPIs, permit matrices, cash flow charts, and risk heat maps underneath.

## Project Classification

| Attribute | Value |
|-----------|-------|
| **Project Type** | Web App (static SPA — single HTML file) |
| **Domain** | General (investor demo / sales tool) |
| **Complexity** | Low (hardcoded data, no backend, no auth) |
| **Project Context** | Brownfield (built on existing OptiPlan platform specs and design system) |
| **Target Platform** | iPad landscape, touch-first, responsive |
| **Deployment** | GitHub Pages (static files) |
| **Tech Stack** | Vanilla HTML / CSS / JS, Google Fonts (Varela Round), no build tools |

## Success Criteria

### User Success

The investor who walks up to the iPad:
- **Drawn in from a distance** — the dashboard is visually magnetic at idle. Subtle animated gradient, large confident typography, and motion (e.g., pulsing AI agent indicator, gauge animations) pull attention from across the room — like an iPhone display in an Apple Store.
- **Self-explanatory without narration** — within 30 seconds of touching the dashboard, an investor understands: this is a command center for infrastructure projects that connects engineering, financial, and administrative data in one place.
- **Feels premium, not generic** — every interaction feels intentional, polished, and luxurious. No jank, no loading spinners, no dead-end taps. The quality of the demo implies the quality of the company.
- **AI chat feels essential** — the chat suggests relevant questions to ask and provides substantive, Hebrew-language answers about the fictitious project. It should feel like talking to a knowledgeable project advisor, not a chatbot.

### Business Success

| Metric | Target |
|--------|--------|
| **Primary outcome** | Investor interest that converts to capital raised |
| **Leading indicator** | Investors engage with the dashboard *before* talking to the team |
| **Engagement quality** | Investors ask follow-up questions about the platform (not the demo) — they confuse the demo for the real product |
| **Conference validation** | Multiple investors express intent to invest or schedule follow-up meetings |

### Technical Success

| Requirement | Target |
|-------------|--------|
| **Load time** | Under 2 seconds on iPad Safari over conference WiFi |
| **Animation smoothness** | 60fps on all transitions — zero jank on card flips, expansions, gauge fills |
| **Touch responsiveness** | Every tap responds within 100ms — no dead zones, no unresponsive states |
| **AI chat response** | Live LLM API responses in under 3 seconds, Hebrew, contextually relevant to the project data |
| **Zero crashes** | No freezes, no white screens, no JS errors during any interaction path |
| **Idle state** | Dashboard must look alive and inviting even when nobody is interacting — subtle animations, gradient motion |

### Measurable Outcomes

- An investor who spends 2+ minutes with the dashboard without team assistance = success signal
- An investor who asks "is this the real product?" = maximum success signal
- Post-conference: investment conversations directly attributed to the dashboard experience

## Product Scope

### MVP (Must ship for Feb 24)

- Layer 1: Landing screen with 3 hero gauges, 5 module cards, AI agent indicator, project header
- Layer 2: Card flip with 3-5 KPIs per module, trend indicators
- Layer 3: Full module expansion with charts, tables, realistic data for all 5 modules
- Live AI chat with LLM API integration — suggests questions, answers in Hebrew about the project
- Subtle animated gradient background or ambient motion at idle
- All animations: card flip (~400ms), expand (~500ms), gauge fill on load (~800ms)
- Full RTL Hebrew, Varela Round font, OptiPlan logo
- iPad landscape optimized, touch-first (min 44x44px targets)
- Single HTML file (or minimal files), GitHub Pages deployed
- Hardcoded realistic data for the Gush Dan Metro fictitious project

### Growth Features (Post-Conference)

- Multi-project view (portfolio dashboard for CEO persona)
- Localization toggle (Hebrew/English)
- Shareable link with custom branding per investor
- Expanded AI capabilities — connect to real OptiPlan data

### Vision (Future)

- Dashboard evolves into the actual production Hub Dashboard for the OptiPlan platform
- Demo codebase serves as design reference and component library for production build

## User Journeys

### Journey 1: "The Drive-By" — Yossi, Angel Investor (52)

**Opening Scene:** Yossi is working the room at the Jaffa conference. He's talked to six startups already, his coffee is getting cold. He glances across the hall and notices an iPad on a table glowing with something that looks... different. Not a slide deck. Something alive — colors shifting subtly, gauges moving, Hebrew text that looks like a real product. He walks over.

**Rising Action:** Nobody from OptiPlan intercepts him. He picks up the iPad. The dashboard is self-explanatory — a project name at the top ("הרחבת קו המטרו — קטע גוש דן"), three big gauges showing financial health, coordination completion, schedule adherence. Five module cards below, each with a number and a colored bar. He taps one — it flips smoothly to show more detail. He taps another. Everything responds instantly, everything moves like water.

**Climax:** He notices the AI indicator pulsing: "3 סוכנים פעילים." He taps it — a tooltip appears explaining AI agents are analyzing emails and extracting tasks in the background. He thinks: "Wait — this is working *right now*?"

**Resolution:** Yossi puts the iPad down after 90 seconds. He doesn't know the full platform story yet, but he knows two things: this team builds beautiful software, and whatever they're building feels real. He picks up a business card from the table. He'll text later.

**Requirements revealed:** Idle-state visual magnetism, instant touch response, self-explanatory Layer 1, tooltip copy that sells without selling, AI agent indicator as a curiosity hook.

---

### Journey 2: "The Deep Diver" — Michal, Investment Fund Associate (34)

**Opening Scene:** Michal was sent by her fund partner to scout interesting deals. She's methodical — she's already photographed three pitch decks. She sits down at the OptiPlan table, iPad in hand, and starts exploring systematically.

**Rising Action:** She taps OptiTrack. The card flips — 347 coordination items, 64% approved, 12 objections. She taps "למודול המלא" and the card expands. A full permit matrix appears — sections vs. authorities, color-coded cells. She sees Israel Electric Corp with 8 pending items. She scrolls through a days-in-status histogram and a recent activity feed. She taps back, opens OptiBiz. A 12-month cash flow chart with plan vs. actual. She opens OptiRisk — a 5x5 heat map, trend lines, a top risks table with severity and mitigation status.

**Climax:** She opens the AI chat at the bottom of the expanded view. Suggested questions appear: "מה הסיכון הגדול ביותר בפרויקט?" She taps it. Within 2 seconds, the AI responds in Hebrew: "הסיכון המשמעותי ביותר הוא עיכוב באישור חברת החשמל בקטע א3, עם השפעה משוערת של 1.2 מיליון ש״ח על התזרים..." She types her own question: "כמה זמן בממוצע לוקח אישור מרשות העתיקות?" The AI answers with data.

**Resolution:** Michal has spent 8 minutes. She takes a screenshot. She messages her fund partner: "Come see this. It's a command center for infrastructure projects. The AI actually knows the project." She asks BenAkiva for a meeting next week.

**Requirements revealed:** Full depth in all 5 module expansions, realistic and internally consistent data, AI chat with suggested questions + free-text input, Hebrew responses that reference specific project data, permit matrix and financial charts must feel production-quality.

---

### Journey 3: "The Skeptic" — Dan, Serial Angel Investor (60)

**Opening Scene:** Dan has invested in 30+ startups. He's seen every trick — animated prototypes, "live demos" that are actually videos, clickable Figmas dressed as products. He picks up the iPad expecting to find the dead end within 30 seconds.

**Rising Action:** He taps a module card. It flips — real KPIs. He taps the full module. Charts render with real data. He taps back. Tries another module. Same depth. He drills into the risk heat map, scrolls the Gantt timeline, checks if the cash flow numbers add up across months. He's looking for the seam — the place where the facade ends.

**Climax:** He opens the AI chat and types something deliberately obscure: "מה הקשר בין ההתנגדות של מקורות לעיכוב בקטע א2?" The AI responds with a coherent, project-specific answer connecting water authority objections to schedule impact on section A2. Dan pauses. This isn't a canned response.

**Resolution:** Dan puts the iPad down and looks for someone from the team. His first question isn't about the demo — it's "How far along is the actual platform?" He's been converted. The demo was too complete, too responsive, too intelligent to be vaporware.

**Requirements revealed:** Zero dead ends anywhere in the interaction tree, data consistency across modules (numbers must cross-reference correctly), AI must handle unexpected/adversarial questions gracefully, no "coming soon" or disabled features — everything shown must work.

---

### Journey 4: "The Operator" — BenAkiva, OptiPlan Team (Setup)

**Opening Scene:** BenAkiva arrives at the conference 45 minutes early. He opens the iPad, navigates to the GitHub Pages URL, and loads the dashboard.

**Rising Action:** The page loads in under 2 seconds. Gauges animate in, cards stagger-fade, the gradient starts moving. He taps through each module to verify everything renders correctly on this WiFi. He tests the AI chat — response comes back in 2 seconds. He adjusts the iPad angle on the table for maximum visibility from the aisle.

**Climax:** The dashboard sits on the table, alive and glowing, while BenAkiva greets the first arrivals. He doesn't need to touch it — investors approach on their own.

**Resolution:** Throughout the event, the dashboard runs continuously without crashes, freezes, or performance degradation. No page refreshes needed. The AI chat handles dozens of questions without errors.

**Requirements revealed:** Reliable load on conference WiFi, long-running stability (3-4 hours without refresh), no memory leaks from animations, graceful AI error handling (if API fails, show fallback — never a broken UI).

---

### Journey-to-Requirements Traceability

All capabilities revealed by these journeys are formally captured in the **Functional Requirements** section (FR1–FR44). Key traceability:

- **Drive-By journey** → FR1-5 (overview), FR28-30 (idle magnetism), FR34-37 (self-explanatory content)
- **Deep Diver journey** → FR6-8 (module exploration), FR11-14 (AI chat), FR17-27 (all data visualizations)
- **Skeptic journey** → FR10 (zero dead ends), FR15 (adversarial AI handling), FR39 (data consistency)
- **Operator journey** → FR40-44 (resilience, stability, deployment)

## Web App Specific Requirements

### Project-Type Overview

Single-page static web application with no routing, no server-side logic, and no build pipeline. All state management is client-side. The application operates as a series of layer transitions (idle → Layer 1 → card flip → module expansion) within a single HTML document. The only network dependency is the AI chat feature, which makes API calls to an external LLM provider.

### Technical Architecture Considerations

**Application Type:** SPA (single-page, no routing)
**Rendering:** Client-side only — all DOM manipulation via vanilla JS
**State Management:** Simple JS state object tracking current view (which layer, which module expanded, chat history). No framework state management needed.
**Network Calls:** Single dependency — LLM API for chat. All other data is hardcoded in JS objects/arrays.
**File Structure:** Single HTML file preferred (inline CSS + JS) or HTML + 1 CSS + 1 JS max.

### Browser & Device Matrix

| Target | Priority | Notes |
|--------|----------|-------|
| **iPad Safari (landscape)** | Primary — must be flawless | Only browser that matters for conference |
| iPad Safari (portrait) | Not required | Landscape-only is acceptable |
| Desktop browsers | Not targeted | May work incidentally, no testing required |
| Android tablets | Not targeted | |
| Mobile phones | Not targeted | |

**Safari-Specific Considerations:**
- Webkit prefixes required for 3D transforms and animations
- Touch events must bypass the 300ms click delay
- Test all interactions on real iPad Safari hardware before conference

### Responsive Design

| Breakpoint | Device | Layout |
|------------|--------|--------|
| **1024×1366** | iPad Pro 12.9" landscape | Primary target — 3 gauges row, 5 cards in 3+2 grid |
| **1180×820** | iPad 10.9" landscape | Same layout, slightly compressed spacing |

**Layout Rules:**
- Max width: 1200px, centered
- Direction: RTL (`dir="rtl"`)
- Card grid: CSS Grid or Flexbox, 3+2 arrangement
- Expanded module: 80% viewport width, 85% height, centered overlay
- Minimum touch target: 44×44px (Apple HIG)
- Padding: 24px outer, 24-32px card internal
- Card gap: 16-20px

### Performance Targets

See **Non-Functional Requirements > Performance** for measurable targets. Key constraint: all animations must use GPU-composited properties only (transform, opacity) to maintain 60fps on iPad hardware.

### SEO Strategy

Not applicable. Private demo URL, not indexed. Add `<meta name="robots" content="noindex">` to prevent accidental indexing.

### Accessibility

Not in scope. Conference-specific demo for sighted touch users. No ARIA, screen reader support, or keyboard navigation required.

### Implementation Considerations

- **Font loading:** Varela Round from Google Fonts, must not block first paint
- **Logo:** Loaded asset with text fallback if unreachable
- **AI chat:** API key embedded client-side (acceptable for demo scope). CORS handling required for GitHub Pages origin.
- **Offline behavior:** All non-AI features must work without network. AI chat falls back to cached responses.
- **iPad Safari:** Must test on real hardware — known quirks with scroll behavior, fixed positioning, and double-tap zoom

## Project Scoping & Phased Development

### MVP Strategy & Philosophy

**MVP Approach:** Experience MVP — the minimum feature set that delivers a *complete emotional experience* to the investor. This is not a feature-validation MVP; it's a "proof of premium" MVP. Every feature shipped must be polished to production quality. Half-built features are worse than missing features.

**Resource Requirements:** 1 developer (AI-assisted), ~2 days. No backend team needed. LLM API key required for AI chat.

### MVP Feature Set (Phase 1 — Ship by Feb 24)

**Core Journeys Supported:** All four (Drive-By, Deep Diver, Skeptic, Operator)

**Must-Have Capabilities (ordered by implementation priority):**

| Priority | Feature | Rationale |
|----------|---------|-----------|
| **P0** | Layer 1 — top bar, 3 gauges, 5 module cards, AI indicator | First impression. Without this, nothing else matters. |
| **P0** | Idle-state animations — gradient, gauge pulse, card stagger | The "magnet" effect. Must work even when nobody's touching. |
| **P0** | Card flip (Layer 2) — all 5 modules with KPIs | Second interaction. Proves depth exists. |
| **P0** | Full RTL Hebrew, Varela Round font, OptiPlan logo, premium styling | Non-negotiable for Israeli investor audience. |
| **P1** | Module expansion (Layer 3) — all 5 modules with charts/tables | Deep Diver and Skeptic journeys depend on this. |
| **P1** | AI chat with LLM API — suggested questions + free text | The "holy shit" moment. Must-have per owner direction. |
| **P1** | Internally consistent hardcoded data across all modules | Skeptic-proofing. Numbers must cross-reference. |
| **P2** | Tooltips with sales-oriented Hebrew copy | Drive-By journey enhancement. |
| **P2** | Back navigation from expanded view | Essential UX — but simple to implement. |

**Cut-if-needed (only if time runs out):**
- Layer 3 depth for OptiGantt (simplified Gantt is acceptable — a full interactive timeline is complex)
- Days-in-status histogram in OptiTrack (progress bars sufficient)
- Cumulative balance line in OptiBiz (bar chart is enough)

### Post-MVP Features

**Phase 2 (Post-Conference):**
- Multi-project portfolio view
- Hebrew/English toggle
- Shareable investor-specific links
- Expanded AI with real OptiPlan data connection

**Phase 3 (Production Evolution):**
- Dashboard becomes the actual OptiPlan Hub Dashboard
- Component library extracted from demo code
- Backend integration with real project data

### Risk Mitigation Strategy

| Risk | Impact | Mitigation |
|------|--------|------------|
| **AI API fails at conference (WiFi/quota)** | Critical — kills the "wow" moment | Pre-cache 5-8 common Q&A pairs as fallback. If API unreachable, serve cached responses. Never show an error to the investor. |
| **iPad Safari rendering bug** | Critical — breaks first impression | Test on real iPad before conference. Have a backup iPad. Keep CSS simple (no cutting-edge features). |
| **Conference WiFi too slow for initial load** | High — investor walks away | Pre-load page before conference starts. Consider service worker for offline capability. Single-file architecture minimizes requests. |
| **Animations jank on iPad** | High — undermines premium feel | Use only GPU-composited properties (transform, opacity). Test all transitions on real hardware. |
| **Data inconsistency caught by Skeptic** | Medium — breaks credibility | Create data model first, derive all KPIs from it. Cross-check numbers across modules before shipping. |
| **Time runs out before all Layer 3 views done** | Medium — limits depth | Prioritize OptiTrack and OptiBiz (most impressive). Simplify OptiGantt and OptiRisk if needed. |

## Functional Requirements

### Dashboard Overview (Layer 1)

- **FR1:** Investor can view a project header displaying the project name, client, and key identifiers
- **FR2:** Investor can view 3 hero gauges showing high-level project health metrics (financial health, coordination completion, schedule adherence)
- **FR3:** Investor can view 5 module cards representing OptiTrack, OptiBiz, OptiRisk, OptiDocs, and OptiGantt
- **FR4:** Investor can see an AI agent status indicator displaying the count of active agents
- **FR5:** Dashboard displays ambient visual activity in idle state without requiring user interaction

### Module Exploration (Layer 2 & Layer 3)

- **FR6:** Investor can tap any module card to reveal detailed KPIs for that module
- **FR7:** Investor can view 3–5 KPIs with trend indicators on each revealed card
- **FR8:** Investor can expand any module to view its full data visualization (charts, tables, detailed data)
- **FR9:** Investor can navigate back from an expanded module to the overview
- **FR10:** Investor can interact with all modules in any order without encountering dead ends or disabled states

### AI-Powered Intelligence

- **FR11:** Investor can access an AI chat interface from the dashboard
- **FR12:** Investor can select from suggested questions relevant to the displayed project data
- **FR13:** Investor can type free-text questions in Hebrew
- **FR14:** System responds to questions in Hebrew with answers that reference specific project data points
- **FR15:** System handles unexpected or adversarial questions with coherent, project-relevant responses
- **FR16:** System provides pre-cached responses as fallback when the LLM API is unavailable

### Data Visualization — OptiTrack

- **FR17:** Investor can view a coordination permit matrix showing sections vs. regulatory authorities with approval status
- **FR18:** Investor can view coordination progress metrics (items count, approval rate, objections)
- **FR19:** Investor can view a recent activity feed for coordination items

### Data Visualization — OptiBiz

- **FR20:** Investor can view a multi-month cash flow chart showing plan vs. actual expenditure
- **FR21:** Investor can view financial KPIs including budget utilization and overrun/underrun status

### Data Visualization — OptiRisk

- **FR22:** Investor can view a risk severity heat map (likelihood × impact)
- **FR23:** Investor can view top risks with severity rating, trend, and mitigation status

### Data Visualization — OptiDocs

- **FR24:** Investor can view document and task tracking status with categorization
- **FR25:** Investor can view document completion metrics and pending items

### Data Visualization — OptiGantt

- **FR26:** Investor can view a project schedule visualization showing sections and milestones
- **FR27:** Investor can view schedule adherence status per project section

### Visual Experience & Animation

- **FR28:** Dashboard displays a subtle animated gradient background that draws visual attention from a distance
- **FR29:** Gauges animate on initial page load to convey data filling in
- **FR30:** Module cards display staggered entrance animations on load
- **FR31:** Card reveal transitions animate smoothly when investor taps a module card
- **FR32:** Module expansion transitions animate smoothly when investor opens full view
- **FR33:** AI agent indicator displays a pulsing animation to signal activity

### Content & Information Architecture

- **FR34:** All text content displays in Hebrew (RTL direction) using the OptiPlan design language
- **FR35:** Dashboard displays OptiPlan branding (logo, color palette, typography)
- **FR36:** Investor can view contextual tooltips on key elements that explain their meaning in sales-oriented language
- **FR37:** Dashboard is self-explanatory — key elements convey purpose without external narration
- **FR38:** All displayed data represents a realistic fictitious transit infrastructure project (Gush Dan Metro Extension)
- **FR39:** All data values are internally consistent across modules (numbers cross-reference correctly)

### Resilience & Operational Readiness

- **FR40:** Operator can load the dashboard from a single URL without configuration
- **FR41:** Dashboard operates in long-running sessions without requiring page refresh
- **FR42:** All non-AI features continue functioning when network connectivity is lost
- **FR43:** AI chat displays a graceful Hebrew-language message when API is unavailable, without breaking the UI
- **FR44:** Dashboard recovers gracefully from any interaction state (no stuck screens or unresponsive states)

## Non-Functional Requirements

### Performance

| Metric | Requirement | Rationale |
|--------|-------------|-----------|
| **First paint** | < 1 second on iPad Safari over WiFi | Investor shouldn't see a blank screen |
| **Fully interactive** | < 2 seconds from URL load | Operator needs fast setup; investor needs instant access |
| **Touch response** | < 100ms from tap to visual feedback | Premium feel requires zero perceived latency |
| **Card flip animation** | ~400ms duration, 60fps | Smooth enough to feel physical, fast enough to not block exploration |
| **Module expand animation** | ~500ms duration, 60fps | Cinematic but not slow |
| **Gauge fill animation** | ~800ms on initial load, 60fps | Entrance drama without delay |
| **Idle gradient animation** | Continuous, 60fps, < 5% CPU | Must run for hours without heating the iPad |
| **AI chat response** | < 3 seconds from question to first visible response | Conversational feel — longer feels broken |
| **Memory footprint** | No growth over 4-hour session | No DOM leaks, no animation frame accumulation |

### Reliability

| Metric | Requirement | Rationale |
|--------|-------------|-----------|
| **Continuous runtime** | 3-4 hours without page refresh or performance degradation | Full conference duration |
| **Zero JS errors** | No uncaught exceptions across any interaction path | A single error modal kills the illusion |
| **Animation stability** | All CSS animations/transitions complete without jank or stutter over extended runtime | Premium feel must not degrade over time |
| **Offline resilience** | All non-AI features operate normally without network | Conference WiFi is unreliable |
| **AI graceful degradation** | Chat falls back to cached responses silently — no error states visible to investor | The illusion must not break |

### Integration

| Metric | Requirement | Rationale |
|--------|-------------|-----------|
| **LLM API** | Single external dependency — REST API call to LLM provider (Gemini/Claude) | AI chat is the only network-dependent feature |
| **API authentication** | API key embedded client-side (acceptable for demo scope) | No backend to proxy through |
| **CORS handling** | API calls must work from GitHub Pages origin | Cross-origin restrictions may apply depending on provider |
| **Fallback mechanism** | 5-8 pre-cached Q&A pairs served when API is unreachable | Seamless experience regardless of connectivity |
| **Font loading** | Google Fonts (Varela Round) with `font-display: swap` | Prevent flash of invisible text on slow WiFi |

### Deployment & Hosting

| Metric | Requirement | Rationale |
|--------|-------------|-----------|
| **Hosting** | GitHub Pages (static files only) | Zero cost, zero infrastructure, URL accessible from any network |
| **File structure** | Single HTML file or HTML + 1 CSS + 1 JS maximum | Minimal HTTP requests, simplest possible deployment |
| **Build tools** | None — no compilation, no bundling, no transpilation | What you write is what ships |
| **External dependencies** | Google Fonts (Varela Round) + LLM API only | No CDN libraries, no frameworks |
