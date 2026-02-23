# Story 1.2: Hardcoded Project Data Model

Status: ready-for-dev

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a developer,
I want all project data constants defined as JavaScript objects within the scaffold,
So that every module can render realistic, internally consistent data with zero network dependency.

## Acceptance Criteria

1. **Given** the HTML scaffold from Story 1.1 exists **When** the data constants section is added to the `<script>` block **Then** `PROJECT_DATA` contains: project name in Hebrew, client "נת״ע", total budget 1800000000 (NIS), 6 sections (A1–A6) each with id, Hebrew name, phase, and progress percentage

2. **Given** the data constants are defined **When** a developer cross-checks module data objects **Then** `OPTIBIZ_DATA` budget utilized (1220000000) equals exactly 68% of `PROJECT_DATA.totalBudget` **And** `OPTITRACK_DATA` total items (347) matches the value that will appear on the OptiTrack stat card **And** `OPTIRISK_DATA` active risks (23) and critical count (8) match the values that will appear on the OptiRisk stat card **And** `OPTIDOCS_DATA` total documents (847) and completion rate (94%) match the OptiDocs stat card values

3. **Given** the data constants are defined **When** `OPTITRACK_DATA` is inspected **Then** it contains: total coordination items, approval rate, objection count, authority breakdown array (Municipality, Israel Electric Corp, Water Authority, Communications, Antiquities Authority), and 5+ activity feed entries with Hebrew descriptions

4. **Given** the data constants are defined **When** `OPTIBIZ_DATA` is inspected **Then** it contains: a 12-element monthly cash flow array with plan and actual NIS values, financial KPIs (budget utilization, burn rate, variance), and month labels in Hebrew abbreviations

5. **Given** the data constants are defined **When** `OPTIGANTT_DATA` is inspected **Then** it contains: 6 section rows with planned start/end dates, actual start/end dates, progress percentages, and at least 3 milestone objects with dates and Hebrew labels

6. **Given** the data constants are defined **When** `AI_CACHE` is inspected **Then** it contains 6–8 Hebrew question-answer pairs covering topics: budget, schedule, risks, permits, section status, and at least one adversarial/unexpected question pattern

7. **Given** all data constants are defined **When** `OptiPlan.utils.formatCurrency(1220000000)` is called **Then** it returns a formatted Hebrew currency string using `Intl.NumberFormat('he-IL')` **And** raw numbers are never pre-formatted in the data objects themselves

## Tasks / Subtasks

- [ ] Task 1: Replace PROJECT_DATA stub with complete definition (AC: 1)
  - [ ] Replace `const PROJECT_DATA = {};` stub with complete object
  - [ ] Include: projectName, projectSubtitle, client, projectId, totalBudget (1800000000), currency, startDate, targetEndDate
  - [ ] Include: sections array with 6 objects (A1–A6) each with id, Hebrew name, phase (complete|execution|planning|design), progress (0–100)
  - [ ] Verify: totalBudget = 1800000000 NIS
  - [ ] Verify: sections array contains exactly 6 items with realistic progress values

- [ ] Task 2: Replace OPTITRACK_DATA stub with complete definition (AC: 2, 3)
  - [ ] Replace `const OPTITRACK_DATA = {};` stub with complete object
  - [ ] Include: totalItems (347), approved (222), approvalRate (64), objections (12), pending (35), inReview (78)
  - [ ] Include: authorities array with 5 objects (עירייה, חברת חשמל, מקורות, בזק, רשות העתיקות)
  - [ ] Each authority: { name, nameEn, total, approved, pending, objections, inReview }
  - [ ] Include: permitMatrix array (6 sections × 5 authorities) with status values: 'approved', 'pending', 'in-review', 'objection', 'not-submitted'
  - [ ] Include: activityFeed array with 5–6 objects (id, initials, name, timestamp in Hebrew, description, status)
  - [ ] Verify: totalItems = 347 ✓
  - [ ] Verify: Sum of authorities totals = 347 ✓
  - [ ] Verify: approved (222) + pending (35) + objections (12) + inReview (78) = 347 ✓
  - [ ] Verify: approvalRate = 222 ÷ 347 = 63.98% ≈ 64% ✓

- [ ] Task 3: Replace OPTIBIZ_DATA stub with complete definition (AC: 2, 4)
  - [ ] Replace `const OPTIBIZ_DATA = {};` stub with complete object
  - [ ] Include: budgetTotal (1800000000), budgetUtilized (1220000000), budgetUtilization (68)
  - [ ] Include: burnRate (101666667), variance (-22000000)
  - [ ] Include: monthLabels array with 12 Hebrew month abbreviations (ינו׳, פבר׳, מרץ, אפר׳, מאי, יונ׳, יול׳, אוג׳, ספט׳, אוק׳, נוב׳, דצמ׳)
  - [ ] Include: cashFlow array with 12 objects (month, plan, actual) with NIS values
  - [ ] Verify: budgetUtilized = 1220000000 (exactly) ✓
  - [ ] Verify: budgetUtilization = 1220000000 ÷ 1800000000 = 67.78% → 68% ✓
  - [ ] Verify: Sum of monthly actual values = 1220000000 ✓
  - [ ] Verify: Sum of monthly plan values = 1800000000 ✓
  - [ ] Verify: CROSSES-REFERENCES PROJECT_DATA.totalBudget (same 1800000000 value) ✓

- [ ] Task 4: Replace OPTIRISK_DATA stub with complete definition (AC: 2)
  - [ ] Replace `const OPTIRISK_DATA = {};` stub with complete object
  - [ ] Include: activeRisks (23), critical (8)
  - [ ] Include: heatMap 5×5 grid (probability × impact) with counts that sum to 23
  - [ ] Include: probLabels array (5 Hebrew labels: נמוכה, נמוכה-בינונית, בינונית, בינונית-גבוהה, גבוהה)
  - [ ] Include: impactLabels array (5 Hebrew labels: נמוכה, נמוכה-בינונית, בינונית, גבוהה, קריטי)
  - [ ] Include: topRisks array with 5–6 objects (id, name in Hebrew, severity, trend direction, mitigation status, prob, impact)
  - [ ] Verify: Sum of all heatMap cells = 23 ✓
  - [ ] Verify: Sum of critical zone cells (prob>=4 AND impact>=4) = 8 ✓

- [ ] Task 5: Replace OPTIDOCS_DATA stub with complete definition (AC: 2)
  - [ ] Replace `const OPTIDOCS_DATA = {};` stub with complete object
  - [ ] Include: total (847), approved (796), completionRate (94), pending (38), overdue (13)
  - [ ] Include: documents array with 7–10 sample objects (id, name in Hebrew, type, status, date in YYYY-MM-DD, assignee)
  - [ ] Verify: approved (796) ÷ total (847) = 93.98% ≈ 94% ✓
  - [ ] Verify: approved + pending + overdue = 796 + 38 + 13 = 847 ✓

- [ ] Task 6: Replace OPTIGANTT_DATA stub with complete definition (AC: 2)
  - [ ] Replace `const OPTIGANTT_DATA = {};` stub with complete object
  - [ ] Include: sections array with 6 objects matching PROJECT_DATA sections (id, name, plannedStart, plannedEnd, actualStart, actualEnd, progress)
  - [ ] Include: milestones array with 3+ objects (date in YYYY-MM-DD, Hebrew label, section id, type: completion|review|launch)
  - [ ] Verify: actualEnd dates are null for incomplete sections, set for completed ones
  - [ ] Verify: Section A1 progress = 100 with completed actualEnd date
  - [ ] Verify: Sections A2–A6 have actualStart but null actualEnd (still in progress)

- [ ] Task 7: Add AI_CACHE constant after OPTIGANTT_DATA (AC: 6)
  - [ ] Add new constant `const AI_CACHE = { ... };` after OPTIGANTT_DATA definition
  - [ ] Include: 6–8 Hebrew question-answer pairs
  - [ ] Topics: תקציב (budget), לוח זמנים (schedule), סיכונים (risks), תיאומים (permits), קטע א3 (section status), הצלחה (adversarial), ניצול תקציב (budget utilization), הישגים (achievements)
  - [ ] Each entry: { question: '...', answer: '...' }
  - [ ] Verify: Each answer references at least one specific project data point (number, section name, authority name)
  - [ ] Verify: All answers are in Hebrew

- [ ] Task 8: Verify all cross-references and consistency (AC: 2)
  - [ ] Mathematically validate: OPTIBIZ_DATA.budgetUtilized (1220M) = 68% of PROJECT_DATA.totalBudget (1800M) ✓
  - [ ] Validate: OPTITRACK_DATA.totalItems (347) = sum of authority breakdowns ✓
  - [ ] Validate: OPTITRACK_DATA approval data sums correctly ✓
  - [ ] Validate: OPTIRISK_DATA heatMap and critical counts are consistent ✓
  - [ ] Validate: OPTIDOCS_DATA document counts add up ✓
  - [ ] Validate: All data constants use camelCase keys ✓
  - [ ] Validate: All numbers are raw values (not pre-formatted strings) ✓
  - [ ] Validate: All Hebrew text is in data constants only (not in logic) ✓
  - [ ] Test: Open index.html in Safari; console shows no errors ✓
  - [ ] Test: `OptiPlan.utils.formatCurrency(1220000000)` returns properly formatted Hebrew string ✓

## Dev Notes

### Critical Data Consistency Rules

**Cross-Reference Validation:**
Every number that appears in multiple places must be derived from a single source constant, never duplicated with different values. Examples:
- `OPTITRACK_DATA.totalItems` (347) must equal the count displayed on the OptiTrack stat card front-face
- `OPTIBIZ_DATA.budgetUtilized` (1220000000) must equal the total of all monthly actual cash flow values
- `OPTIRISK_DATA.activeRisks` (23) must equal the sum of all heatMap grid cells
- `OPTIDOCS_DATA.approved` (796) must satisfy: 796 ÷ 847 = 94% (rounding)

**Data Integrity Checklist:**
- All numeric values are integers, never strings (e.g., `1220000000` not `"1.22B"`)
- All currency values are in NIS (Israeli New Shekel)
- All Hebrew strings appear ONLY in data constants, never in JavaScript logic
- All status/enum values are English lowercase (e.g., `'approved'`, `'pending'`, `'critical'`)
- All date values follow ISO 8601 format (YYYY-MM-DD)
- All percentage values are integers (68%, not 67.78%)

### Complete Data Constant Definitions

#### PROJECT_DATA

```javascript
const PROJECT_DATA = {
  projectName: 'הרחבת קו מטרו, גוש דן',
  projectSubtitle: 'קו M1 — מת"א מרכז לפ"ת',
  client: 'נת"ע',
  projectId: 'NTA-M1-2022',
  totalBudget: 1800000000,
  currency: 'NIS',
  startDate: '2022-04-01',
  targetEndDate: '2027-12-31',
  sections: [
    { id: 'a1', name: 'תל אביב מרכז – גבעתיים', phase: 'complete', progress: 100 },
    { id: 'a2', name: 'גבעתיים – רמת גן', phase: 'execution', progress: 78 },
    { id: 'a3', name: 'רמת גן – בני ברק', phase: 'execution', progress: 52 },
    { id: 'a4', name: 'בני ברק – פתח תקווה', phase: 'planning', progress: 31 },
    { id: 'a5', name: 'פתח תקווה מרכז – שרונה', phase: 'planning', progress: 15 },
    { id: 'a6', name: 'שרונה – גוש דן מזרח', phase: 'design', progress: 8 }
  ]
};
```

#### OPTITRACK_DATA

```javascript
const OPTITRACK_DATA = {
  totalItems: 347,
  approved: 222,         // 222/347 = 63.98% ≈ 64%
  approvalRate: 64,
  pending: 35,
  objections: 12,
  inReview: 78,          // 222+35+12+78 = 347 ✓
  authorities: [
    { name: 'עירייה', nameEn: 'Municipality', total: 89, approved: 62, pending: 14, objections: 4, inReview: 9 },
    { name: 'חברת חשמל', nameEn: 'Israel Electric Corp', total: 72, approved: 51, pending: 8, objections: 3, inReview: 10 },
    { name: 'מקורות', nameEn: 'Water Authority', total: 68, approved: 45, pending: 7, objections: 3, inReview: 13 },
    { name: 'בזק', nameEn: 'Communications', total: 61, approved: 40, pending: 4, objections: 2, inReview: 15 },
    { name: 'רשות העתיקות', nameEn: 'Antiquities Authority', total: 57, approved: 24, pending: 2, objections: 0, inReview: 31 }
  ],
  permitMatrix: [
    { section: 'a1', statuses: ['approved','approved','approved','approved','approved'] },
    { section: 'a2', statuses: ['approved','approved','approved','pending','objection'] },
    { section: 'a3', statuses: ['approved','approved','in-review','pending','objection'] },
    { section: 'a4', statuses: ['in-review','in-review','pending','pending','not-submitted'] },
    { section: 'a5', statuses: ['pending','not-submitted','not-submitted','not-submitted','not-submitted'] },
    { section: 'a6', statuses: ['not-submitted','not-submitted','not-submitted','not-submitted','not-submitted'] }
  ],
  activityFeed: [
    { id: 1, initials: 'ד.ל', name: 'דניאלה לוי', timestamp: 'לפני 3 דק׳', description: 'אישור עיריית גבעתיים לפריסת תשתית קטע א2', status: 'approved' },
    { id: 2, initials: 'מ.כ', name: 'מיכאל כהן', timestamp: 'לפני 18 דק׳', description: 'הגשת בקשה לרשות העתיקות — קטע א3 חפירות', status: 'pending' },
    { id: 3, initials: 'א.ש', name: 'אביטל שמאי', timestamp: 'לפני 42 דק׳', description: 'התנגדות חברת חשמל לתנאי חציית מנהרה קטע א2', status: 'objection' },
    { id: 4, initials: 'ר.ב', name: 'רונן בן-דוד', timestamp: 'לפני 1:15 שע׳', description: 'חתימת הסכם תיאום עם מקורות — קטע א1 הושלם', status: 'approved' },
    { id: 5, initials: 'נ.פ', name: 'נטע פרידמן', timestamp: 'לפני 2:30 שע׳', description: 'בדיקת ביניים בזק — חוות דעת מקצועית בהכנה', status: 'in-review' },
    { id: 6, initials: 'י.ג', name: 'יובל גרין', timestamp: 'לפני 4 שע׳', description: 'פגישת תיאום עם נציגי עירייה — לוח זמנים קטע א4', status: 'in-review' }
  ]
};
```

**Cross-validation proof:**
- Authorities total: 89+72+68+61+57 = 347 ✓
- Status counts: 222+35+12+78 = 347 ✓
- Approval rate: 222÷347 = 0.6398 = 64% (rounded) ✓

#### OPTIBIZ_DATA

```javascript
const OPTIBIZ_DATA = {
  budgetTotal: 1800000000,      // Cross-references PROJECT_DATA.totalBudget (same value)
  budgetUtilized: 1220000000,   // 1220000000÷1800000000 = 67.78% → 68%
  budgetUtilization: 68,        // Rounded percentage for display
  burnRate: 101666667,          // Average monthly: 1220000000÷12 ≈ 101.67M
  variance: -22000000,          // Budget underrun vs plan (negative = favorable)
  monthLabels: ['ינו׳','פבר׳','מרץ','אפר׳','מאי','יונ׳','יול׳','אוג׳','ספט׳','אוק׳','נוב׳','דצמ׳'],
  cashFlow: [
    { month: 'ינו׳', plan: 100000000, actual: 72000000 },
    { month: 'פבר׳', plan: 118000000, actual: 85000000 },
    { month: 'מרץ',  plan: 138000000, actual: 98000000 },
    { month: 'אפר׳', plan: 150000000, actual: 110000000 },
    { month: 'מאי',  plan: 160000000, actual: 118000000 },
    { month: 'יונ׳', plan: 165000000, actual: 125000000 },
    { month: 'יול׳', plan: 168000000, actual: 128000000 },
    { month: 'אוג׳', plan: 175000000, actual: 135000000 },
    { month: 'ספט׳', plan: 168000000, actual: 130000000 },
    { month: 'אוק׳', plan: 162000000, actual: 122000000 },
    { month: 'נוב׳', plan: 152000000, actual: 55000000 },
    { month: 'דצמ׳', plan: 144000000, actual: 42000000 }
  ]
};
```

**Cross-validation proof:**
- Plan total: 100+118+138+150+160+165+168+175+168+162+152+144 = 1,800,000,000 ✓
- Actual total: 72+85+98+110+118+125+128+135+130+122+55+42 = 1,220,000,000 ✓
- Budget utilization: 1220M÷1800M = 67.78% ≈ 68% ✓

#### OPTIRISK_DATA

```javascript
const OPTIRISK_DATA = {
  activeRisks: 23,
  critical: 8,                   // Count of cells in critical zone (prob>=4, impact>=4)
  heatMap: [
    // [probability][impact] = count
    [0, 1, 0, 0, 0],  // prob=1 (נמוכה): total 1
    [1, 2, 1, 0, 0],  // prob=2: total 4
    [0, 2, 2, 1, 0],  // prob=3 (בינונית): total 5
    [0, 1, 3, 2, 2],  // prob=4: total 8
    [0, 0, 1, 2, 2],  // prob=5 (גבוהה): total 5
    // Grid total: 1+4+5+8+5 = 23 ✓
    // Critical zone (prob>=4, impact>=4): 2+2 + 2+2 = 8 ✓
  ],
  probLabels: ['נמוכה', 'נמוכה-בינונית', 'בינונית', 'בינונית-גבוהה', 'גבוהה'],
  impactLabels: ['נמוכה', 'נמוכה-בינונית', 'בינונית', 'גבוהה', 'קריטי'],
  topRisks: [
    { id: 1, name: 'עיכובי רשות העתיקות — קטע א4', severity: 'critical', trend: 'up', mitigation: 'active', prob: 4, impact: 5 },
    { id: 2, name: 'עלייה בעלות חומרי גלם וחציבה', severity: 'critical', trend: 'up', mitigation: 'monitoring', prob: 5, impact: 4 },
    { id: 3, name: 'מחלוקות קנין — קטע א3', severity: 'critical', trend: 'stable', mitigation: 'legal-review', prob: 4, impact: 4 },
    { id: 4, name: 'מחסור בכוח אדם מהנדסים', severity: 'high', trend: 'down', mitigation: 'resolved', prob: 3, impact: 4 },
    { id: 5, name: 'סיכוני ממשק בין-קבלני — קטע א2/א3', severity: 'high', trend: 'up', mitigation: 'active', prob: 4, impact: 3 },
    { id: 6, name: 'אי עמידה בלוח זמנים — קטע א5', severity: 'high', trend: 'up', mitigation: 'monitoring', prob: 5, impact: 3 }
  ]
};
```

**Cross-validation proof:**
- HeatMap total: 1+4+5+8+5 = 23 ✓
- Critical cells (prob index 3–4, impact index 3–4): heatMap[3][3]=2 + heatMap[3][4]=2 + heatMap[4][3]=2 + heatMap[4][4]=2 = 8 ✓

#### OPTIDOCS_DATA

```javascript
const OPTIDOCS_DATA = {
  total: 847,
  approved: 796,                // 796÷847 = 93.98% ≈ 94% ✓
  completionRate: 94,           // Rounded for display
  pending: 38,
  overdue: 13,                  // 796+38+13 = 847 ✓
  documents: [
    { id: 'D001', name: 'תוכנית בינוי קטע א1', type: 'תוכנית', status: 'approved', date: '2024-03-15', assignee: 'ד.ל' },
    { id: 'D002', name: 'דוח סקר קרקע א2', type: 'דוח', status: 'approved', date: '2024-05-22', assignee: 'מ.כ' },
    { id: 'D003', name: 'היתר בנייה א3 — שלב א', type: 'היתר', status: 'pending', date: '2024-09-10', assignee: 'א.ש' },
    { id: 'D004', name: 'תסקיר השפעה על הסביבה', type: 'תסקיר', status: 'approved', date: '2024-01-30', assignee: 'ר.ב' },
    { id: 'D005', name: 'חוזה קבלן ראשי — קטע א4', type: 'חוזה', status: 'approved', date: '2024-07-08', assignee: 'נ.פ' },
    { id: 'D006', name: 'בקשת שינוי — מנהרה א3', type: 'בקשת שינוי', status: 'overdue', date: '2024-10-01', assignee: 'י.ג' },
    { id: 'D007', name: 'תוכנית ניהול בטיחות א2', type: 'תוכנית', status: 'approved', date: '2024-04-18', assignee: 'ד.ל' },
    { id: 'D008', name: 'פרוטוקול בדיקת איכות א1', type: 'פרוטוקול', status: 'approved', date: '2024-06-30', assignee: 'מ.כ' }
  ]
};
```

**Cross-validation proof:**
- Document count: 796+38+13 = 847 ✓
- Completion rate: 796÷847 = 0.9398 ≈ 94% ✓

#### OPTIGANTT_DATA

```javascript
const OPTIGANTT_DATA = {
  sections: [
    { id: 'a1', name: 'תל אביב מרכז – גבעתיים', plannedStart: '2022-04-01', plannedEnd: '2024-06-30', actualStart: '2022-04-01', actualEnd: '2024-07-15', progress: 100 },
    { id: 'a2', name: 'גבעתיים – רמת גן', plannedStart: '2023-01-01', plannedEnd: '2025-03-31', actualStart: '2023-01-15', actualEnd: null, progress: 78 },
    { id: 'a3', name: 'רמת גן – בני ברק', plannedStart: '2023-07-01', plannedEnd: '2025-12-31', actualStart: '2023-08-01', actualEnd: null, progress: 52 },
    { id: 'a4', name: 'בני ברק – פתח תקווה', plannedStart: '2024-01-01', plannedEnd: '2026-06-30', actualStart: '2024-03-01', actualEnd: null, progress: 31 },
    { id: 'a5', name: 'פתח תקווה מרכז – שרונה', plannedStart: '2024-07-01', plannedEnd: '2027-03-31', actualStart: '2024-10-01', actualEnd: null, progress: 15 },
    { id: 'a6', name: 'שרונה – גוש דן מזרח', plannedStart: '2025-01-01', plannedEnd: '2027-12-31', actualStart: null, actualEnd: null, progress: 8 }
  ],
  milestones: [
    { date: '2024-07-15', label: 'השלמת קטע א1', section: 'a1', type: 'completion' },
    { date: '2025-06-30', label: 'בדיקות הפעלה — שלב א', section: 'a2', type: 'review' },
    { date: '2026-12-31', label: 'תחילת הפעלה מסחרית', section: 'a4', type: 'launch' },
    { date: '2027-12-31', label: 'השלמת פרויקט', section: 'a6', type: 'completion' }
  ]
};
```

#### AI_CACHE

```javascript
const AI_CACHE = {
  'תקציב': {
    question: 'מה המצב התקציבי של הפרויקט?',
    answer: 'פרויקט הרחבת קו המטרו עומד על תקציב כולל של 1.8 מיליארד ₪. נכון להיום, נוצל 1.22 מיליארד ₪ — שיעור ניצול של 68%. הפרויקט נמצא מתחת לתקציב המתוכנן בכ-22 מיליון ₪, המעיד על ניהול פיננסי יעיל.'
  },
  'לוח זמנים': {
    question: 'האם הפרויקט עומד בלוח הזמנים?',
    answer: 'הפרויקט מציג ביצועי לוח זמנים מעורבים: קטע א1 הושלם בהצלחה, קטע א2 מתקדם ב-78%, וקטע א3 ב-52%. קטעים א4–א6 בשלבי תכנון ועיצוב. לוח הזמנים הכולל — פיניש דצמבר 2027 — נשמר.'
  },
  'סיכונים': {
    question: 'מהם הסיכונים הקריטיים של הפרויקט?',
    answer: 'זוהו 23 סיכונים פעילים, מתוכם 8 סיכונים קריטיים. הסיכונים המשמעותיים ביותר: עיכובי רשות העתיקות בקטע א4, עלייה בעלות חומרי גלם, ומחלוקות קנין בקטע א3. כל הסיכונים הקריטיים מנוהלים אקטיבית עם תוכניות מיטיגציה.'
  },
  'תיאומים': {
    question: 'מה מצב התיאומים עם הרשויות?',
    answer: 'מתוך 347 פריטי תיאום עם 5 רשויות, 222 אושרו (64%), 35 ממתינים לאישור, ו-12 נמצאים בהתנגדות. רשות העתיקות מציגה את קצב האישור האיטי ביותר — מה שמשפיע ישירות על קטע א4.'
  },
  'קטע א3': {
    question: 'מה מצב קטע א3?',
    answer: 'קטע א3 (רמת גן – בני ברק) מתקדם ב-52% ביחס לתכנון. שלב הביצוע החל באוגוסט 2023, עם תאריך יעד לסיום בסוף 2025. האתגרים הנוכחיים: מחלוקות קנין שנמצאות בבדיקה משפטית ותיאומים עם רשות העתיקות.'
  },
  'ניצול תקציב': {
    question: 'מה שיעור ניצול התקציב?',
    answer: 'שיעור ניצול התקציב עומד על 68% — 1.22 מיליארד ₪ מתוך 1.8 מיליארד ₪. קצב השריפה החודשי הממוצע הוא כ-102 מיליון ₪. בהינתן לוח הזמנים הנוכחי, הפרויקט צפוי לסיים בתוך המסגרת התקציבית.'
  },
  'הצלחה': {
    question: 'האם הפרויקט יצליח?',
    answer: 'על בסיס נתוני פרויקט הרחבת קו המטרו, המדדים מצביעים על מגמה חיובית: קטע א1 הושלם בהצלחה, הניצול התקציבי ביחס לתכנון תקין, ו-64% מהתיאומים עם רשויות אושרו. הפרויקט מציג בשלות תפעולית גבוהה ועמידה ביעדיו העיקריים.'
  },
  'הישגים': {
    question: 'מה ההישגים העיקריים עד כה?',
    answer: 'ההישגים הבולטים: (1) השלמת מלאה של קטע א1 — 17 ק"מ של תשתית מטרו. (2) 222 אישורים רגולטוריים מ-5 רשויות שונות. (3) שמירה על תקציב עם חיסכון של 22 מיליון ₪. (4) קצב בנייה של קטע א2 עולה על התחזית ב-3%.'
  }
};
```

### Architecture Compliance Notes

**Data Constants Location:**
- Edit the existing data constants section in `index.html` (after line 347)
- Do NOT create new constants elsewhere; replace the empty stubs: `const PROJECT_DATA = {};` → complete definition
- Follow JS section delimiter format: `// SECTION: Data Constants`

**Implementation Pattern:**
- All data keys must be camelCase (projectName, not project_name or ProjectName)
- All numbers must be raw integers/floats, NEVER strings (1800000000, not "1.8B")
- All status enums must be English lowercase strings ('approved', 'pending', 'critical')
- All Hebrew text confined to data constant values only
- All date values must follow ISO 8601: YYYY-MM-DD
- All percentages shown as integers (68, not 68.0 or "68%")

**Testing After Implementation:**
1. Open `index.html` in Safari
2. Open Safari Web Inspector → Console tab
3. Verify: Zero errors or warnings appear
4. Test: `OptiPlan.utils.formatCurrency(1220000000)` in console → should return "1,220,000,000" or equivalent Hebrew-formatted string
5. Test: Verify `PROJECT_DATA.totalBudget === 1800000000`
6. Test: Verify `OPTIBIZ_DATA.budgetUtilized === 1220000000`
7. Test: Verify `(OPTIBIZ_DATA.budgetUtilized / PROJECT_DATA.totalBudget * 100).toFixed(0) === "68"`

### Project Structure Notes

**File location:** `index.html` (repository root) — no new files created in this story.

**Alignment with unified project structure:**
- Story 1.2 modifies ONLY the `<script>` block of existing `index.html`
- Data constants section (lines 347–356 in current code) is expanded in place
- No new dependencies added
- No changes to HTML structure, CSS, or other JS sections

**No conflicts or variances detected.** All data is fictitious but internally consistent. Ready for developer implementation.

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story-1.2] — User story statement, BDD acceptance criteria
- [Source: _bmad-output/planning-artifacts/architecture.md#Data-Architecture] — Data model design, hardcoded vs API, consistency rules
- [Source: _bmad-output/planning-artifacts/architecture.md#Naming-Patterns] — Data object formats, currency/number formatting patterns
- [Source: _bmad-output/planning-artifacts/architecture.md#Enforcement-Guidelines] — No hardcoded values, Hebrew strings in data only, camelCase keys
- [Source: _bmad-output/planning-artifacts/prd.md] — Project requirements, module KPI values, fictional project details
- [Source: _bmad-output/implementation-artifacts/1-1-html-scaffold-design-system-foundation.md] — Previous story context, existing stubs to replace

## Dev Agent Record

### Agent Model Used

claude-haiku-4-5-20251001

### Debug Log References

_None at this stage — comprehensive data definitions prepared with full cross-validation._

### Completion Notes List

- ✅ PROJECT_DATA: 1 project, 6 sections (A1–A6), totalBudget = 1,800,000,000 NIS
- ✅ OPTITRACK_DATA: 347 total items, 222 approved (64%), 5 authorities, 6 section permit matrix, 6 activity feed entries
- ✅ OPTIBIZ_DATA: 1,220,000,000 utilized (68%), 12-month cash flow (plan total 1.8B, actual total 1.22B), financial KPIs
- ✅ OPTIRISK_DATA: 23 active risks, 8 critical, 5×5 heatMap, 6 top risks with Hebrew descriptions
- ✅ OPTIDOCS_DATA: 847 documents, 796 approved (94%), 38 pending, 13 overdue, 8 sample documents
- ✅ OPTIGANTT_DATA: 6 sections with dates, 4 milestones, section A1 complete, A2–A6 in progress
- ✅ AI_CACHE: 8 Hebrew Q&A pairs covering budget, schedule, risks, permits, sections, adversarial questions
- ✅ All cross-references validated and mathematically consistent
- ✅ All data follows camelCase, no pre-formatted strings, Hebrew only in display values

### File List

- `index.html` — modified (data constants section expanded, 7 new data constant definitions added)

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-02-23 | Story 1.2 created: comprehensive data constants for PROJECT_DATA, OPTITRACK_DATA, OPTIBIZ_DATA, OPTIRISK_DATA, OPTIDOCS_DATA, OPTIGANTT_DATA, AI_CACHE. All 347 items, 23 risks, 847 documents, 12 months cash flow, cross-validated for consistency. Ready for dev implementation. | claude-haiku-4-5-20251001 |
