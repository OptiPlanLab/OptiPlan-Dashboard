# OptiPlan Dashboard — Fix List Work Plan

> **How to work:** For each batch, open a **fresh context window** in Claude Code,
> run `/bmad-bmm-quick-dev`, and paste the text from the **"Paste this"** section.
> After each batch — verify in the browser, then check off and move to the next.
>
> **Project structure:** Everything is in a single file: `index.html` (5,707 lines).
> CSS is inline (lines 22–2416), HTML markup (lines 2418–2704), JS (lines 2730–end).
>
> Logo file: `user-data/LOGO-ICON.png`

---

## Batch 1 — RTL & Table Fixes (E1, E2, E3)

**Why first:** Structural layout fixes affect everything else. Fix the foundation first.

- [x] **Done**

**Paste this into Quick Dev:**

```
Fix RTL table rendering across 3 modules in index.html (single-file SPA, ~5700 lines).

E1. OptiTrack — Fix RTL Table Display
- The permits/coordination table is not rendering correctly for RTL
- All columns, headers, and cell content must be properly right-aligned
- Text direction must be RTL throughout
- Look for the OptiTrack module overlay section and its CSS

E2. OptiRisk — Fix Table Overlapping Text
- The risk table currently covers/overlaps text content
- Stretch the table to accommodate Hebrew text
- All content must be readable and properly spaced in RTL layout
- Look for the OptiRisk module section

E3. OptiGantt — Same RTL Table Fix
- Apply the same RTL and table stretching fixes as OptiTrack and OptiRisk
- Gantt chart elements must respect RTL direction

All CSS is inline in <style> tag (lines 22-2416). Search for SECTION comments
like "OptiTrack Module", "OptiRisk Module", "OptiGantt Module" to find relevant CSS.
JS that builds these tables is after line 2730 in the OptiPlan namespace.
The app is RTL Hebrew (html dir="rtl" lang="he").
```

**Verify:** Open each module overlay (OptiTrack, OptiRisk, OptiGantt) and check that
tables render correctly with RTL text, no overlapping, proper spacing.

---

## Batch 2 — Layout & Card Sizing (C1, C2)

**Why second:** Card grid and font sizing changes will shift the entire visual layout.

- [x] **Done**

**Paste this into Quick Dev:**

```
Fix card grid layout and increase all text sizes in index.html (single-file SPA, ~5700 lines).

C1. Card Grid — Two Rows (3 + 2)
- Currently 5 module stat cards are in a single row
- Rearrange to 2 rows: 3 cards on top, 2 cards on bottom
- Cards must be SIGNIFICANTLY larger than current size
- Look for CSS SECTION "Stat Cards" and the .stat-cards container
- The cards use class .stat-card with flip animation

C2. Increase All Text Sizes — Globally
- This is an investor-facing display dashboard for trade shows — text is currently too small
- Increase font sizes for:
  - Module names on cards (card titles)
  - Icons on cards
  - Data/numbers displayed on cards (KPI values)
  - All body text, labels, and headers across EVERY screen
  - Sidebar text, topbar text, hero gauge labels
  - Module overlay content
- The cards should prominently showcase: module name, icon, and key data
- Consider increasing the base font-size in design tokens or root

All CSS is inline in the <style> tag. Design tokens start around line 50 with
CSS custom properties (--font-size-*, --space-*, etc).
The stat cards HTML is in the main content area with class .stat-cards.
```

**Verify:** Dashboard should show 3+2 card grid with noticeably larger text everywhere.
Check on 1024px width (the viewport meta is set to width=1024).

---

## Batch 3 — Header & Logo (B1)

- [x] **Done**

**Paste this into Quick Dev:**

```
Fix the top header/logo layout in index.html (single-file SPA, ~5700 lines, RTL Hebrew).

B1. Fix Top Header Layout
- RIGHT side (this is RTL, so visual right = start): Large OptiPlan logo image
- NEXT to the logo: Two lines of text:
  - Line 1: "OptiPlan" (or "אופטיפלאן")
  - Line 2: "מרכז בקרה" (Control Center)
- REMOVE the duplicate logo from the sidebar/navigation menu

Current state:
- Logo file is at: user-data/LOGO-ICON.png
- Sidebar has a brand section with: <img class="sidebar__brand-logo" src="user-data/LOGO-ICON.png">
- Topbar is 58px height, CSS section "Topbar" and "Topbar Content"
- Sidebar is 240px width, CSS section "Sidebar"

The logo should be prominent in the topbar with the two-line text beside it.
Remove the sidebar brand logo to avoid duplication.
```

**Verify:** Top header shows logo + "OptiPlan" / "מרכז בקרה" on the right side.
Sidebar should NOT have a separate logo anymore.

---

## Batch 4 — UX Fixes (D1, D2, D3)

- [x] **Done**

**Paste this into Quick Dev:**

```
Fix 3 UX issues in index.html (single-file SPA, ~5700 lines).

D1. Smooth Scrolling
- All scrollable areas must use consistent smooth scroll behavior
- Add scroll-behavior: smooth where appropriate
- Ensure scroll containers are properly styled (no janky or mismatched scrolling)
- Check module overlay scroll areas, sidebar, and any other scrollable containers

D2. Fix Light/Dark Toggle
- BUG: The toggle circle/thumb overflows outside the toggle track — fix sizing/positioning
- DEFAULT MODE: Light (בהיר) — dashboard must load in light mode by default
- Look for CSS section "Theme Toggle" and the JS theme initialization code
- The toggle likely uses a checkbox with a styled track and thumb/circle

D3. Fix Tooltip Duration
- CURRENT: Tooltip appears on click and disappears almost instantly
- REQUIRED: Tooltip appears on click and stays visible for 3 seconds, then fades out
- Look for tooltip JS code (after line 2730) — there's a tooltip component that
  reuses a single DOM element
- Find the timeout/duration value and change it to 3000ms
- Add a CSS fade-out transition for smooth disappearance

CSS is inline in <style> (lines 22-2416). JS is inline in <script> (line 2730+).
Search for "Theme Toggle", "Tooltip" in CSS sections and tooltip/theme in JS.
```

**Verify:**
- Scroll: smooth scrolling in module overlays and any scrollable area
- Toggle: circle stays inside track, dashboard loads in light mode by default
- Tooltip: click a card → tooltip stays 3 seconds → fades out

---

## Batch 5 — Content Tweaks (A1, A3)

- [x] **Done**

**Paste this into Quick Dev:**

```
Two content changes in index.html (single-file SPA, ~5700 lines, RTL Hebrew).

A1. Rename "Financial Health" → "Budget Progress"
- On the main dashboard, find the card/section labeled "בריאות פיננסית" (Financial Health)
- Rename it to "התקדמות תקציבית" (Budget Progress)
- This may appear in: stat card title, hero gauge label, module overlay title,
  sidebar navigation text, JS data constants, tooltip text
- Search for "בריאות פיננסית" and "Financial Health" across the entire file
- Replace ALL occurrences consistently

A3. OptiTrack — Add Column for Traffic Arrangements & Licensing
- In the OptiTrack module table, add a new column: "הסדרי תנועה ותהליכי רישוי"
  (Traffic Arrangements & Licensing Processes)
- This is IN ADDITION to the existing permits column — NOT a replacement
- Add sample data consistent with the existing OptiTrack data style
  (the project is a metro/rail expansion project in Israel)
- Look for OPTITRACK_DATA in the JS constants and the table rendering code

The data model uses Object.freeze() for immutability — you may need to
restructure the data definition to add the new field.
```

**Verify:**
- "בריאות פיננסית" no longer appears anywhere — replaced by "התקדמות תקציבית"
- OptiTrack table shows the new column with sample data

---

## Batch 6 — AI Agent Recommendations Section (A2)

**Why last:** This is a new feature addition — do it after all fixes are stable.

- [x] **Done**

**Paste this into Quick Dev:**

```
Add a new AI Agent Insights section to the main dashboard in index.html
(single-file SPA, ~5700 lines, RTL Hebrew, investor-facing display).

A2. Add AI Agent Recommendations Section
- Add a NEW section at the BOTTOM of the main dashboard screen (below the stat cards,
  above any footer or before closing the main content area)
- Section title: "תובנות סוכני AI" (AI Agent Insights)
- Display 3 agent cards side by side:

  1. סוכן סיכונים (Risk Agent)
     - Icon: ti-shield-check (or similar risk icon from Tabler Icons)
     - Color accent: #FF7A3C (orange, matching OptiRisk)
     - Sample insight: "זוהו 3 סיכונים חדשים בממשק עם רשות המים — נדרשת תשומת לב מיידית"

  2. סוכן פיננסי (Financial Agent)
     - Icon: ti-chart-bar (or similar finance icon from Tabler Icons)
     - Color accent: #F6AE2D (gold, matching OptiBiz)
     - Sample insight: "חריגה תקציבית של 2.3% צפויה ברבעון הבא — מומלץ לעדכן תחזית"

  3. סוכן תיאומים (Coordination Agent)
     - Icon: ti-arrows-exchange (or similar coordination icon from Tabler Icons)
     - Color accent: #0991F3 (blue, matching OptiTrack)
     - Sample insight: "4 משימות תיאום עם חברת החשמל נמצאות בעיכוב של מעל שבועיים"

Design guidelines:
- Cards should match the existing design language (rounded corners, shadows, glassmorphism)
- Use the project's existing design tokens (CSS custom properties)
- Text must be large and readable (this is a trade show display)
- RTL layout — cards should flow right-to-left
- Each card: icon at top, agent name, then insight text
- The section should have a subtle background to differentiate it from the cards above

The project uses Tabler Icons webfont (class="ti ti-icon-name"),
Varela Round font for UI, and JetBrains Mono for data.
CSS custom properties are defined in the "Design Tokens" section.
```

**Verify:** New section visible at bottom of dashboard with 3 agent cards,
each showing icon + name + Hebrew insight text. RTL layout, readable text.

---

## Post-Completion Checklist

After all 6 batches are done, do a final review:

- [x] All module overlays render RTL tables correctly
- [x] Card grid shows 3+2 layout with large, readable cards
- [x] Header has logo + "OptiPlan" / "מרכז בקרה", no duplicate in sidebar
- [x] Scrolling is smooth everywhere
- [x] Light/dark toggle works, loads in light mode
- [x] Tooltips stay 3 seconds
- [x] "התקדמות תקציבית" replaced everywhere
- [x] OptiTrack has traffic & licensing column
- [x] AI Agent Insights section at bottom of dashboard
- [ ] Test on iPad Safari (1024px) if possible
