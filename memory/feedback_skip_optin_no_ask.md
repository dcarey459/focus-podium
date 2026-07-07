---
name: feedback-skip-optin-no-ask
description: "When a FOCUS lead is blocked by unconfirmed text opt-in (or account-wide email being disabled), skip it automatically without asking Darrell for a decision each time"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 6976c132-c0fb-4381-9a8c-77f9961ffd8c
---

Don't stop and ask Darrell whether to send the opt-in-confirmation text when a lead lacks confirmed "Text Opt In (c)". Default action is: skip the lead, keep working the rest of the batch, and just note the skip (name + reason) in the end-of-batch report.

**Why:** Darrell already set the default (skip unconfirmed opt-in) in [[skill_focus_lead_followup]], and surfacing it as a per-lead or per-category question defeats the point of the default — he said explicitly not to ask again.

**How to apply:** Only escalate to a question if Darrell asks about opt-in status directly, or if he wants to run a deliberate opt-in outreach campaign (a distinct, explicitly-requested task). Never click "Apply"/send opt-in confirmation on your own initiative — that part of the rule still stands, only the "ask before skipping" step is removed.
