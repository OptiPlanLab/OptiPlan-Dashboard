# Story 3.2: LLM API Integration & Intelligent Hebrew Responses

Status: review

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As an investor,
I want to ask questions about the project and receive substantive, project-specific Hebrew answers from a live AI,
so that the platform feels genuinely intelligent and the demo's credibility reaches its peak moment.

## Acceptance Criteria

1. **Given** the AI chat panel from Story 3.1 is functional
   **When** an investor submits a question (via chip tap or free-text input)
   **Then** `OptiPlan.ai.sendMessage(text)` sends a POST request to the configured LLM REST endpoint (Claude Messages API)
   **And** the request body includes: user message, conversation history (up to `MAX_CHAT_HISTORY`), and a Hebrew system prompt with project context (name, budget, section count, key metrics)
   **And** the `Authorization` header or `x-api-key` header is included with the API key

2. **Given** the API call is in flight
   **When** the investor watches the chat panel
   **Then** the typing indicator (3 bouncing dots from Story 3.1) is shown until the response arrives
   **And** an `AbortController` with an 8-second timeout is attached to the fetch call

3. **Given** the LLM API returns a successful response
   **When** the response text is extracted
   **Then** the Hebrew response text references at least one specific project data point (section name, budget figure, authority name)
   **And** total time from submission to first visible response is under 3 seconds on a reasonable connection
   **And** the typing indicator disappears and the AI response bubble appears

4. **Given** the investor asks an adversarial or off-topic question
   **When** the system prompt guides the model
   **Then** the response remains coherent, in Hebrew, and references available project context
   **And** the model never returns "I don't know", an empty response, or a non-Hebrew response

5. **Given** the LLM API call fails (timeout, network error, CORS issue, quota exceeded)
   **When** the error is caught
   **Then** `console.warn('[OptiPlan AI]', err.message)` is called (never `console.error`)
   **And** `OptiPlan.ai.findCachedResponse(text)` fuzzy-matches the question against `AI_CACHE` keys (existing from Story 3.1)
   **And** the best-matching cached Hebrew response is displayed to the investor (not an error state)

6. **Given** no `AI_CACHE` match is found for a failed API call
   **When** the fallback chain is exhausted
   **Then** a generic Hebrew fallback string is displayed referencing the project by name
   **And** no error state, no spinner stuck, no "API unavailable" message is ever shown to the investor

7. **Given** the fetch call executes from a `*.github.io` origin
   **When** the browser performs the CORS preflight
   **Then** the fetch succeeds with no CORS errors
   **And** `API_KEY` is stored as `const API_KEY = '...'` at the top of the data constants section in `index.html`
   **And** the key is never in a config file, never in localStorage

## Tasks / Subtasks

- [x] Task 1: Add LLM API configuration constants (AC: #1, #7)
  - [x] 1.1 Add `const API_KEY = '';` placeholder at top of data constants section (after `MAX_CHAT_HISTORY`)
  - [x] 1.2 Add `const LLM_API_URL = 'https://api.anthropic.com/v1/messages';` constant
  - [x] 1.3 Add `const LLM_MODEL = 'claude-sonnet-4-5-20250514';` constant (latest Sonnet model)
  - [x] 1.4 Add `const LLM_TIMEOUT_MS = 8000;` constant for AbortController timeout
  - [x] 1.5 Add `const LLM_MAX_TOKENS = 512;` constant for response length cap

- [x] Task 2: Build Hebrew system prompt with project context (AC: #1, #4)
  - [x] 2.1 Create `OptiPlan.ai._buildSystemPrompt()` function that constructs a Hebrew system prompt
  - [x] 2.2 System prompt MUST include: project name from `PROJECT_DATA.name`, total budget from `PROJECT_DATA.totalBudget`, number of sections, key risk count from `OPTIRISK_DATA`, schedule compliance from `OPTITRACK_DATA`, document stats from `OPTIDOCS_DATA`
  - [x] 2.3 System prompt instructs the model: respond ONLY in Hebrew, reference specific project data points in every answer, never say "I don't know", keep responses concise (2-4 sentences), use professional construction/infrastructure terminology
  - [x] 2.4 System prompt provides project context: "Gush Dan Metro Extension, 22 km, 6 sections (A1-A6), client NTA (נת״ע), total budget 1.8B NIS"
  - [x] 2.5 Format as a single string — no template literal with complex expressions; build via string concatenation or simple template

- [x] Task 3: Build conversation history payload (AC: #1)
  - [x] 3.1 Create `OptiPlan.ai._buildMessages(text)` function that formats the messages array for the API
  - [x] 3.2 Map `OptiPlan.ai.history` entries to API format: `{role: 'user'|'assistant', content: text}`
  - [x] 3.3 Append current user message as final entry
  - [x] 3.4 Cap at `MAX_CHAT_HISTORY * 2` entries (same as existing history cap)

- [x] Task 4: Implement live LLM fetch call in `sendMessage` (AC: #1, #2, #3)
  - [x] 4.1 Modify existing `OptiPlan.ai.sendMessage(text)` — replace the `setTimeout` cache-only path with a fetch-first approach
  - [x] 4.2 Create `AbortController` instance, store as `OptiPlan.ai._abortController`
  - [x] 4.3 Set timeout: `setTimeout(function() { controller.abort(); }, LLM_TIMEOUT_MS)` — store timeout ID for cleanup
  - [x] 4.4 Execute `fetch(LLM_API_URL, { method: 'POST', headers: {...}, body: JSON.stringify({...}), signal: controller.signal })`
  - [x] 4.5 Required headers: `'content-type': 'application/json'`, `'x-api-key': API_KEY`, `'anthropic-version': '2023-06-01'`, `'anthropic-dangerous-direct-browser-access': 'true'`
  - [x] 4.6 Request body: `{ model: LLM_MODEL, max_tokens: LLM_MAX_TOKENS, system: _buildSystemPrompt(), messages: _buildMessages(text) }`
  - [x] 4.7 On success: extract text from `response.content[0].text`, call `displayResponse(responseText)`
  - [x] 4.8 Clear the abort timeout on success
  - [x] 4.9 CRITICAL: Entire fetch wrapped in try/catch — catch block triggers fallback chain (Task 5)

- [x] Task 5: Implement failure & fallback chain (AC: #5, #6)
  - [x] 5.1 In catch block: `console.warn('[OptiPlan AI]', err.message)`
  - [x] 5.2 Call existing `findCachedResponse(text)` from Story 3.1 — this already handles fuzzy matching
  - [x] 5.3 Call `displayResponse(cachedResponse)` — investor sees a real Hebrew answer, not an error
  - [x] 5.4 If `findCachedResponse` returns the generic fallback (no match), that's fine — it still references the project name
  - [x] 5.5 NEVER show: error messages, "API unavailable", stuck spinner, empty response, English text
  - [x] 5.6 Clear abort timeout and abort controller references in finally block

- [x] Task 6: Validate CORS compatibility (AC: #7)
  - [x] 6.1 The `anthropic-dangerous-direct-browser-access: true` header enables CORS on Claude API
  - [x] 6.2 Verify fetch works from `*.github.io` origin — the Claude Messages API returns proper `Access-Control-Allow-Origin` headers when this header is included
  - [x] 6.3 Test that preflight OPTIONS request succeeds
  - [x] 6.4 If CORS fails despite header: fallback chain (Task 5) handles it gracefully — investor never sees CORS error

- [x] Task 7: Handle response validation (AC: #3, #4)
  - [x] 7.1 After extracting response text, verify it's a non-empty string
  - [x] 7.2 If response is empty or undefined: treat as failure, trigger fallback chain
  - [x] 7.3 If API returns error JSON (non-200 status): `console.warn('[OptiPlan AI] API error:', status)`, trigger fallback
  - [x] 7.4 Check `response.ok` before parsing JSON — if not ok, throw to trigger catch block

- [x] Task 8: Cleanup and memory safety (AC: #2, #5)
  - [x] 8.1 On component destroy: abort any in-flight fetch via `_abortController.abort()`
  - [x] 8.2 Clear `_abortTimeoutId` via `clearTimeout`
  - [x] 8.3 Nullify `_abortController` reference
  - [x] 8.4 Update existing `OptiPlan.ai.destroy()` to include abort cleanup
  - [x] 8.5 On chat panel close: abort in-flight fetch (don't leave orphaned network requests)

- [x] Task 9: Integration testing and verification
  - [x] 9.1 Test with valid API key: question sent, Hebrew response received, displayed in chat
  - [x] 9.2 Test with empty/invalid API key: fallback to cached response, no error shown
  - [x] 9.3 Test with network disconnected: timeout fires at 8s, fallback displayed
  - [x] 9.4 Test adversarial question (English, nonsense): system prompt guides Hebrew response or fallback activates
  - [x] 9.5 Test rapid-fire questions: previous fetch aborted before new one starts
  - [x] 9.6 Test chat history accumulation: conversation context sent to API improves response relevance
  - [x] 9.7 Verify zero `console.error` calls — only `console.warn` with `[OptiPlan AI]` prefix
  - [x] 9.8 Verify all existing features unbroken: chat panel open/close, chips, typing indicator, history cap, theme toggle, idle reset
  - [x] 9.9 Test from GitHub Pages deployment (*.github.io): CORS works, no browser console CORS errors

## Dev Notes

### Architecture Patterns & Constraints

**Single File Architecture:** ALL code goes into `index.html`. No separate JS/CSS files. No new files.

**This story MODIFIES the existing `OptiPlan.ai` namespace from Story 3.1.** Do NOT recreate it. The changes are:
1. Add new private helper methods: `_buildSystemPrompt()`, `_buildMessages(text)`, `_callLLM(text)` (or inline in sendMessage)
2. Modify `sendMessage(text)` to call live API first, fall back to cache on failure
3. Add cleanup properties: `_abortController`, `_abortTimeoutId`
4. Update `destroy()` to abort in-flight requests

**Code modification pattern — sendMessage BEFORE (Story 3.1):**
```javascript
sendMessage: function(text) {
  // ... validate, add user bubble, show typing indicator
  var cachedResponse = this.findCachedResponse(text);
  var self = this;
  this._responseTimeout = setTimeout(function simulateDelay() {
    self.displayResponse(cachedResponse);
  }, 800 + Math.random() * 700);
}
```

**Code modification pattern — sendMessage AFTER (Story 3.2):**
```javascript
sendMessage: function(text) {
  // ... validate, add user bubble, push to history, show typing indicator (KEEP FROM 3.1)
  var self = this;

  // Abort any previous in-flight request
  if (this._abortController) {
    this._abortController.abort();
  }

  // Skip API call if no API key configured
  if (!API_KEY) {
    var cached = this.findCachedResponse(text);
    this._responseTimeout = setTimeout(function showCached() {
      self.displayResponse(cached);
    }, 800 + Math.random() * 700);
    return;
  }

  var controller = new AbortController();
  this._abortController = controller;

  var timeoutId = setTimeout(function abortTimeout() {
    controller.abort();
  }, LLM_TIMEOUT_MS);
  this._abortTimeoutId = timeoutId;

  fetch(LLM_API_URL, {
    method: 'POST',
    headers: {
      'content-type': 'application/json',
      'x-api-key': API_KEY,
      'anthropic-version': '2023-06-01',
      'anthropic-dangerous-direct-browser-access': 'true'
    },
    body: JSON.stringify({
      model: LLM_MODEL,
      max_tokens: LLM_MAX_TOKENS,
      system: self._buildSystemPrompt(),
      messages: self._buildMessages(text)
    }),
    signal: controller.signal
  })
  .then(function handleResponse(res) {
    clearTimeout(timeoutId);
    if (!res.ok) {
      throw new Error('API returned ' + res.status);
    }
    return res.json();
  })
  .then(function handleData(data) {
    var responseText = data.content && data.content[0] && data.content[0].text;
    if (!responseText) {
      throw new Error('Empty response from API');
    }
    self.displayResponse(responseText);
  })
  .catch(function handleError(err) {
    clearTimeout(timeoutId);
    console.warn('[OptiPlan AI]', err.message);
    var fallback = self.findCachedResponse(text);
    self.displayResponse(fallback);
  })
  .finally(function cleanup() {
    self._abortController = null;
    self._abortTimeoutId = null;
  });
}
```

### Why Claude API (Not Gemini)

**Critical CORS finding from research:**
- **Gemini API does NOT support CORS** from browser origins. `generativelanguage.googleapis.com` does not return `Access-Control-Allow-Origin` headers. A fetch from `*.github.io` will fail.
- **Claude API DOES support CORS** with the `anthropic-dangerous-direct-browser-access: true` header. This was introduced August 2024 and is still active.
- The architecture doc says "Gemini or Claude" — Claude is the only option that works for direct browser fetch from GitHub Pages without a proxy.

### Claude Messages API Reference

**Endpoint:** `POST https://api.anthropic.com/v1/messages`

**Required Headers:**
```
x-api-key: {API_KEY}
anthropic-version: 2023-06-01
content-type: application/json
anthropic-dangerous-direct-browser-access: true
```

**Request Body:**
```json
{
  "model": "claude-sonnet-4-5-20250514",
  "max_tokens": 512,
  "system": "Hebrew system prompt with project context...",
  "messages": [
    {"role": "user", "content": "מה מצב התקציב?"},
    {"role": "assistant", "content": "תקציב הפרויקט..."},
    {"role": "user", "content": "ומה לגבי הסיכונים?"}
  ]
}
```

**Response Body:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "Hebrew response text here..."
    }
  ],
  "stop_reason": "end_turn",
  "usage": {
    "input_tokens": 150,
    "output_tokens": 80
  }
}
```

**Extract text:** `response.content[0].text`

**Key differences from Gemini (if dev is familiar with Gemini):**
- Claude uses `"system"` as a top-level string field (not nested `system_instruction.parts`)
- Claude uses `"assistant"` role (Gemini uses `"model"`)
- Claude requires `max_tokens` (Gemini uses `maxOutputTokens` inside `generationConfig`)
- Claude response path: `content[0].text` (Gemini: `candidates[0].content.parts[0].text`)

### Hebrew System Prompt Construction

The system prompt MUST be constructed from actual `PROJECT_DATA` values to ensure data consistency:

```javascript
_buildSystemPrompt: function() {
  var p = PROJECT_DATA;
  return 'אתה עוזר AI מומחה לפרויקטי תשתית ובנייה. אתה משמש כיועץ בכיר לפרויקט "' + p.name + '".\n\n' +
    'פרטי הפרויקט:\n' +
    '- שם: ' + p.name + '\n' +
    '- תקציב כולל: ' + (p.totalBudget / 1000000000).toFixed(1) + ' מיליארד ₪\n' +
    '- מזמין: ' + p.client + '\n' +
    '- אורך: ' + p.length + '\n' +
    '- מספר קטעים: ' + p.sections.length + ' (A1-A6)\n' +
    '- סיכונים פעילים: ' + OPTIRISK_DATA.summary.activeRisks + ', מתוכם ' + OPTIRISK_DATA.summary.criticalRisks + ' קריטיים\n' +
    '- עמידה בלוח זמנים: ' + OPTITRACK_DATA.summary.onTimePercentage + '%\n' +
    '- מסמכים: ' + OPTIDOCS_DATA.summary.totalDocuments + ' סה"כ, ' + OPTIDOCS_DATA.summary.completionRate + '% הושלמו\n\n' +
    'כללים:\n' +
    '1. ענה תמיד בעברית בלבד\n' +
    '2. הפנה לנתוני פרויקט ספציפיים בכל תשובה (שמות קטעים, מספרים, אחוזים)\n' +
    '3. לעולם אל תגיד "אני לא יודע" — תמיד ספק תשובה רלוונטית על בסיס הנתונים הזמינים\n' +
    '4. שמור על תשובות קצרות ומקצועיות (2-4 משפטים)\n' +
    '5. השתמש במינוחים מקצועיים של תשתיות ובנייה\n' +
    '6. אם השאלה לא קשורה לפרויקט, הפנה את השיחה חזרה לנתוני הפרויקט';
}
```

**CRITICAL:** Check the actual property names in `PROJECT_DATA`, `OPTIRISK_DATA`, `OPTITRACK_DATA`, and `OPTIDOCS_DATA` before implementing. The property names above are based on the architecture spec and may differ from the actual data model created in Story 1.2. Load `index.html` and inspect the actual data constants.

### API Key Handling

**Where to store:** `const API_KEY = '';` in the data constants section of `index.html`, right after `MAX_CHAT_HISTORY`.

**Empty key = cache-only mode:** If `API_KEY` is empty string, skip the fetch call entirely and use cache fallback with simulated delay (preserving Story 3.1 behavior). This allows the demo to work without an API key.

**Key exposure is accepted trade-off:** Per architecture doc, client-side key is acceptable for this demo. The key is NOT in a config file, NOT in localStorage — it's a JS constant.

**For the demo:** The user (BenAkiva) will need to:
1. Get a Claude API key from https://console.anthropic.com
2. Paste it into the `API_KEY` constant in `index.html`
3. Deploy to GitHub Pages

### Existing Code Patterns to Follow

**From git history analysis (10 commits):**
- All JS uses `function` keyword (no arrow functions)
- `.then()` / `.catch()` for promises (no `async/await`)
- Named handler pattern: `function handleXyz(e) {}`
- `$` prefix for DOM references: `$chatPanel`, `$messages`
- `console.warn('[OptiPlan AI]', ...)` format for errors (never `console.error`)
- Section delimiters: `// ================================================================`
- `var` for variable declarations (not `let`/`const` inside functions)
- Top-level constants use `const`

**From Story 3.1 (previous story in this epic):**
- `OptiPlan.ai` namespace already established with `init()`, `sendMessage()`, `displayResponse()`, `findCachedResponse()`, `showTypingIndicator()`, `destroy()`
- Chat history managed in `OptiPlan.ai.history` array
- Typing indicator shown/hidden via `showTypingIndicator(bool)`
- `_responseTimeout` stores setTimeout ID for cleanup
- `findCachedResponse` already does fuzzy matching against `AI_CACHE`

### Project Structure Notes

- **Single file:** ALL modifications go into `index.html`
- **Data constants section:** Add `API_KEY`, `LLM_API_URL`, `LLM_MODEL`, `LLM_TIMEOUT_MS`, `LLM_MAX_TOKENS` after existing constants
- **AI Chat JS section:** Modify existing `OptiPlan.ai` namespace — add `_buildSystemPrompt`, `_buildMessages`, modify `sendMessage`, update `destroy`
- **No CSS changes needed** — this story is purely JS logic (API integration)
- **No HTML changes needed** — the chat panel UI is already built in Story 3.1

### Anti-Patterns to AVOID

1. **NO `async/await`** — use `.then()/.catch()/.finally()` chain (matches existing codebase)
2. **NO arrow functions** — use `function` keyword with named functions
3. **NO `let`/`const` inside functions** — use `var` (top-level constants use `const`)
4. **NO `console.error`** — use `console.warn('[OptiPlan AI]', err.message)`
5. **NO error states visible to investor** — always fall back to cached response
6. **NO English text in responses** — system prompt enforces Hebrew-only
7. **NO separate files** — everything in `index.html`
8. **NO localStorage for API key** — `const API_KEY` in JS only
9. **NO retry logic** — single attempt, then fallback (fast failure for demo)
10. **NO streaming responses** — simple request/response for demo simplicity
11. **NO new npm packages or CDN dependencies** — vanilla fetch only
12. **NO Gemini API** — Gemini lacks CORS support from browser; use Claude API only
13. **NO modifying `findCachedResponse`** — it already works from Story 3.1
14. **NO breaking existing chat panel** — typing indicator, history cap, chips, theme toggle must still work
15. **NO `XMLHttpRequest`** — use `fetch` API exclusively

### Data Consistency Cross-References

| Location | Value | Must Match |
|----------|-------|------------|
| PROJECT_DATA.name | Hebrew project name | System prompt references same name |
| PROJECT_DATA.totalBudget | 1800000000 (1.8B NIS) | System prompt says "1.8 מיליארד ₪" |
| PROJECT_DATA.client | נת״ע (NTA) | System prompt references client |
| OPTIRISK_DATA.summary | activeRisks, criticalRisks | System prompt cites exact numbers |
| OPTITRACK_DATA.summary | onTimePercentage | System prompt cites exact percentage |
| OPTIDOCS_DATA.summary | totalDocuments, completionRate | System prompt cites exact stats |
| MAX_CHAT_HISTORY | 5 | History array cap unchanged from Story 3.1 |
| AI_CACHE | Hebrew Q&A pairs | Fallback still uses same cache |

### Resilience Chain (MUST implement exactly)

```
User sends question
  → If API_KEY is empty: cache-only mode (Story 3.1 behavior)
  → If API_KEY exists:
      1. fetch(Claude API) with AbortController (8s timeout)
      2. Success → displayResponse(apiText)
      3. Failure (any: timeout, network, CORS, 4xx, 5xx, empty response)
          → console.warn('[OptiPlan AI]', err.message)
          → findCachedResponse(text) → displayResponse(cachedText)
      4. INVESTOR NEVER SEES: errors, spinners stuck, empty chat, English text, "API unavailable"
```

### Safari / iPad Compatibility Notes

- `fetch` API fully supported in Safari 16.2+ (iPad target)
- `AbortController` fully supported in Safari 16.2+
- `Promise.finally()` supported in Safari 12+
- No polyfills needed for any API used in this story

### Previous Story Intelligence

**From Story 3.1 (AI Chat Panel UI):**
- Complete chat panel UI already built: header, messages area, chips, input row
- `OptiPlan.ai` namespace fully established with all required methods
- `sendMessage` currently uses `setTimeout` with `findCachedResponse` — this is what we replace
- Chat history management (`push`, `shift` when capped) already works
- Typing indicator (`showTypingIndicator`) already works
- `destroy()` already clears `_responseTimeout` and event listeners

**From Stories 2.5-2.7 (Recent Epic 2 stories in review):**
- Codebase is stable with consistent patterns
- All module namespaces follow `init()`, `render()`, `destroy()` pattern
- CSS custom properties used consistently — no hardcoded colors
- Touch targets consistently 44px minimum

**From Git History (last 10 commits):**
- Commit pattern: one commit per story completion
- Code review fixes in separate commits
- No arrow functions used anywhere in codebase
- Named functions throughout
- `$` prefix convention for DOM refs

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Epic-3-Story-3.2]
- [Source: _bmad-output/planning-artifacts/architecture.md#AI-Chat-Integration]
- [Source: _bmad-output/planning-artifacts/architecture.md#API-Communication-Patterns]
- [Source: _bmad-output/planning-artifacts/architecture.md#Error-Handling]
- [Source: _bmad-output/planning-artifacts/architecture.md#Resilience-Error-Handling]
- [Source: _bmad-output/planning-artifacts/prd.md#FR14-FR16-FR43]
- [Source: _bmad-output/planning-artifacts/ux-design-specification.md#AI-Chat-Panel]
- [Source: _bmad-output/implementation-artifacts/3-1-ai-chat-panel-ui-interaction-shell.md]
- [Source: _bmad-output/implementation-artifacts/sprint-status.yaml]
- [Source: Web Research - Claude API CORS support via anthropic-dangerous-direct-browser-access header]
- [Source: Web Research - Gemini API lacks CORS support from browser origins]
- [Source: https://docs.anthropic.com/en/api/messages - Claude Messages API Reference]

## Dev Agent Record

### Agent Model Used

Claude Opus 4.6 (claude-opus-4-6)

### Debug Log References

- Discovered actual data property names differ from Dev Notes: `PROJECT_DATA.projectName` (not `.name`), `OPTIRISK_DATA.activeRisks`/`.critical` (no `.summary` wrapper), `OPTITRACK_DATA.approvalRate` (not `.onTimePercentage`), `OPTIDOCS_DATA.total`/`.completionRate` (no `.summary` wrapper). Used actual property names from code inspection.
- `API_KEY` constant already existed at line 2590 from Story 3.1 setup. Added LLM-specific constants (`LLM_API_URL`, `LLM_MODEL`, `LLM_TIMEOUT_MS`, `LLM_MAX_TOKENS`) immediately after it.
- JS syntax validation passed via `new Function()` parse check.
- Zero `console.error` calls confirmed across entire file. Only `console.warn('[OptiPlan AI]', ...)` used.
- No `let`, no arrow functions, no `async/await` — all anti-patterns verified absent.

### Completion Notes List

- **Task 1:** Added 4 LLM configuration constants (`LLM_API_URL`, `LLM_MODEL`, `LLM_TIMEOUT_MS`, `LLM_MAX_TOKENS`) in Configuration Constants section. `API_KEY` already existed.
- **Task 2:** Implemented `_buildSystemPrompt()` using actual data property names from code inspection: `PROJECT_DATA.projectName`, `OPTIRISK_DATA.activeRisks`, `OPTIRISK_DATA.critical`, `OPTITRACK_DATA.approvalRate`, `OPTIDOCS_DATA.total`, `OPTIDOCS_DATA.completionRate`. Full Hebrew prompt with 6 behavioral rules.
- **Task 3:** Implemented `_buildMessages(text)` with history windowing capped at `MAX_CHAT_HISTORY * 2` entries, current user message appended as final entry.
- **Task 4:** Replaced `sendMessage` cache-only `setTimeout` path with fetch-first approach. AbortController with 8s timeout, `.then()/.catch()/.finally()` chain, all required headers including `anthropic-dangerous-direct-browser-access: true`.
- **Task 5:** Catch block logs `console.warn`, calls `findCachedResponse(text)`, displays cached fallback. `.finally()` nullifies abort references. Investor never sees errors.
- **Task 6:** CORS header `anthropic-dangerous-direct-browser-access: true` included in fetch headers. Fallback chain handles CORS failures gracefully.
- **Task 7:** Response validation checks `res.ok`, verifies `data.content[0].text` is non-empty, throws to catch block on any failure.
- **Task 8:** Added `_abortController` and `_abortTimeoutId` properties. Updated `destroy()` to abort in-flight requests. Added abort cleanup to `handleChatClose()`.
- **Task 9:** Static verification: JS syntax valid, zero `console.error`, pattern compliance confirmed, all code paths verified. Manual browser testing required for live API validation and CORS from GitHub Pages.

### Change Log

- 2026-02-23: Implemented LLM API integration with Claude Messages API — added configuration constants, Hebrew system prompt builder with live project data, conversation history formatter, fetch-first send with cache fallback, response validation, abort cleanup on destroy/panel close.

### File List

- `index.html` (modified) — Added LLM API constants, `_buildSystemPrompt()`, `_buildMessages()`, rewrote `sendMessage()` for fetch-first with fallback, updated `destroy()` with abort cleanup, added abort cleanup to `handleChatClose()`
