# Story 4.2: Cross-Module Data Consistency Audit

Status: ready-for-dev

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As an investor (playing the Skeptic),
I want every number visible across the entire dashboard to cross-reference correctly,
so that I cannot find a single inconsistency that reveals this is a demo rather than the real product.

## Acceptance Criteria

1. **AC1 — OptiBiz Budget Cross-Reference:**
   **Given** all 5 module expansions are complete (Epics 1–3 done)
   **When** a developer performs a full data consistency audit
   **Then** `OPTIBIZ_DATA.budgetUtilized` (1,220,000,000) ÷ `PROJECT_DATA.totalBudget` (1,800,000,000) = 67.8%, displayed as 68% on both the stat card and the OptiBiz KPI row

2. **AC2 — OptiTrack Cross-Reference:**
   **Given** the data audit is in progress
   **When** OptiTrack values are cross-checked
   **Then** `OPTITRACK_DATA.totalItems` (347) equals the value displayed on the OptiTrack stat card front face
   **And** `OPTITRACK_DATA.approved` count (222) when divided by `totalItems` (347) equals the 64% approval rate shown on the card back and in the module expansion

3. **AC3 — OptiRisk Cross-Reference:**
   **Given** the data audit is in progress
   **When** OptiRisk values are cross-checked
   **Then** `OPTIRISK_DATA.activeRisks` (23) and `OPTIRISK_DATA.critical` (8) match the OptiRisk stat card values exactly
   **And** the sum of all risk counts in the heat map grid equals `OPTIRISK_DATA.activeRisks`

4. **AC4 — OptiDocs Cross-Reference:**
   **Given** the data audit is in progress
   **When** OptiDocs values are cross-checked
   **Then** `OPTIDOCS_DATA.total` (847) and `OPTIDOCS_DATA.completionRate` (94%) match the OptiDocs stat card values
   **And** `OPTIDOCS_DATA.approved` count (796) ÷ `OPTIDOCS_DATA.total` (847) equals 94% (within rounding)

5. **AC5 — OptiBiz Cash Flow Cross-Reference:**
   **Given** the data audit is in progress
   **When** OptiBiz cash flow is cross-checked
   **Then** the sum of all actual monthly values in `OPTIBIZ_DATA.cashFlow` array equals `OPTIBIZ_DATA.budgetUtilized` (1,220,000,000) within reasonable rounding
   **And** the 12-month plan total in the array reflects a realistic planned spend trajectory toward `PROJECT_DATA.totalBudget`

6. **AC6 — Single Source of Truth Enforcement:**
   **Given** all cross-references are verified
   **When** any discrepancy is found between a data constant and its display value
   **Then** the data constant is corrected to be the single source of truth — display values are always derived from constants, never independently hardcoded
   **And** cross-reference relationships are documented in adjacent code comments

## Tasks / Subtasks

- [ ] **Task 1: Audit data constants mathematical consistency** (AC: 1, 2, 3, 4, 5)
  - [ ] 1.1 Verify `OPTIBIZ_DATA.budgetUtilized` (1,220,000,000) ÷ `PROJECT_DATA.totalBudget` (1,800,000,000) = `OPTIBIZ_DATA.budgetUtilization` (68). Actual: 1220000000/1800000000 = 67.78% → displayed as 68% ✓ (acceptable rounding)
  - [ ] 1.2 Verify `OPTITRACK_DATA.approved` (222) ÷ `OPTITRACK_DATA.totalItems` (347) = `OPTITRACK_DATA.approvalRate` (64). Actual: 222/347 = 64.0% ✓
  - [ ] 1.3 Verify `OPTITRACK_DATA.totalItems` (347) = `approved` (222) + `pending` (35) + `objections` (12) + `inReview` (78). Sum: 222+35+12+78 = 347 ✓
  - [ ] 1.4 Verify `OPTIDOCS_DATA.approved` (796) ÷ `OPTIDOCS_DATA.total` (847) = `OPTIDOCS_DATA.completionRate` (94). Actual: 796/847 = 93.98% → displayed as 94% ✓
  - [ ] 1.5 Verify `OPTIDOCS_DATA.total` (847) = `approved` (796) + `pending` (38) + `overdue` (13). Sum: 796+38+13 = 847 ✓
  - [ ] 1.6 Sum all 12 `OPTIBIZ_DATA.cashFlow[].actual` values and verify = `OPTIBIZ_DATA.budgetUtilized` (1,220,000,000)
  - [ ] 1.7 Sum all 12 `OPTIBIZ_DATA.cashFlow[].plan` values and verify = `OPTIBIZ_DATA.budgetTotal` / `PROJECT_DATA.totalBudget` (1,800,000,000)
  - [ ] 1.8 Verify `OPTIRISK_DATA` heat map grid cell counts sum to `OPTIRISK_DATA.activeRisks` (23)
  - [ ] 1.9 Verify `PROJECT_DATA.sections` has 6 entries matching `OPTIGANTT_DATA.sections` (6 entries, same IDs a1-a6)
  - [ ] 1.10 Verify `PROJECT_DATA.sections[].progress` values match corresponding `OPTIGANTT_DATA.sections[].progress` values

- [ ] **Task 2: Fix orphaned hardcoded schedule gauge value** (AC: 6)
  - [ ] 2.1 At `HERO_GAUGE_CONFIG` (line ~3047), the schedule adherence gauge value is hardcoded `52`. This should be derived from data.
  - [ ] 2.2 **Option A (recommended):** Compute schedule adherence as the weighted average of all `OPTIGANTT_DATA.sections[].progress` values, resulting in approximately 47-52%. Store as a computed property or inline computation.
  - [ ] 2.3 **Option B:** Add an explicit `scheduleAdherence` field to `OPTIGANTT_DATA` or `PROJECT_DATA` and reference it: `value: OPTIGANTT_DATA.scheduleAdherence`
  - [ ] 2.4 Whichever approach is chosen, the gauge value must now derive from data constants, NOT be a magic number
  - [ ] 2.5 Add a comment explaining the derivation (e.g., `// Computed: average of OPTIGANTT_DATA.sections[].progress`)

- [ ] **Task 3: Fix hardcoded OptiTrack growth rate sublabel** (AC: 6)
  - [ ] 3.1 At stat card config (line ~3139), OptiTrack sublabel is `'+18%'` — a hardcoded string with no data backing
  - [ ] 3.2 **Option A (recommended):** Add a `growthRate` or `monthlyChange` field to `OPTITRACK_DATA` (e.g., `growthRate: 18`) and reference: `'+' + OPTITRACK_DATA.growthRate + '%'`
  - [ ] 3.3 **Option B:** Compute from activity feed data or trend data if available
  - [ ] 3.4 Whichever approach, the sublabel must now derive from a data constant

- [ ] **Task 4: Audit module expansion rendering vs data constants** (AC: 1, 2, 3, 4, 5)
  - [ ] 4.1 Open OptiTrack module overlay: verify metrics summary (347 items, 64% approved, 12 objections, 35 pending) all render from `OPTITRACK_DATA` — not hardcoded in render function
  - [ ] 4.2 Open OptiTrack permit matrix: verify section rows (A1-A6) and authority columns render from `OPTITRACK_DATA.permitMatrix` and `OPTITRACK_DATA.authorities`
  - [ ] 4.3 Open OptiBiz module overlay: verify ₪1.22B and 68% render from `OPTIBIZ_DATA.budgetUtilized` and `OPTIBIZ_DATA.budgetUtilization`
  - [ ] 4.4 Open OptiBiz cash flow chart: verify 12 bars render from `OPTIBIZ_DATA.cashFlow` array values, not hardcoded SVG coordinates
  - [ ] 4.5 Open OptiRisk module overlay: verify 23 active risks and 8 critical render from `OPTIRISK_DATA`
  - [ ] 4.6 Open OptiRisk heat map: verify cell counts render from `OPTIRISK_DATA.heatMap` grid data
  - [ ] 4.7 Open OptiDocs module overlay: verify 847 total, 94% completion, 38 pending, 13 overdue render from `OPTIDOCS_DATA`
  - [ ] 4.8 Open OptiGantt module overlay: verify 6 section rows render from `OPTIGANTT_DATA.sections`, milestones from `OPTIGANTT_DATA.milestones`
  - [ ] 4.9 For each module: trace the render path to confirm data flows from constant → render function → DOM (no intermediate hardcoding)

- [ ] **Task 5: Add cross-reference comments to data constants** (AC: 6)
  - [ ] 5.1 Add comment above `OPTIBIZ_DATA.budgetTotal`: `// Must equal PROJECT_DATA.totalBudget`
  - [ ] 5.2 Add comment above `OPTIBIZ_DATA.budgetUtilization`: `// Derived: budgetUtilized / budgetTotal * 100 (rounded)`
  - [ ] 5.3 Add comment above `OPTITRACK_DATA.approvalRate`: `// Derived: approved / totalItems * 100 (rounded)`
  - [ ] 5.4 Add comment above `OPTIDOCS_DATA.completionRate`: `// Derived: approved / total * 100 (rounded)`
  - [ ] 5.5 Add comment near `PROJECT_DATA.sections`: `// Section IDs and progress must match OPTIGANTT_DATA.sections`
  - [ ] 5.6 Add comment near schedule gauge config: explain how schedule adherence value is derived
  - [ ] 5.7 Add comment near stat card config for any sublabel derivations

- [ ] **Task 6: Verify tooltip data references (Story 4.1 integration)** (AC: 6)
  - [ ] 6.1 If Story 4.1 tooltips are implemented, verify tooltip copy numbers match data constants:
    - "₪1.8 מיליארד" → `PROJECT_DATA.totalBudget / 1000000000` = 1.8
    - "347 פריטים" → `OPTITRACK_DATA.totalItems` = 347
    - "5 רשויות" → `OPTITRACK_DATA.authorities.length` = 5
    - "23 סיכונים" → `OPTIRISK_DATA.activeRisks` = 23
    - "8 קריטיים" → `OPTIRISK_DATA.critical` = 8
    - "847 פריטים" → `OPTIDOCS_DATA.total` = 847
    - "94%" → `OPTIDOCS_DATA.completionRate` = 94
    - "6 קטעים" → `OPTIGANTT_DATA.sections.length` = 6
  - [ ] 6.2 If tooltip copy numbers don't match data constants, update `data-tooltip` attribute values to match

- [ ] **Task 7: Verification** (AC: 1, 2, 3, 4, 5, 6)
  - [ ] 7.1 Zero console errors on page load
  - [ ] 7.2 All 3 hero gauge values derive from data constants (financial=68, coordination=64, schedule=derived)
  - [ ] 7.3 All 5 stat card front face values derive from data constants
  - [ ] 7.4 All 5 stat card back face KPIs derive from data constants
  - [ ] 7.5 All 5 module expansion metrics derive from data constants
  - [ ] 7.6 OptiBiz cash flow bar heights match data constant values
  - [ ] 7.7 OptiRisk heat map cell counts sum to 23
  - [ ] 7.8 No hardcoded numeric display values remain in JS render functions (except formatting constants like decimal places)
  - [ ] 7.9 Cross-reference comments are present in data constants section
  - [ ] 7.10 Theme toggle works — all modules render correctly in both dark and light themes
  - [ ] 7.11 Card flip reveals correct KPI values (data-derived)
  - [ ] 7.12 Module expansion shows correct metrics (data-derived)
  - [ ] 7.13 No regressions: all animations, interactions, navigation, idle reset still function
  - [ ] 7.14 **Skeptic test:** Walk through the entire dashboard as an investor cross-checking every visible number against every other occurrence of that number — zero inconsistencies found

## Dev Notes

### Architecture Patterns & Constraints

**Single File Architecture:** ALL code lives in `index.html` (~5017 lines currently). No separate JS/CSS files. No new files.

**This story modifies EXISTING code only.** No new components, no new CSS sections, no new JS sections. This is a surgical audit-and-fix story.

**Data Constants Location:** Lines 2597-2726 in `index.html`. All constants are frozen via `Object.freeze()` at lines 2764-2780. Any new fields must be added BEFORE the freeze calls.

**Key Principle:** Data constants are the SINGLE SOURCE OF TRUTH. Display values MUST be derived from constants at render time. Never duplicate a number — derive it.

### Current Data Constant Structure (Reference)

```javascript
// Line ~2597
const PROJECT_DATA = {
  projectName: 'הרחבת קו מטרו, גוש דן',
  totalBudget: 1800000000,
  currency: 'NIS',
  sections: [/* 6 items: a1-a6 with progress values */]
};

// Line ~2616
const OPTITRACK_DATA = {
  totalItems: 347,
  approved: 222,
  approvalRate: 64,      // ← Must equal Math.round(approved/totalItems*100)
  pending: 35,
  objections: 12,
  inReview: 78,           // ← totalItems must equal approved+pending+objections+inReview
  authorities: [/* 5 arrays */],
  permitMatrix: [/* 6 arrays */],
  activityFeed: [/* 6 arrays */]
};

// Line ~2648
const OPTIBIZ_DATA = {
  budgetTotal: 1800000000,  // ← Must equal PROJECT_DATA.totalBudget
  budgetUtilized: 1220000000,
  budgetUtilization: 68,    // ← Must equal Math.round(budgetUtilized/budgetTotal*100)
  burnRate: 101666667,
  variance: -22000000,
  cashFlow: [/* 12 monthly objects */]
  // ← Sum of cashFlow[].actual must equal budgetUtilized
  // ← Sum of cashFlow[].plan must equal budgetTotal
};

// Line ~2671
const OPTIRISK_DATA = {
  activeRisks: 23,        // ← Must equal sum of all heatMap grid values
  critical: 8,
  heatMap: [/* 5x5 grid */],
  topRisks: [/* 6 risk objects */]
};

// Line ~2693
const OPTIDOCS_DATA = {
  total: 847,              // ← Must equal approved+pending+overdue
  approved: 796,
  completionRate: 94,      // ← Must equal Math.round(approved/total*100)
  pending: 38,
  overdue: 13
};

// Line ~2711
const OPTIGANTT_DATA = {
  sections: [/* 6 items — IDs and progress must match PROJECT_DATA.sections */],
  milestones: [/* 4 items */]
};
```

### Known Issues to Fix

| Issue | Location | Current Value | Fix |
|-------|----------|--------------|-----|
| Hardcoded schedule gauge | HERO_GAUGE_CONFIG line ~3047 | `value: 52` | Derive from OPTIGANTT_DATA |
| Hardcoded OptiTrack growth | Stat card config line ~3139 | `sublabel: '+18%'` | Add `growthRate` field to OPTITRACK_DATA |
| Cash flow sum verification | OPTIBIZ_DATA.cashFlow lines ~2656-2667 | 12 monthly objects | Verify sum(actual) = 1,220,000,000 |
| Heat map sum verification | OPTIRISK_DATA.heatMap lines ~2676-2682 | 5x5 grid | Verify sum(cells) = 23 |

### Stat Card Config Reference (Lines 3136-3222)

The stat card front and back values are configured in `STAT_CARD_CONFIG` object. Key rendering:

```javascript
// Front face values — MOSTLY derived ✓
optitrack: { value: OPTITRACK_DATA.approved, sublabel: '+18%' /* ← FIX */ }
optibiz:   { value: formatter(OPTIBIZ_DATA.budgetUtilized) }
optirisk:  { value: OPTIRISK_DATA.activeRisks, sublabel: OPTIRISK_DATA.critical + ' קריטיים' }
optidocs:  { value: OPTIDOCS_DATA.total, sublabel: OPTIDOCS_DATA.completionRate + '% הושלם' }
optigantt: { value: OPTIGANTT_DATA.sections.length, sublabel: computed-delayed-count }

// Back face KPIs — ALL derived ✓ (no fixes needed)
```

### Hero Gauge Config Reference (Lines 3045-3047)

```javascript
var HERO_GAUGE_CONFIG = [
  { key: 'financial',    value: OPTIBIZ_DATA.budgetUtilization, ... },  // ✓ Derived
  { key: 'coordination', value: OPTITRACK_DATA.approvalRate, ... },      // ✓ Derived
  { key: 'schedule',     value: 52, ... }  // ✗ HARDCODED — FIX THIS
];
```

### Module Render Functions (Reference)

Each module's `render(data)` function receives the corresponding `*_DATA` constant as input. The rendering chain is:

```
moduleOverlay.expand(moduleId)
  → OptiPlan.modules[moduleId].render(DATA_CONSTANT)
    → innerHTML generated from data parameter
    → DOM updated
```

This means if the data constant is correct, the module display should be correct. The audit verifies this chain end-to-end.

### Anti-Patterns (DO NOT)

1. NO new hardcoded numeric values — derive everything from data constants
2. NO changing visual appearance — this is a data audit, not a redesign
3. NO breaking existing animations, interactions, or navigation
4. NO creating new files — edit `index.html` only
5. NO adding `let`/`const` inside functions — use `var` (project convention)
6. NO removing `Object.freeze()` calls — add new fields BEFORE freeze
7. NO changing data values unless they are mathematically inconsistent
8. NO changing formatted display text unless it doesn't match its data source
9. NO adding console.log statements — use `console.warn('[OptiPlan Audit]', ...)` if needed
10. NO modifying any CSS — this story is JS/data only

### Previous Story Intelligence

**From Story 4.1 (Contextual Tooltips — previous story in this epic):**
- Codebase is ~5017 lines in index.html
- CSS sections follow strict `/* SECTION: */` delimiter pattern
- JS sections follow `// SECTION: //` delimiter pattern
- Tooltip `data-tooltip` attributes contain specific numbers that must match data constants
- All `var` declarations, named functions only
- `$` prefix for DOM references

**From Story 2.7 (OptiGantt — most recent module implementation):**
- OPTIGANTT_DATA structure confirmed: sections array with progress, milestones array
- Gantt timeline renders from data constants via `OptiPlan.modules.optigantt.render()`
- SVG bars computed from data values

**From Story 2.4 (OptiBiz — cash flow chart):**
- OptiBiz cash flow chart renders 12 bars from `OPTIBIZ_DATA.cashFlow` array
- Bar heights are computed from data values using SVG viewBox scaling
- Financial KPIs derive from `OPTIBIZ_DATA` properties

**From Story 2.5 (OptiRisk — heat map):**
- OptiRisk heat map renders from `OPTIRISK_DATA.heatMap` 5x5 grid
- Cell counts displayed alongside colors
- Top risks table renders from `OPTIRISK_DATA.topRisks`

### Git Intelligence

**Recent commits** all follow pattern: one story per commit modifying `index.html` + story `.md` file + `sprint-status.yaml`. Commit messages format: `Complete Story X.Y: {title}`.

**Code conventions confirmed across all commits:**
- `var` declarations (not `let`/`const` inside functions)
- Named handler functions
- `$` prefix for DOM references
- Section delimiters with `// ================================================================`
- `{ passive: true }` on touchstart listeners
- `Object.freeze()` on all data constants

### Data Consistency Cross-Reference Matrix

| Value | Source Constant | Displayed At | Card Front | Card Back | Module Expansion |
|-------|----------------|-------------|------------|-----------|-----------------|
| 1,800,000,000 (budget) | PROJECT_DATA.totalBudget | OptiBiz financial context | — | ✓ (via utilization %) | ✓ |
| 1,220,000,000 (utilized) | OPTIBIZ_DATA.budgetUtilized | OptiBiz stat card | ✓ (₪1.22B) | ✓ (₪1.22B) | ✓ |
| 68% (budget util) | OPTIBIZ_DATA.budgetUtilization | Hero gauge + OptiBiz | ✓ (hero) | ✓ | ✓ |
| 347 (total items) | OPTITRACK_DATA.totalItems | OptiTrack metrics | — | ✓ | ✓ |
| 222 (approved) | OPTITRACK_DATA.approved | OptiTrack stat card | ✓ | — | — |
| 64% (approval rate) | OPTITRACK_DATA.approvalRate | Hero gauge + OptiTrack | ✓ (hero) | ✓ | ✓ |
| 12 (objections) | OPTITRACK_DATA.objections | OptiTrack back/expansion | — | ✓ | ✓ |
| 35 (pending) | OPTITRACK_DATA.pending | OptiTrack back/expansion | — | ✓ | ✓ |
| 23 (active risks) | OPTIRISK_DATA.activeRisks | OptiRisk stat card | ✓ | ✓ | ✓ |
| 8 (critical) | OPTIRISK_DATA.critical | OptiRisk sublabel/back | ✓ (sublabel) | ✓ | ✓ |
| 847 (total docs) | OPTIDOCS_DATA.total | OptiDocs stat card | ✓ | ✓ | ✓ |
| 94% (completion) | OPTIDOCS_DATA.completionRate | OptiDocs sublabel/back | ✓ (sublabel) | ✓ | ✓ |
| 38 (pending docs) | OPTIDOCS_DATA.pending | OptiDocs back/expansion | — | ✓ | ✓ |
| 13 (overdue docs) | OPTIDOCS_DATA.overdue | OptiDocs back/expansion | — | ✓ | ✓ |
| 6 (sections) | PROJECT_DATA.sections.length | OptiGantt stat card | ✓ | ✓ | ✓ |
| 52 (schedule) | HARDCODED ✗ | Hero gauge | ✓ (hero) | — | — |
| +18% (growth) | HARDCODED ✗ | OptiTrack sublabel | ✓ (sublabel) | — | — |

### Project Structure Notes

- **Single file:** `index.html` is the entire application (~5017 lines)
- **Data constants:** Lines 2597-2726 (all `*_DATA` objects)
- **Object.freeze:** Lines 2764-2780 (freezes all constants)
- **Hero gauge config:** Line ~3045-3047
- **Stat card config:** Lines ~3136-3222
- **Module render functions:** Each under `OptiPlan.modules.*`
- **No build system:** What you write ships directly
- **iPad Safari 16.2+** is the only target browser

### Functional Requirements Coverage

- **FR39:** All data values are internally consistent across modules (numbers cross-reference correctly) — **PRIMARY FR for this story**
- **FR38:** All displayed data represents a realistic fictitious transit infrastructure project — data must be realistic and consistent

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Epic-4-Story-4.2]
- [Source: _bmad-output/planning-artifacts/architecture.md#Data-Architecture]
- [Source: _bmad-output/planning-artifacts/architecture.md#Implementation-Patterns]
- [Source: _bmad-output/planning-artifacts/architecture.md#Data-Flow]
- [Source: _bmad-output/planning-artifacts/prd.md#FR38-FR39]
- [Source: _bmad-output/implementation-artifacts/4-1-contextual-tooltips-with-hebrew-sales-copy.md — Tooltip data cross-references]
- [Source: index.html lines 2597-2726 — Data constants definitions]
- [Source: index.html lines 2764-2780 — Object.freeze() calls]
- [Source: index.html lines 3045-3047 — HERO_GAUGE_CONFIG]
- [Source: index.html lines 3136-3222 — STAT_CARD_CONFIG]

## Dev Agent Record

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
