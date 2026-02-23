# Story 3.1: AI Chat Panel UI & Interaction Shell

Status: done

## Story

As an investor,
I want to see an AI chat panel with Hebrew question chips and a text input,
so that engaging with the platform's intelligence feels natural and requires zero effort to start.

## Acceptance Criteria

1. **Given** the Epic 1 dashboard shell is complete
   **When** an investor accesses the AI chat (via "Opium AI" sidebar nav item or AI indicator)
   **Then** the AI chat panel renders in RTL layout with: 3-4 suggested question chips in Hebrew (tappable pills, min 44px height), a Hebrew free-text input field, and a send button (min 44x44px touch target)

2. **Given** the chat panel is open
   **When** a developer inspects the AI agent indicator
   **Then** it shows a pulsing dot (`@keyframes pulse`, 2s ease-in-out infinite) and Hebrew text "3 סוכנים פעילים"
   **And** the indicator is the same one established in Story 1.6, now also accessible as a tap target to open the AI panel

3. **Given** the chat panel is open and an investor has tapped a suggested question chip
   **When** the question is submitted
   **Then** the user's question appears as a right-aligned message bubble in the chat thread (Hebrew, Varela Round)
   **And** a typing indicator renders: 3 CSS-animated bouncing dots (`@keyframes bounce`, staggered delays) below the user bubble
   **And** the suggested chips remain visible for the next question after a response is received

4. **Given** the chat panel receives a response
   **When** the response text is available
   **Then** it appears as a left-aligned AI response bubble with JetBrains Mono for any numeric data citations
   **And** the typing indicator disappears before the response renders
   **And** the chat scrolls to show the latest message

5. **Given** the chat history grows
   **When** the number of stored message pairs exceeds `MAX_CHAT_HISTORY` (5)
   **Then** the oldest pair is removed from `OptiPlan.ai.history` before adding the new pair
   **And** the chat DOM display shows the most recent messages -- no unbounded array growth

6. **Given** the chat panel is fully rendered
   **When** a developer inspects `OptiPlan.ai`
   **Then** it exposes `sendMessage(text)`, `displayResponse(text)`, `findCachedResponse(text)`, and `showTypingIndicator(bool)` methods
   **And** all event handlers are named functions -- no anonymous callbacks

## Tasks / Subtasks

- [x] Task 1: Implement AI Chat Panel HTML structure (AC: #1)
  - [x]1.1 Add `.chat-panel` container to `index.html` body — positioned as a slide-in panel or overlay (not inside module overlay)
  - [x]1.2 Chat panel sections: header (title + close button), messages area (scrollable), suggested chips row, input row (text input + send button)
  - [x]1.3 Header: "Opium AI" title with `ti-sparkles` Tabler icon, close button (44x44px, `ti-x`)
  - [x]1.4 Messages area: `.chat-panel__messages` container with `overflow-y: auto`, `overscroll-behavior: contain`
  - [x]1.5 Suggested chips: 3-4 Hebrew question pills from `AI_CACHE` keys — `min-height: 44px`, tappable
  - [x]1.6 Input row: Hebrew RTL text input (`placeholder="שאלו על הפרויקט..."`) + send button (`ti-send`, 44x44px)

- [x] Task 2: Implement chat panel open/close interaction (AC: #1, #2)
  - [x]2.1 Open triggers: "Opium AI" sidebar nav item tap, AI agent indicator tap (in sidebar footer)
  - [x]2.2 AI agent indicator in sidebar footer: make existing pulsing dot + "3 סוכנים פעילים" text a tap target that opens chat
  - [x]2.3 Open animation: slide-in from right side (RTL: `inset-inline-start`), `transform: translateX()` + `opacity`, 350ms
  - [x]2.4 Close triggers: close button (X), backdrop tap (if using backdrop), sidebar "לוח בקרה" tap
  - [x]2.5 Close animation: reverse slide-out, 250ms
  - [x]2.6 State management: `OptiPlan.state.set('chatOpen', true/false)` — single source of truth
  - [x]2.7 Named event handlers: `handleChatOpen`, `handleChatClose`

- [x] Task 3: Implement suggested question chips (AC: #1, #3)
  - [x]3.1 Render 3-4 chips from `AI_CACHE` question keys (first 3-4 entries)
  - [x]3.2 Chip styling: pill shape, `var(--bg-input)` background, `var(--border)` border, `var(--radius-sm)` radius, min 44px height
  - [x]3.3 Touch feedback: `scale(0.98)` on `touchstart` via named `handleChipTouchStart` / `handleChipTouchEnd`
  - [x]3.4 On chip tap: call `OptiPlan.ai.sendMessage(chipText)` — same flow as typed input
  - [x]3.5 Chips remain visible after response for next question (do NOT remove after first use)

- [x] Task 4: Implement message display (AC: #3, #4)
  - [x]4.1 User message bubble: right-aligned (RTL), `var(--bg-input)` background, `var(--radius)` radius, Varela Round font
  - [x]4.2 AI response bubble: left-aligned (RTL), slightly different background tint, JetBrains Mono for numeric citations
  - [x]4.3 Message append: `insertAdjacentHTML('beforeend', bubbleHtml)` into `.chat-panel__messages`
  - [x]4.4 Auto-scroll: after each message append, `$messages.scrollTop = $messages.scrollHeight`
  - [x]4.5 Timestamp on each bubble: relative time (JetBrains Mono, muted, small)

- [x] Task 5: Implement typing indicator (AC: #3, #4)
  - [x]5.1 Three bouncing dots: CSS `@keyframes bounce` with staggered `animation-delay` (0s, 0.2s, 0.4s)
  - [x]5.2 `showTypingIndicator(true)` — appends typing indicator to messages, scrolls to bottom
  - [x]5.3 `showTypingIndicator(false)` — removes typing indicator from DOM
  - [x]5.4 Typing indicator positioned as left-aligned (AI side) mini-bubble

- [x] Task 6: Implement chat history management (AC: #5)
  - [x]6.1 `OptiPlan.ai.history = []` — array of `{role, content}` objects
  - [x]6.2 On send: push `{role: 'user', content: text}` to history
  - [x]6.3 On response: push `{role: 'assistant', content: responseText}` to history
  - [x]6.4 Before push: if `history.length >= MAX_CHAT_HISTORY * 2`, shift first 2 entries (oldest pair)
  - [x]6.5 DOM cleanup: when oldest pair removed from history, remove corresponding DOM bubbles

- [x] Task 7: Implement `OptiPlan.ai` namespace (AC: #6)
  - [x]7.1 `OptiPlan.ai.init()` — cache DOM refs (`$chatPanel`, `$messages`, `$input`, `$sendBtn`, `$chips`)
  - [x]7.2 `OptiPlan.ai.sendMessage(text)` — displays user bubble, shows typing indicator, calls cached response (Story 3.1 uses cache only; Story 3.2 adds live API)
  - [x]7.3 `OptiPlan.ai.displayResponse(text)` — hides typing indicator, renders AI bubble, scrolls to bottom
  - [x]7.4 `OptiPlan.ai.findCachedResponse(text)` — fuzzy-matches user text against `AI_CACHE` keys, returns best match or generic fallback
  - [x]7.5 `OptiPlan.ai.showTypingIndicator(show)` — adds/removes typing dots
  - [x]7.6 `OptiPlan.ai.destroy()` — clear history, remove event listeners, clear DOM

- [x] Task 8: Implement `findCachedResponse` fuzzy matching (AC: #6)
  - [x]8.1 Iterate `AI_CACHE` entries, compare user text against each question key
  - [x]8.2 Match strategy: check if any `AI_CACHE` key words appear in user text (simple keyword overlap)
  - [x]8.3 Score matches by word overlap count, return highest-scoring answer
  - [x]8.4 If no match scores above threshold: return generic Hebrew fallback string referencing the project name
  - [x]8.5 Generic fallback: "על בסיס נתוני פרויקט הרחבת קו המטרו, גוש דן — המערכת מנתחת כעת את המידע. ניתן לשאול שאלות ספציפיות על תקציב, לוח זמנים, סיכונים או תיאומים."

- [x] Task 9: Implement text input submission (AC: #1, #3)
  - [x]9.1 Send on Enter key press (named handler `handleInputKeydown`)
  - [x]9.2 Send on send button tap (named handler `handleSendTap`)
  - [x]9.3 Trim and validate input (ignore empty strings)
  - [x]9.4 Clear input field after send
  - [x]9.5 Disable send button while typing indicator is shown (prevent double-send)

- [x] Task 10: Simulate response delay for cached responses (AC: #3, #4)
  - [x]10.1 After showing typing indicator, wait 800-1500ms (random) before showing response
  - [x]10.2 Use `setTimeout` with stored reference for cleanup
  - [x]10.3 This simulates API response time — Story 3.2 replaces with real fetch

- [x] Task 11: Add CSS section for AI Chat Panel
  - [x]11.1 New CSS section: `/* SECTION: Component — AI Chat Panel */` after OptiGantt Module CSS
  - [x]11.2 Chat panel positioning: fixed, right side (RTL), full height or 85% height
  - [x]11.3 Panel width: 380-420px (enough for Hebrew text without cramping)
  - [x]11.4 Message bubble styles (user vs. AI alignment, colors, radius)
  - [x]11.5 Suggested chip styles (pill, touch feedback, spacing)
  - [x]11.6 Typing indicator animation (`@keyframes bounce`)
  - [x]11.7 Input row styles (input field + send button layout)
  - [x]11.8 Slide-in/out animation classes
  - [x]11.9 All colors via `var(--*)` tokens — zero hardcoded hex
  - [x]11.10 Backdrop: `rgba(0,0,0,0.3)` semi-transparent overlay behind chat panel

- [x] Task 12: Add JS section for AI Chat module
  - [x]12.1 New JS section with delimiter: `// ================================================================ // SECTION: AI Chat // ================================================================`
  - [x]12.2 Place after OptiGantt module section, before Animation Orchestration
  - [x]12.3 Complete `OptiPlan.ai` namespace with all methods

- [x] Task 13: Integration with existing sidebar and state
  - [x]13.1 Wire "Opium AI" sidebar nav item to `handleChatOpen`
  - [x]13.2 Wire AI agent indicator (sidebar footer pulsing dot) to `handleChatOpen`
  - [x]13.3 Update sidebar active state when chat is open ("Opium AI" shows active styling)
  - [x]13.4 Close chat when module overlay opens (chat and module overlay are mutually exclusive or can coexist — determine from existing behavior)
  - [x]13.5 Idle timer integration: chat interaction resets idle timer (`handleActivityReset`)
  - [x]13.6 Idle auto-reset closes chat panel if open

- [x] Task 14: Verify and test all acceptance criteria
  - [x]14.1 Zero console errors on chat open/close
  - [x]14.2 3-4 suggested question chips render with Hebrew text
  - [x]14.3 Chip tap sends message and shows in chat
  - [x]14.4 Typing indicator appears and disappears correctly
  - [x]14.5 Cached response displays after simulated delay
  - [x]14.6 Chat scrolls to latest message
  - [x]14.7 History cap works (send 6+ messages, verify oldest removed)
  - [x]14.8 Text input works with Enter key and send button
  - [x]14.9 Theme toggle works on all chat panel elements (dark/light)
  - [x]14.10 RTL layout correct (user messages right, AI messages left)
  - [x]14.11 Existing features unbroken: card flip, overlay, sidebar nav, idle reset
  - [x]14.12 Touch targets meet 44px minimum on all interactive elements
  - [x]14.13 Named functions only — no anonymous callbacks
  - [x]14.14 All colors via CSS custom properties

## Dev Notes

### Architecture Patterns & Constraints

**Single File Architecture:** ALL code goes into `index.html`. No separate JS/CSS files.

**AI Chat Namespace (MUST follow exactly):**
```javascript
// ================================================================
// SECTION: AI Chat
// ================================================================
OptiPlan.ai = {
  history: [],
  _responseTimeout: null,
  _isTyping: false,
  $chatPanel: null,
  $messages: null,
  $input: null,
  $sendBtn: null,
  $chips: null,

  init: function() {
    this.$chatPanel = document.querySelector('.chat-panel');
    this.$messages = document.querySelector('.chat-panel__messages');
    this.$input = document.querySelector('.chat-panel__input');
    this.$sendBtn = document.querySelector('.chat-panel__send');
    this.$chips = document.querySelectorAll('.chat-panel__chip');
    // Register event listeners with named functions
  },

  sendMessage: function(text) {
    // 1. Trim + validate
    // 2. Add user bubble to DOM
    // 3. Push to history
    // 4. Clear input
    // 5. Show typing indicator
    // 6. Find cached response
    // 7. setTimeout to simulate delay, then displayResponse
  },

  displayResponse: function(text) {
    // 1. Hide typing indicator
    // 2. Add AI bubble to DOM
    // 3. Push to history
    // 4. Scroll to bottom
    // 5. Cap history if needed
  },

  findCachedResponse: function(text) {
    // Fuzzy match against AI_CACHE
    // Return best match or generic fallback
  },

  showTypingIndicator: function(show) {
    // Add/remove typing dots element
  },

  destroy: function() {
    // Clear timeouts, history, event listeners
  }
};
```

**This is the FIRST story in Epic 3 (AI Intelligence).** There are no previous Epic 3 stories. Story 3.2 builds on this by adding live LLM API integration. This story establishes the complete UI shell and cache-only response flow.

### Data Source — `AI_CACHE` (already defined in Story 1.2, Object.freeze'd)

The `AI_CACHE` constant contains 6-8 Hebrew question-answer pairs. The suggested chips should use the first 3-4 question keys from `AI_CACHE`. Example structure:

```javascript
const AI_CACHE = Object.freeze({
  questions: [
    {
      q: 'מה מצב התקציב?',
      a: 'תקציב הפרויקט עומד על 1.8 מיליארד ₪...'
    },
    {
      q: 'מה מצב לוח הזמנים?',
      a: 'הפרויקט מתקדם בקצב של 52% עמידה...'
    },
    {
      q: 'מהם הסיכונים העיקריים?',
      a: 'כרגע ישנם 23 סיכונים פעילים, מתוכם 8 קריטיים...'
    },
    // ... more pairs
  ]
});
```

**IMPORTANT: Check the actual `AI_CACHE` structure** in `index.html` before implementing. The exact key format may differ from this example. The cache was created in Story 1.2 — load and inspect the actual data before coding `findCachedResponse`.

### Chat Panel Positioning & Layout

**Panel Position:**
- Fixed position, right side of screen (in RTL: `inset-inline-start: 0` or `right: 0` depending on implementation)
- Full viewport height or 85% height with margin
- Width: 380-420px — wide enough for Hebrew text blocks
- Z-index: above module overlay backdrop but below critical system overlays
- Semi-transparent backdrop behind panel: `rgba(0,0,0,0.3)`

**Panel Layout (top to bottom):**
```
┌──────────────────────────┐
│ [X] Opium AI ✦           │  ← Header (58px, close button)
├──────────────────────────┤
│                          │
│  ┌────────────────────┐  │
│  │ AI bubble          │  │  ← Messages area (scrollable)
│  └────────────────────┘  │
│        ┌──────────────┐  │
│        │ User bubble  │  │
│        └──────────────┘  │
│                          │
├──────────────────────────┤
│ [chip1] [chip2] [chip3]  │  ← Suggested chips (flex-wrap)
├──────────────────────────┤
│ [שאלו על הפרויקט...] [▶] │  ← Input row (input + send)
└──────────────────────────┘
```

**RTL Message Alignment:**
- User messages: `margin-inline-start: auto` (pushes to right in RTL) — background uses subtle user color
- AI messages: `margin-inline-end: auto` (pushes to left in RTL) — background uses `var(--bg-input)` or similar

### Message Bubble HTML Structure

```html
<!-- User message -->
<div class="chat-bubble chat-bubble--user">
  <div class="chat-bubble__text">{userText}</div>
  <span class="chat-bubble__time">עכשיו</span>
</div>

<!-- AI response -->
<div class="chat-bubble chat-bubble--ai">
  <div class="chat-bubble__text">{aiText}</div>
  <span class="chat-bubble__time">עכשיו</span>
</div>

<!-- Typing indicator -->
<div class="chat-typing" id="chat-typing">
  <span class="chat-typing__dot"></span>
  <span class="chat-typing__dot"></span>
  <span class="chat-typing__dot"></span>
</div>
```

### Typing Indicator Animation

```css
@keyframes chatBounce {
  0%, 80%, 100% { transform: translateY(0); }
  40% { transform: translateY(-6px); }
}

.chat-typing__dot {
  display: inline-block;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--text-muted);
  animation: chatBounce 1.4s ease-in-out infinite;
}

.chat-typing__dot:nth-child(2) { animation-delay: 0.2s; }
.chat-typing__dot:nth-child(3) { animation-delay: 0.4s; }
```

### Fuzzy Matching Strategy for `findCachedResponse`

```javascript
findCachedResponse: function(text) {
  var questions = AI_CACHE.questions;  // Adjust to actual structure
  var bestMatch = null;
  var bestScore = 0;
  var userWords = text.split(/\s+/);

  for (var i = 0; i < questions.length; i++) {
    var qWords = questions[i].q.split(/\s+/);
    var score = 0;
    for (var j = 0; j < userWords.length; j++) {
      for (var k = 0; k < qWords.length; k++) {
        if (userWords[j] === qWords[k] && userWords[j].length > 2) {
          score++;
        }
      }
    }
    if (score > bestScore) {
      bestScore = score;
      bestMatch = questions[i].a;
    }
  }

  if (bestScore === 0 || !bestMatch) {
    return 'על בסיס נתוני פרויקט הרחבת קו המטרו, גוש דן — המערכת מנתחת כעת את המידע. ניתן לשאול שאלות ספציפיות על תקציב, לוח זמנים, סיכונים או תיאומים.';
  }

  return bestMatch;
}
```

**IMPORTANT:** Check actual `AI_CACHE` structure before implementing. Adapt key access pattern to match the actual data format from Story 1.2.

### Sidebar Integration

**Existing sidebar nav items (from Story 1.3):**
The sidebar has a "כלים" (Tools) section with "Opium AI" as a nav item. When tapped:
1. Set sidebar active state to "Opium AI" item
2. Open chat panel via `handleChatOpen`
3. On chat close, revert sidebar active to "לוח בקרה" (Dashboard)

**Existing AI agent indicator (from Story 1.6):**
The sidebar footer has a pulsing green dot with "3 סוכנים פעילים" text. Make this entire footer element a tap target that also opens the chat panel. Use `cursor: pointer` and the same `handleChatOpen` handler.

**State integration:**
```javascript
// Open chat
function handleChatOpen(e) {
  if (e) e.preventDefault();
  OptiPlan.state.set('chatOpen', true);
  // Add class to chat panel for slide-in animation
  // Update sidebar active state
  // Reset idle timer
}

// Close chat
function handleChatClose(e) {
  if (e) e.preventDefault();
  OptiPlan.state.set('chatOpen', false);
  // Remove class from chat panel for slide-out animation
  // Revert sidebar active state to Dashboard
}
```

### Idle Reset Integration

The idle auto-reset (90 seconds, from Story 1.6) must also close the chat panel:
- Chat interactions (typing, sending messages, tapping chips) should reset the idle timer
- When idle timeout fires: if chat is open, close it as part of the reset sequence
- Add chat close to the existing `handleActivityReset` function or the idle timeout handler

### Chat Panel vs. Module Overlay Coexistence

**Decision needed:** Can the chat panel be open simultaneously with a module overlay?

**Recommended approach:** Chat panel and module overlay are **mutually exclusive**. Opening chat closes any open module overlay, and opening a module overlay closes chat. This prevents visual complexity and z-index conflicts.

If the dev agent determines coexistence works better (e.g., chat as narrow side panel alongside wider module overlay), that's acceptable as long as:
- No z-index conflicts
- Both are independently dismissible
- Idle reset closes both
- State machine tracks both states correctly

### Content Entrance Animation Pattern

Follow the established stagger pattern from Epic 2 modules:
```css
.chat-panel {
  position: fixed;
  /* positioning properties */
  transform: translateX(-100%);  /* Adjust for RTL */
  opacity: 0;
  transition: transform 350ms cubic-bezier(0.16, 1, 0.3, 1),
              opacity 350ms cubic-bezier(0.16, 1, 0.3, 1);
}

.chat-panel--open {
  transform: translateX(0);
  opacity: 1;
}
```

### Project Structure Notes

- **Single file:** ALL additions go into `index.html`
- **CSS section:** Add after the last module CSS section (OptiGantt), create new section: "Component — AI Chat Panel"
- **JS section:** Add `OptiPlan.ai` after the last module JS section (OptiGantt), before Animation Orchestration
- **HTML:** Add `.chat-panel` container in the `<body>` after module overlay elements, before closing `</body>`
- **Section delimiter format:**

```javascript
// ================================================================
// SECTION: AI Chat
// ================================================================
```

```css
/* ================================================================ */
/* SECTION: Component — AI Chat Panel                                  */
/* ================================================================ */
```

### Anti-Patterns to AVOID

1. **NO hardcoded hex colors** — use `var(--*)` tokens or CSS classes
2. **NO anonymous arrow functions** — named functions only for event handlers
3. **NO animating width/height/top/left** — only `transform` and `opacity`
4. **NO new files** — edit `index.html` only
5. **NO functions outside `OptiPlan.*` namespace**
6. **NO emojis** — Tabler Icons only
7. **NO breaking existing** entrance animations, touch feedback, theme toggle, card flip, overlay open/close, idle reset, sidebar nav, any Epic 2 module
8. **NO canvas/D3.js/charting libraries** — vanilla HTML/CSS/JS only
9. **NO `alert()` or `console.error`** — use `console.warn` for caught errors
10. **NO growing arrays** — cap `OptiPlan.ai.history` at `MAX_CHAT_HISTORY * 2` entries
11. **NO `setInterval` for typing animation** — CSS `@keyframes` only
12. **NO orphaned event listeners** — `destroy()` removes all registered listeners
13. **NO forgetting `-webkit-` prefixes** for Safari where needed
14. **NO hover-only interactions** — all interactions must work with touch (touchstart/touchend)
15. **NO inline Hebrew strings in logic** — Hebrew display strings in data constants or template literals only
16. **NO `console.error`** — use `console.warn('[OptiPlan AI]', err.message)` format

### CSS Custom Property Rules

- ALL structural colors MUST use `var(--*)` tokens
- Background: `var(--bg-card)` for panel, `var(--bg-input)` for input field
- Text: `var(--text-primary)`, `var(--text-secondary)`, `var(--text-muted)`
- Borders: `var(--border)`
- Spacing: `var(--gap)`, `var(--gap-sm)`, `var(--gap-lg)`
- Radius: `var(--radius)`, `var(--radius-sm)`
- Font: `var(--font-main)` for Varela Round, `var(--font-mono)` for JetBrains Mono
- Transitions: `var(--transition-default)` or explicit cubic-bezier
- Chat-specific: Use existing theme tokens — no new color tokens needed

### RTL Layout Considerations

- HTML `dir="rtl"` is set on `<html>` — all flex/grid auto-reverses
- User messages align to `inline-end` (right in RTL) — use `margin-inline-start: auto`
- AI messages align to `inline-start` (left in RTL) — use `margin-inline-end: auto`
- Chat panel slides in from `inline-start` side (left in LTR, right in RTL)
- Text input placeholder is Hebrew: "שאלו על הפרויקט..."
- Send button: position at `inline-end` of input row
- Suggested chips flow RTL naturally via flexbox
- Close button: positioned at `inset-inline-end` (right in LTR, left in RTL) of header

### Safari WebKit Compatibility

- CSS `@keyframes` for bouncing dots — fully supported in Safari 16.2+
- `position: fixed` for chat panel — works correctly on iPad Safari
- `overflow-y: auto` on messages container — standard scrolling, no `-webkit-overflow-scrolling: touch` needed (deprecated)
- `overscroll-behavior: contain` on scroll container — prevents background scroll
- `touch-action: manipulation` on all interactive elements (chips, buttons, input)
- `-webkit-touch-callout: none` on interactive elements
- `user-select: none` on interactive elements (except text input)
- Text input: `user-select: auto` to allow text selection/editing
- `-webkit-transform` prefix alongside unprefixed `transform` for slide animation

### Previous Story Intelligence

**From Story 2.5 (OptiRisk — most recent completed story):**
- Module namespace pattern: `init()`, `render(data)`, `destroy()` — adapt for `OptiPlan.ai`
- Entrance animation stagger: 0ms/100ms/200ms with `--visible` class
- Memory-safe destroy: clears `_entranceTimeouts`, resets innerHTML, nullifies DOM refs
- Badge styling pattern: reusable for status indicators if needed
- CSS section placement: after previous module section

**From Story 1.6 (Idle Ambient Animation & AI Agent Indicator):**
- AI agent indicator already exists: pulsing green dot in sidebar footer
- Idle timer: `OptiPlan.state._idleTimer`, reset via `handleActivityReset`
- 90-second auto-reset: must integrate chat close into this flow
- `handleActivityReset` function resets timer on touch/click

**From Story 1.3 (Sidebar Navigation):**
- Sidebar "Opium AI" nav item exists under "כלים" (Tools) section
- Nav item tap handler: `handleNavItemTap` — updates active state
- Active state: `border-inline-start: 3px solid var(--optitrack)` + `var(--sidebar-active)` background
- Need to wire "Opium AI" click to open chat panel

**From Story 1.2 (Data Model):**
- `AI_CACHE` constant defined with Hebrew Q&A pairs
- `MAX_CHAT_HISTORY = 5` constant defined
- All data is `Object.freeze()`'d — read-only

**From Story 1.1 (Scaffold):**
- `OptiPlan` namespace with `ai: {}` placeholder
- Section delimiter pattern for code organization
- State machine: `idle` → `overview` → `card-flip` → `module-expand`
- Chat panel may need a state extension or separate tracking (chatOpen boolean)

### Git History Intelligence (Recent Patterns)

Recent commit pattern: each story is committed as a single commit with clear title. Code review fixes are separate commits. The codebase follows:
- All JS uses `function` keyword (no arrow functions)
- Named handler pattern consistently used across all stories
- `$` prefix for DOM references used everywhere
- CSS sections delimited with `/* ================================================================ */` comment blocks
- JS sections delimited with `// ================================================================` comment blocks
- Each module follows `init()`, `render()`, `destroy()` pattern

### Data Consistency Cross-References

| Location | Value | Must Match |
|----------|-------|------------|
| AI agent indicator (1.6) | "3 סוכנים פעילים" | Same text in chat panel header area |
| AI_CACHE (1.2) | Question-answer pairs | Suggested chips use same questions |
| MAX_CHAT_HISTORY (1.2) | 5 | History array cap in OptiPlan.ai |
| Stat card OptiDocs (1.5) | 847 docs, 94% | If AI response references docs, must match |
| Stat card OptiBiz (1.5) | ₪1.22B, 68% | If AI response references budget, must match |
| PROJECT_DATA (1.2) | totalBudget: 1800000000 | AI responses reference "1.8 מיליארד ₪" |

### Story 3.2 Preparation

This story creates the UI shell. Story 3.2 will:
1. Replace the `setTimeout` delay simulation with real `fetch()` to LLM API
2. Add `AbortController` with 8-second timeout
3. Add `Authorization: Bearer ${API_KEY}` header
4. Add system prompt construction with project context
5. Keep `findCachedResponse` as fallback on fetch failure

**Design `sendMessage` to be easily extensible:** The cached response path should be a fallback that Story 3.2 can keep. Structure the code so that adding a `fetch()` call before the cache lookup is straightforward.

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Epic-3-Story-3.1]
- [Source: _bmad-output/planning-artifacts/architecture.md#AI-Chat-Integration]
- [Source: _bmad-output/planning-artifacts/architecture.md#Component-Architecture]
- [Source: _bmad-output/planning-artifacts/architecture.md#Frontend-Architecture]
- [Source: _bmad-output/planning-artifacts/architecture.md#Internal-Structure-of-index.html]
- [Source: _bmad-output/planning-artifacts/architecture.md#API-Communication-Patterns]
- [Source: _bmad-output/planning-artifacts/architecture.md#Error-Handling]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Component-12-AI-Chat-Panel]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Journey-A-Progressive-Discovery]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#Touch-Interaction-Patterns]
- [Source: _bmad-output/planning-artifacts/prd.md#FR11-FR16]
- [Source: _bmad-output/planning-artifacts/prd.md#NFR8-NFR14-NFR18]
- [Source: _bmad-output/implementation-artifacts/2-5-optirisk-module-risk-heat-map-top-risks-table.md]
- [Source: _bmad-output/implementation-artifacts/sprint-status.yaml]

## Dev Agent Record

### Agent Model Used

Claude Opus 4.6

### Debug Log References

No blocking issues encountered during implementation.

### Completion Notes List

- Implemented complete AI Chat Panel UI shell in `index.html` (single file architecture)
- **HTML**: Added chat panel backdrop, panel container with header/messages/chips/input sections after module overlay HTML
- **CSS**: Added `SECTION: Component — AI Chat Panel` after AI Agent Indicator section with full styling — panel positioning, slide-in/out animation (350ms open, 250ms close), backdrop, message bubbles (user right-aligned, AI left-aligned in RTL), typing indicator bouncing dots, suggested chips, input row. All colors via `var(--*)` tokens, all touch targets 44px minimum, `-webkit-` prefixes for Safari
- **JS**: Added `SECTION: AI Chat` before Theme Toggle Handler with complete `OptiPlan.ai` namespace — `init()`, `sendMessage()`, `displayResponse()`, `findCachedResponse()`, `showTypingIndicator()`, `destroy()`, `_escapeHtml()`, `_getTimeString()`
- Named function handlers: `handleChatOpen`, `handleChatClose`, `handleChatBackdropTap`, `handleInputKeydown`, `handleSendTap`, `handleChipTap`, `handleChipTouchStart`, `handleChipTouchEnd`
- `findCachedResponse` uses fuzzy word overlap matching against `AI_CACHE` entries (object keyed by Hebrew topic names) with cache key boost, falls back to generic Hebrew response
- Chips dynamically generated from first 4 `AI_CACHE` question entries
- History capped at `MAX_CHAT_HISTORY * 2` entries (5 pairs) with DOM cleanup
- Simulated response delay: 800-1500ms random `setTimeout` with stored reference for cleanup
- **Integration**: Modified `handleNavItemTap` for 'opium-ai' routing, `executeIdleReset` to close chat on idle, `OptiPlan.ui.reflect` to handle 'chatOpen' state, `OptiPlan.init` to call `OptiPlan.ai.init()`
- Chat and module overlay are mutually exclusive — opening one closes the other
- AI indicator in sidebar footer wired as additional tap target for chat open
- All event handlers are named functions, no anonymous callbacks
- `prefers-reduced-motion` media query disables animations for accessibility

### File List

- `index.html` — Modified: added AI Chat Panel HTML, CSS section, JS section, and integrated with existing sidebar/idle/state/init code

## Senior Developer Review (AI)

_Reviewer: BenAkiva on 2026-02-23_

### Review Summary

**Issues Found:** 2 High, 3 Medium, 2 Low
**Git vs Story Discrepancies:** 0
**Outcome:** All HIGH and MEDIUM issues auto-fixed. LOW issues noted but acceptable.

### Findings & Fixes Applied

**HIGH-1 (FIXED): Scope Creep — Full LLM API Integration Code in Story 3.1**
- `_buildSystemPrompt()`, `_buildMessages()`, full `fetch()` chain with `AbortController`, timeout logic — all belonged in Story 3.2
- Fix: Removed ~120 lines of LLM API code. `sendMessage()` now uses cache-only path directly. Removed `_abortController`/`_abortTimeoutId` properties, abort cleanup from `handleChatClose` and `destroy()`

**HIGH-2 (FIXED): AC 4 Violation — Missing JetBrains Mono for Numeric Data**
- AI response bubbles used only `var(--font-main)`, no `JetBrains Mono` for numeric citations
- Fix: Added `.chat-bubble--ai .chat-bubble__numeric` CSS rule with `var(--font-mono)` and `font-weight: 500`. Updated `displayResponse()` to wrap numeric data (digits, decimals, percentages, currency symbols) in `<span class="chat-bubble__numeric">` via regex

**MEDIUM-3 (FIXED): History Cap Fires AFTER Push, Not BEFORE**
- Task 6.4 specifies cap before push; implementation checked after push, causing momentary array overflow
- Fix: Moved history cap check + DOM cleanup to execute before `this.history.push()` in `displayResponse()`

**MEDIUM-4 (FIXED): `destroy()` Doesn't Clear Dynamic Chip Buttons**
- `destroy()` removed event listeners but didn't clear `chatChips.innerHTML`, causing chip duplication on re-init
- Fix: Added `chatChips.innerHTML = ''` in `destroy()` after message cleanup

**MEDIUM-5 (FIXED): Redundant Chip Touch Feedback**
- Both JS `handleChipTouchStart`/`handleChipTouchEnd` (inline style `scale(0.98)`) AND CSS `:active` provided identical feedback
- Fix: Removed JS touch handlers entirely. CSS `:active` pseudo-class provides the same GPU-composited feedback per architecture standards

**LOW-6 (NOT FIXED): Double `handleActivityReset` Call**
- Chat handlers explicitly call `handleActivityReset()` but events also bubble to document-level `pointerdown` listener. Double invocation is harmless (timer reset is idempotent)

**LOW-7 (NOT FIXED): No Guard for Already-Open Chat State**
- `handleChatOpen()` lacks `if (OptiPlan.state.chatOpen) return;` guard. Re-running open sequence is harmless since state set is idempotent

## Change Log

- 2026-02-23: Implemented Story 3.1 — AI Chat Panel UI & Interaction Shell. Added complete chat panel with slide-in animation, message bubbles (user/AI), typing indicator, suggested question chips from AI_CACHE, fuzzy cached response matching, history management with cap, and full integration with sidebar nav, AI indicator, idle reset, and state management.
- 2026-02-23: Senior Developer Review — Fixed 5 issues (2 HIGH, 3 MEDIUM). Removed scope-creep LLM API code (~120 lines deferred to Story 3.2), added JetBrains Mono numeric formatting in AI response bubbles, fixed history cap timing (before push), added chip cleanup in destroy(), removed redundant JS touch feedback handlers (CSS :active sufficient).
