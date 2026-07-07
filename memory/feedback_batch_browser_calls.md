---
name: feedback-batch-browser-calls
description: "Minimize tool-call count during browser automation (FOCUS, etc.) by batching actions instead of one-at-a-time round trips"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 6976c132-c0fb-4381-9a8c-77f9961ffd8c
---

Bundle browser automation steps into as few tool calls as possible. Concretely:

- **Don't call `find()` to relocate the same recurring element (e.g. a panel's close button) every iteration.** Screenshot or read the layout once, note the coordinate, and reuse the coordinate directly in later `computer` actions instead of re-querying `find` each time.
- **Fold an entire per-record sequence into one `browser_batch` call**: close-previous-panel → click-next-row (by coordinate) → wait → `get_page_text`, all as one batch, not as 2-3 separate batches per record. A loop over N leads should cost roughly N tool calls, not 3N.
- `find()` is only worth its own round trip when the target's position is genuinely unpredictable (list just reflowed, first time seeing a new layout) — not for elements that appear in the same place every time.
- Prefer `get_page_text` over `screenshot` for reading data, but don't reach for either when the data needed is already sitting in the conversation from a prior read.

**Why:** Darrell flagged this twice in one session (first generally, then again with a screenshot showing 20 claude-in-chrome calls and 7.4k tokens to gather 6 leads' contact info). The first fix ([[feedback_batch_browser_calls]] itself, originally) was too vague — "use browser_batch more" — and didn't actually change behavior because `find()` calls were still issued per-iteration outside the batch. This version names the specific anti-pattern to stop doing.

**How to apply:** Before starting a repetitive browser task (e.g. looping through a lead list in [[skill_focus_lead_followup]]), take one screenshot/read to map out fixed coordinates, then batch the full per-item sequence in a single `browser_batch` call per item. Reserve extra round trips for genuinely unpredictable UI state.
