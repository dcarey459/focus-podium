---
name: feedback-existing-customer-optin
description: "For FOCUS After Sale Check-Up / anniversary leads (existing customers), treat as pre-opted-in for text rather than requiring a confirmed Text Opt In tag"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 6976c132-c0fb-4381-9a8c-77f9961ffd8c
---

Existing-customer FOCUS activities (After Sale Check-Up, Sales Anniversary Contact, and similar post-sale categories) can be texted even without a confirmed "Text Opt In (c)" tag.

**Why:** Darrell decided existing customers already have a relationship with the dealership, unlike cold internet prospects, so the strict opt-in gate in [[skill_focus_lead_followup]] (and [[feedback_skip_optin_no_ask]]) doesn't need to apply to them. Answered once on 2026-07-06 per the skill's instruction to ask this only one time.

**How to apply:** When working a post-sale/existing-customer category (After Sale Check-Up, Anniversary Contact, etc.), skip the opt-in check and proceed to draft/send texts. Still keep the strict confirmed-opt-in requirement for new prospects/internet leads (Day 0 Internet Follow-up, etc.) — this exception is for existing customers only.
