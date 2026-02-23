# OptiPlan Interactive Dashboard — Build Document

**Date:** 22 February 2026
**Purpose:** Complete specification for building an interactive investor demo dashboard
**Target event:** Angel investor conference, Jaffa, 24 February 2026
**Device:** iPad (touch-first, responsive)
**Deployment:** Single HTML file (or minimal files), GitHub Pages compatible
**Language:** Hebrew (RTL), with English technical terms

> **This document is the single source of truth for the builder agent.**
> Attach the product brief V2 (`product-brief-v2.md`) as a reference for narrative, module descriptions, and personas — but all dashboard-specific specs are here.

---

## Table of Contents

1. [What This Dashboard Is](#1-what-this-dashboard-is)
2. [Fictitious Project Definition](#2-fictitious-project-definition)
3. [Interaction Model — 3 Layers](#3-interaction-model--3-layers)
4. [Module Definitions & KPIs](#4-module-definitions--kpis)
5. [Design System](#5-design-system)
6. [Tooltip & UX Copy Philosophy](#6-tooltip--ux-copy-philosophy)
7. [Architecture Visualization](#7-architecture-visualization)
8. [Technical Requirements](#8-technical-requirements)
9. [Data Appendix — Authorities & Enums](#9-data-appendix--authorities--enums)

---

## 1. What This Dashboard Is

This is **not a real product**. It is a high-fidelity, interactive demo built to impress angel investors at a conference. It simulates the OptiPlan Hub Dashboard — the central command center ("בור הפיקוד") for infrastructure projects.

**Goals:**
- Show the platform's holistic vision: all project dimensions (engineering, administrative, financial) in one place
- Demonstrate depth — not just surface cards, but flip interactions, expanded views, real KPIs
- Feel premium and polished — this is the investor's first impression
- Work flawlessly on an iPad with touch interactions

**What it must NOT be:**
- A wireframe or prototype feel — it should feel like a real product
- Shallow/disabled — every interaction should lead somewhere real (with fictitious data)
- Cluttered — clean, confident, minimal. "The product explains itself."

**Audience context:** Angel investors, most non-technical. They need to feel the product's power through visual clarity, not through technical jargon. The dashboard should tell a story: "this is what control looks like."

---

## 2. Fictitious Project Definition

The dashboard displays a single fictitious infrastructure project. All data is invented but must feel realistic and internally consistent.

### Project Details

| Field | Value |
|-------|-------|
| **Project Name** | הרחבת קו המטרו — קטע גוש דן (Metro Line Extension — Gush Dan Segment) |
| **Type** | Public transit infrastructure |
| **Client** | נת"ע — נתיבי תחבורה עירוניים (NTA — Metropolitan Mass Transit System) |
| **Budget (Estimate)** | ₪1.8 מיליארד (₪1.8B) |
| **Duration** | 2024–2029 |
| **Length** | ~22 km |
| **Sections** | 6 sections (א1 through א6) |
| **Phases per section** | 3 phases: תכנון מוקדם (Early Planning), תכנון מפורט (Detailed Planning), ביצוע (Execution) |
| **Active team** | ~45 stakeholders across multiple firms |
| **Managing company** | שחר הנדסה בע"מ (Shachar Engineering Ltd.) |

### Why this project?
- Government client = aligns with V2 positioning (government as first market)
- Large scale = demonstrates enterprise value proposition
- Transit = recognizable, impressive, relatable to Israeli investors
- Multiple sections & phases = shows the matrix complexity OptiPlan solves

---

## 3. Interaction Model — 3 Layers

The dashboard has 3 layers of depth. Each layer is reached by user interaction (tap/click).

### Layer 1 — Project Overview (Landing Screen)

**What the investor sees first when the dashboard opens.**

**Top bar:**
- OptiPlan logo (left)
- Project name + client: "הרחבת קו המטרו — קטע גוש דן | נת"ע"
- Status badge: "פעיל" (Active) in green

**3 Hero Gauges** (speedometer/dial style, prominent):

| Gauge | Label (Hebrew) | Value | Visual |
|-------|----------------|-------|--------|
| Financial Health | בריאות כספית | 78% | Green zone |
| Coordination Completion | השלמת תיאומים | 64% | Yellow zone |
| Schedule Adherence | עמידה בלו"ז | 71% | Yellow-green zone |

**5 Module Cards** arranged in a row/grid below the gauges:

| Card | Module | Color | Icon Suggestion | Summary Stat |
|------|--------|-------|-----------------|--------------|
| 1 | OptiTrack | Blue `#3B82F6` | Shield / Clipboard-Check | 64% תיאומים מאושרים |
| 2 | OptiBiz | Purple `#8B5CF6` | Wallet / TrendingUp | תזרים נקי: ₪12.4M |
| 3 | OptiRisk | Red-Orange `#EF4444` / `#F97316` | AlertTriangle / ShieldAlert | 7 סיכונים פעילים |
| 4 | OptiDocs | Green `#22C55E` | FileText / CheckSquare | 23 משימות פתוחות |
| 5 | OptiGantt | Indigo `#6366F1` | Calendar / GitBranch | 71% בלו"ז |

Each card shows:
- Module name + icon
- Color-coded top border (3-4px, module color)
- One headline KPI number
- A mini sparkline or progress bar
- Touch hint (subtle arrow or "tap to explore" in Hebrew)

### Layer 2 — Card Flip (Tap on any module card)

**Interaction:** Tap a card → it flips (3D CSS flip animation, ~400ms) to reveal the back side.

**Back of card shows "middle-level insights":**
- 3-4 specific KPIs for that module (see Section 4 for each module's KPIs)
- Small trend indicators (arrows up/down/stable)
- A "למודול המלא →" (Go to full module) button at the bottom

**Example — OptiTrack back:**
- סה"כ אישורים: 347
- מאושרים: 223 (64%)
- ממתינים: 89
- התנגדויות: 12 (with red badge)
- ממוצע ימים בסטטוס: 14
- [למודול המלא →]

### Layer 3 — Full Module Expansion (Tap "Go to full module")

**Interaction:** The card "opens" / expands to fill the center of the screen. Other cards shrink/slide to the sides or bottom. Smooth expand animation (~500ms).

**What shows in expanded view:**
- Module-specific gauges, charts, tables
- Realistic depth — not placeholders
- Full KPI breakdown (see Section 4)
- A "← חזרה" (Back) button to collapse back to Layer 1

**Optional (nice-to-have):** A chat input bar at the bottom of the expanded view — labeled "שאל את הסוכן" (Ask the agent). If technically feasible, connect to Anthropic API for live responses. If not, show it as a styled input that displays a pre-written demo response on tap.

---

## 4. Module Definitions & KPIs

### 4.1 OptiTrack — בקרה ושליטה על תהליכי תיאום
**Color:** Blue `#3B82F6`
**V2 Description:** NOT just permits. Full coordination control — permits with infrastructure authorities, engineering coordination between planners and disciplines, traffic arrangements, licensing, and client controls.

#### Layer 2 KPIs (Card Back)
| KPI | Hebrew | Demo Value |
|-----|--------|------------|
| Total coordination items | סה"כ פריטי תיאום | 347 |
| Approved | מאושרים | 223 (64%) |
| Pending | ממתינים לתגובה | 89 |
| Objections | התנגדויות | 12 |
| Avg days in status | ממוצע ימים בסטטוס | 14 |

#### Layer 3 Expanded View
- **Permit matrix:** Grid of sections (א1-א6) × phases (3), with colored cells (green=approved, yellow=pending, red=objection, gray=not submitted)
- **Section progress bars:** Each section shows % completion across all authorities
- **Authority breakdown:** Top 5 authorities with most pending items (e.g., חברת החשמל — 8 pending, מקורות — 6 pending)
- **Days-in-status histogram:** Distribution of how long items sit in each status
- **Recent activity feed:** Last 5 status changes with timestamps

**Status colors for cells:**
| Status | Hebrew | Color |
|--------|--------|-------|
| Approved | מאושר | Green `#22C55E` |
| Pending | ממתין | Yellow `#F59E0B` |
| Objection | התנגדות | Red `#EF4444` |
| Submitted | הוגש | Blue `#3B82F6` |
| Not submitted | לא הוגש | Gray `#9CA3AF` |

---

### 4.2 OptiBiz — תכנון פיננסי-תקציבי
**Color:** Purple `#8B5CF6`
**V2 Description:** Expanded financial planning — cash flow for 12 months with plan vs. actual, client management, AI-assisted proposals, contracts & accounts, invoices, expense tracking, subcontractor management.

#### Layer 2 KPIs (Card Back)
| KPI | Hebrew | Demo Value |
|-----|--------|------------|
| Total income (YTD) | סך הכנסות | ₪142.3M |
| Total expenses (YTD) | סך הוצאות | ₪129.9M |
| Net cash flow | תזרים נקי | ₪12.4M |
| Open invoices | חשבוניות פתוחות | 8 |
| Budget utilization | ניצול תקציב | 34% |

#### Layer 3 Expanded View
- **12-month cash flow chart:** Bar chart (income green, expenses red) with net line (blue). Show plan vs. actual overlay
- **Monthly breakdown table:** Months as columns, rows for income categories and expense categories
- **Contract utilization gauge:** X% of total contract value utilized
- **Top 5 open invoices:** Table with amount, client, due date, status
- **Cumulative balance line:** Running total over 12 months

---

### 4.3 OptiRisk — ניהול סיכונים
**Color:** Red-Orange — use `#EF4444` as primary, `#F97316` as secondary
**V2 Description:** Risk register with scoring, trend analysis (improving/worsening/stable), mitigation task tracking, CSV import/export.

#### Layer 2 KPIs (Card Back)
| KPI | Hebrew | Demo Value |
|-----|--------|------------|
| Active risks | סיכונים פעילים | 7 |
| High severity | חומרה גבוהה | 2 |
| Improving trend | במגמת שיפור | 3 |
| Worsening trend | במגמת החמרה | 1 |
| Mitigation completion | השלמת הקלה | 68% |

#### Layer 3 Expanded View
- **Risk heat map:** 5×5 grid (probability × impact), with risk count per cell
- **Risk trend chart:** Line chart showing risk score over time (last 6 months), with arrows for trend direction
- **Top risks table:** Rank, description, severity, trend (↑↓→), owner, mitigation status
- **Risk classification pie chart:** By category (engineering, financial, regulatory, schedule, environmental)
- **Mitigation tasks list:** Task name, linked risk, status (proposed/in_process/completed), due date

**Trend indicators:**
| Trend | Hebrew | Icon | Color |
|-------|--------|------|-------|
| Rising | עלייה | ↑ ArrowUp | Red `#EF4444` |
| Declining | ירידה | ↓ ArrowDown | Green `#22C55E` |
| No change | ללא שינוי | → Minus | Gray `#9CA3AF` |

---

### 4.4 OptiDocs — ניהול מסמכים ומשימות
**Color:** Green `#22C55E`
**V2 Description:** Task management with AI email analysis, formal letters with reference coding and conversation threading, meeting summaries with automatic content extraction.

#### Layer 2 KPIs (Card Back)
| KPI | Hebrew | Demo Value |
|-----|--------|------------|
| Open tasks | משימות פתוחות | 23 |
| Overdue tasks | משימות באיחור | 5 |
| Completion rate | שיעור השלמה | 74% |
| Pending letters | מכתבים ממתינים | 4 |
| Last meeting summary | סיכום ישיבה אחרון | 19.2.26 |

#### Layer 3 Expanded View
- **Task distribution chart:** By status (open / in progress / completed / on hold) — horizontal bar chart
- **Priority breakdown:** By priority (urgent/high/medium/low) — colored badges with counts
- **Overdue tasks table:** Task name, assignee, due date, days overdue (red), priority
- **Recent meeting summaries:** Last 3 meetings — date, title, number of extracted action items
- **Letters log:** Reference code, subject, recipient, status (draft/sent/archived), date

---

### 4.5 OptiGantt — תזמון פרויקטים
**Color:** Indigo `#6366F1`
**V2 Description:** Project scheduling with MS Project import, multiple dependency types, critical path display, hierarchical task structure (WBS).

#### Layer 2 KPIs (Card Back)
| KPI | Hebrew | Demo Value |
|-----|--------|------------|
| On-schedule tasks | משימות בלו"ז | 71% |
| Critical path items | פריטים בנתיב קריטי | 14 |
| Milestones achieved | אבני דרך שהושלמו | 8/12 |
| Average slack (days) | ממוצע פנוי (ימים) | 6.2 |
| Next milestone | אבן דרך הבאה | 28.2.26 |

#### Layer 3 Expanded View
- **Simplified Gantt view:** Horizontal bars for top-level tasks (not full WBS — keep it readable). Critical path highlighted in red. Milestones as diamonds.
- **Section timeline:** 6 sections (א1-א6) with their current phase status
- **Milestone tracker:** Timeline with diamonds — completed (filled), upcoming (outline), overdue (red)
- **Schedule health donut:** % on time / behind / ahead
- **Dependency alert list:** Tasks at risk due to predecessor delays

---

### 4.6 Hub Dashboard — Top-Level Aggregation

The Hub is **Layer 1 itself** — it IS the landing screen with the 3 gauges and 5 module cards. No separate "Hub module" card needed. The Hub is the frame that holds everything.

**Additional Hub elements (on Layer 1):**
- **AI Agent activity indicator:** A subtle pulse/dot near the logo — "3 סוכנים פעילים" (3 agents active). Hovering/tapping shows: "סוכן מייל מנתח 2 הודעות חדשות" (Email agent analyzing 2 new messages), etc. This demonstrates AI-native DNA without overwhelming.
- **Last updated timestamp:** "עודכן לפני 2 דקות" (Updated 2 minutes ago) — reinforces real-time feel.
- **Quick stats ribbon** (optional, below gauges): "45 בעלי עניין | 6 קטעים | 64 רשויות" (45 stakeholders | 6 sections | 64 authorities)

---

## 5. Design System

### 5.1 Aesthetic Direction

**"Calvin Klein more than Ralph Lauren."** Clean, minimal, confident. Not ornate. Let the data speak.

From the founders' meeting (18.2.26):
- "The product explains itself" — Apple-like simplicity
- "Power to the hands — you don't need to be an expert"
- "5 wow integrations are better than 500 shallow ones"

**Principles:**
- Strong typographic hierarchy, generous whitespace
- Borders over shadows (1px solid, not drop shadows)
- Flat colors, no gradients (except subtle gauge fills)
- Data should breathe — extra padding on charts and tables
- Premium but not flashy — confident restraint
- No rounded corners > 6px, no floating blobs, no emoji as design elements

### 5.2 Color Palette

#### Base Palette (from design spec)

| Token | Hex | Usage |
|-------|-----|-------|
| `--navy` | `#1B365D` | Headers, primary text, top bar background |
| `--steel` | `#4A86B8` | Subheaders, interactive accents, links |
| `--sky` | `#89C4E8` | Section dividers, chart fills |
| `--ice` | `#D6EAF5` | Card backgrounds, hover states, table alternating rows |
| `--white` | `#FFFFFF` | Page background, card surfaces |
| `--text` | `#2C3E50` | Body text |
| `--text-light` | `#5D6D7E` | Captions, secondary text, timestamps |
| `--success` | `#27AE60` | Positive indicators |
| `--warning` | `#F39C12` | Attention items |
| `--danger` | `#E74C3C` | Risks, blockers |

#### Module Colors (chosen based on founder guidance + Tailwind palette)

| Module | Primary Hex | Usage |
|--------|------------|-------|
| OptiTrack | `#3B82F6` (blue-500) | Card border-top, header accents, charts |
| OptiBiz | `#8B5CF6` (violet-500) | Card border-top, header accents, charts |
| OptiRisk | `#EF4444` (red-500) | Card border-top, risk indicators |
| OptiDocs | `#22C55E` (green-500) | Card border-top, completion indicators |
| OptiGantt | `#6366F1` (indigo-500) | Card border-top, schedule charts |

Each module card has a 3-4px colored top border. Background stays white. Color is an accent, not overwhelming.

### 5.3 Typography

```html
<link href="https://fonts.googleapis.com/css2?family=Heebo:wght@300;400;500;700;900&display=swap" rel="stylesheet">
```

Use **Heebo** — a modern Hebrew-first sans-serif that works well for both Hebrew and numbers. This aligns with the Calvin Klein direction better than Playfair Display (which is more Ralph Lauren).

| Element | Weight | Size | Color |
|---------|--------|------|-------|
| Page title | 900 | 2rem | `--navy` |
| Module name | 700 | 1.25rem | `--navy` |
| KPI value (big number) | 700 | 2.5rem | `--navy` |
| KPI label | 500 | 0.8rem, uppercase, letter-spacing 1px | `--text-light` |
| Body text | 400 | 1rem | `--text` |
| Caption/timestamp | 300 | 0.85rem | `--text-light` |

### 5.4 Component Patterns

**Module Card (Layer 1):**
```
┌─────────────────────────┐
│ ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄ │  ← 3px module-color top border
│  [Icon]  OptiTrack      │
│                         │
│     64%                 │
│  תיאומים מאושרים        │
│  ═══════════░░░░        │  ← progress bar in module color
│                         │
│         לחץ לפרטים →    │
└─────────────────────────┘
```

**Gauge (Hero):**
Semi-circular speedometer dial. The filled arc uses gradient from danger→warning→success based on value. Center shows the big number. Below: Hebrew label.

**Status Badge:**
Small pill, uppercase, bold. Background color per status. White text. 2px border-radius.

**Flip Card Animation:**
```css
.card { perspective: 1000px; }
.card-inner { transition: transform 0.4s ease; transform-style: preserve-3d; }
.card.flipped .card-inner { transform: rotateY(180deg); }
.card-front, .card-back { backface-visibility: hidden; }
.card-back { transform: rotateY(180deg); }
```

**Expand Animation:**
When "Go to full module" is tapped, the card scales/transitions to fill ~80% of the viewport. Other cards animate to small thumbnails at the bottom. Use CSS transitions + JS class toggling.

### 5.5 Layout

- **Direction:** RTL (right-to-left)
- **Max width:** 1200px, centered
- **Top bar:** Full width, navy background, logo left (in RTL: right), project name center
- **Gauges row:** 3 gauges, evenly spaced
- **Module cards:** 5 cards in a responsive grid (3+2 on iPad landscape, or 2+2+1)
- **Expanded module:** Centered, ~80% width, ~85% height, with scroll if needed
- **Padding:** 24px on iPad, generous card internal padding (24-32px)
- **Card gap:** 16-20px

### 5.6 Logo

**URL:** `https://github.com/OptiPlanLab/OptiPlan-Vision/blob/main/LOGO-ICON.png?raw=true`

Place in the top bar, ~40px height. If the URL is unreachable, fall back to a text logo: "OptiPlan" in Heebo 900 weight.

---

## 6. Tooltip & UX Copy Philosophy

### The Rule: "Screaming the pain without saying it"

Tooltips show value without blaming the past. They appear for ~5 seconds on tap (touch) or hover (desktop).

**Pattern:**
- ✅ "כאן תוכלו לנהל [capability] ללא צורך ב[old way]"
  (Here you can manage [X] without needing [old painful way])
- ❌ "עד היום הייתם צריכים..." (Until today you had to...)

### Tooltip Content Per Module

| Module | Tooltip (Hebrew) | Translation |
|--------|-------------------|-------------|
| OptiTrack | "כל התיאומים מול 64 רשויות — במטריצה אחת, עם היסטוריה אוטומטית" | All coordination across 64 authorities — in one matrix, with automatic history |
| OptiBiz | "תזרים מזומנים ל-12 חודשים, מסונכרן אוטומטית מאבני דרך בפרויקט" | 12-month cash flow, auto-synced from project milestones |
| OptiRisk | "סיכונים מקושרים ללו"ז ולתקציב — שינוי באחד מתגלגל בכל המערכת" | Risks linked to schedule and budget — a change in one cascades through the system |
| OptiDocs | "סיכומי ישיבות הופכים למשימות באופן אוטומטי. שום דבר לא נופל בין הכיסאות" | Meeting summaries become tasks automatically. Nothing falls through the cracks |
| OptiGantt | "נתיב קריטי, תלויות ואבני דרך — הכל מחובר לתיאומים ולתזרים" | Critical path, dependencies and milestones — all connected to coordination and cash flow |

### Gauge Tooltips
| Gauge | Tooltip |
|-------|---------|
| Financial Health | "בריאות כספית: תכנית מול ביצוע, על בסיס אבני דרך וחשבוניות" |
| Coordination | "אחוז תיאומים שהושלמו מול סה"כ פריטים נדרשים — כל הרשויות, כל הקטעים" |
| Schedule | "עמידה בלו"ז על בסיס אבני דרך ונתיב קריטי" |

### AI Agent Tooltip
When tapping the "3 agents active" indicator:
"סוכני AI פועלים ברקע — מנתחים מיילים, מחלצים משימות מסיכומי ישיבות, ומעדכנים סטטוסים אוטומטית"
(AI agents operate in the background — analyzing emails, extracting tasks from meeting summaries, and updating statuses automatically)

---

## 7. Architecture Visualization

### The Cascade — Optional Visual Element

If there's room (perhaps in an "about" or "info" panel accessible from Layer 1), include a simplified architecture diagram:

```
         ┌──────────────────┐
         │   Hub Dashboard  │
         │  (מרכז הפיקוד)   │
         └────────┬─────────┘
                  │
    ┌─────┬───────┼───────┬─────┐
    │     │       │       │     │
┌───┴──┐┌─┴──┐┌──┴──┐┌──┴──┐┌─┴───┐
│Track ││Docs ││Risk ││Gantt││ Biz │
└───┬──┘└──┬─┘└──┬──┘└──┬──┘└──┬──┘
    │      │     │      │      │
    └──────┴─────┴──────┴──────┘
         Shared Data Layer
         + AI Agent Layer
```

**Key message:** "שינוי באחד — וההשפעה מתגלגלת בכל המערכת בזמן אמת"
(A change in one — and the impact cascades across the entire system in real-time)

### Three Pillars Label
- **הנדסי (Engineering):** OptiTrack, OptiRisk, OptiGantt
- **מנהלי (Administrative):** OptiDocs
- **כספי (Financial):** OptiBiz

---

## 8. Technical Requirements

### Must-Have
- **Single HTML file** (or HTML + 1 CSS + 1 JS max) — GitHub Pages deployable
- **RTL direction** throughout
- **iPad landscape touch-optimized** — min touch targets 44×44px, no hover-dependent interactions (hover = bonus, tap = primary)
- **Responsive** — works on iPad Pro 12.9" (1024×1366) and iPad 10.9" (820×1180)
- **Smooth animations:** Card flip (400ms ease), card expand (500ms ease), gauge fill on load (800ms ease-out)
- **All data hardcoded** — no API calls needed for data (everything is fictitious)
- **Google Fonts loaded** — Heebo
- **No build tools** — vanilla HTML/CSS/JS, no React/Vue/npm. Must work by opening the file in a browser.

### Nice-to-Have
- **Chat input** at bottom of expanded module view — connected to Anthropic API (`claude-sonnet-4-20250514`). If not feasible, show a styled input that on tap displays a pre-written Hebrew response demonstrating AI agent capability. Example response: "על בסיס ניתוח 3 פרויקטים דומים, עיכוב ממוצע מול חברת החשמל עומד על 42 יום. מומלץ להגיש בקשה מקדימה לקטע א4."
- **Subtle loading animation** on first open — gauges fill, cards fade in with stagger
- **Light particle/grid background** — very subtle, not distracting (optional, only if it doesn't affect performance)

### Don'ts
- No dark mode
- No language toggle (Hebrew only for the conference)
- No login/auth screens
- No actual backend or database
- No disabled/grayed-out features — everything that's shown should work (with fake data)

---

## 9. Data Appendix — Authorities & Enums

### Israeli Infrastructure Authorities (sample for permit matrix)

Use a realistic subset of ~15 authorities for the demo permit matrix (from the full list of 64):

| Category | Authority (Hebrew) | Authority (English) |
|----------|-------------------|-------------------|
| חשמל | חברת החשמל לישראל | Israel Electric Corp |
| מים | מקורות | Mekorot |
| מים | תאגיד מים עירוני | Municipal Water Corp |
| תקשורת | בזק | Bezeq |
| תקשורת | סלקום | Cellcom |
| תחבורה | נתיבי ישראל | Netivei Israel |
| תחבורה | רכבת ישראל | Israel Railways |
| גז | נתיב הגז | Nativ HaGaz |
| עירייה | עיריית תל אביב — כבישים ותאורה | Tel Aviv Municipality — Roads |
| עירייה | עיריית תל אביב — תנועה | Tel Aviv Municipality — Traffic |
| עירייה | עיריית רמת גן — תכנון | Ramat Gan — Planning |
| סביבה | המשרד להגנת הסביבה | Environmental Protection |
| ממשלה | רשות העתיקות | Antiquities Authority |
| ממשלה | רשות מקרקעי ישראל | Israel Land Authority |
| ניקוז | רשות ניקוז | Drainage Authority |

### Permit Status Enums

| Status | Hebrew | Color |
|--------|--------|-------|
| not_submitted | לא הוגש | Gray |
| submitted | הוגש | Blue |
| waiting_response | ממתין לתגובה | Yellow |
| response_received | התקבלה תגובה | Light Blue |
| objection_received | התנגדות | Red |
| approved | מאושר | Green |
| approved_no_infrastructure | מאושר — אין תשתית | Green (lighter) |
| closed_reopened | נסגר ונפתח מחדש | Orange |

### Task Status Enums

| Status | Hebrew |
|--------|--------|
| open | פתוח |
| in_progress | בביצוע |
| completed | הושלם |
| on_hold | מושהה |
| deferred | נדחה |

### Task Priority Enums

| Priority | Hebrew | Color |
|----------|--------|-------|
| urgent | דחוף | Red |
| high | גבוה | Orange |
| medium | בינוני | Yellow |
| low | נמוך | Gray |

### Risk Status Enums

| Status | Hebrew |
|--------|--------|
| active | פעיל |
| closed | נסגר |
| monitored | במעקב |

---

## Builder Notes

### What to reference from V2 brief (attached separately)
- **Section 1:** Executive summary — for understanding the overall narrative
- **Section 4:** "The Solution" — module table for accurate descriptions
- **Section 4:** "The Cascade" — for the interconnection narrative
- **Section 4:** "Open Door" — email integration philosophy
- **Section 5:** Differentiators — for tooltip and badge content
- **Section 8:** Business model — for any pricing/model references
- **Section 12:** Vision — AI Agent Ecosystem for the agent indicator

### V2 Terminology Rules (critical)
- Always "מודולים" (modules), NEVER "מוצרים" (products)
- OptiTrack = "בקרה ושליטה על תהליכי תיאום" (NOT "just permits")
- No specific count of modules — say "מודולים מתמחים" if needed
- AI agents = DNA of the platform, not a feature added later
- Email integration = platform-level capability ("מחברים את המייל לכל המערכות"), not specific to one system

### NOW vs Roadmap
The dashboard simulates the **V1 (NOW)** product. Everything shown should be something that either exists today or is being built for V1. Do NOT show predictive intelligence, cross-project pattern recognition, or international features — those are V2+.

What IS V1: Module dashboards, permit tracking, task management, risk register, Gantt, cash flow, AI extraction from emails/meetings, the cascade (basic cross-module linking).

---

*This document was compiled by Plank on 22 February 2026. It integrates specifications from the orchestrator briefing, the OPIUM design spec, repo data extraction, and the V2 product brief — filtered for currency and consistency.*
