---
name: project-focus-podium-split
description: McElveen uses FOCUS (Reynolds and Reynolds CRM) for leads/sales records but Podium (separate tab/tool) for actual customer texting and AI follow-up
metadata: 
  node_type: memory
  type: project
  originSessionId: 6976c132-c0fb-4381-9a8c-77f9961ffd8c
---

FOCUS and Podium are two separate tools that are supposed to work together but don't fully merge yet. FOCUS is where leads and sales get loaded/tracked (the system of record). Podium is the actual channel used to text customers — it also runs an AI-driven follow-up on leads. Leads flow into both, but sending a text to a customer does not happen natively inside FOCUS's Client Record panel — despite what [[skill_focus_lead_followup]] currently assumes (it describes a "chat-bubble icon" opening "Send Text Message" inside FOCUS, which does not exist as described; that icon instead switches to an "Interactions" view, and FOCUS's own icon row is really just Call/Email/Task/Note/Calendar/History activity-logging shortcuts, not a compose UI).

**Why:** Darrell confirmed this on 2026-07-06 after a long browser-automation session where I mistakenly hunted for a "Send Text Message" button inside FOCUS per the skill's (outdated/wrong) description, burning a lot of tool calls. Evidence that texts actually route through Podium: interaction log entries on client records show past texts tagged with source "Podium," not FOCUS.

**How to apply:** For any future FOCUS lead follow-up work, don't try to send texts from within FOCUS. Once contact info and drafts are prepared from FOCUS, the actual send step needs to happen in Podium (a separate browser tab). The [[skill_focus_lead_followup]] skill file's "Sending" section describing FOCUS chat-bubble/Send Text Message is wrong for this account and should be corrected/rewritten once we've actually worked in Podium and learned its real UI (per [[feedback_skills_from_real_sessions]] — only write workflow steps after live use).

**Correction (2026-07-06, same day):** A message appearing in a Podium conversation thread as sent by Darrell doesn't necessarily mean Darrell composed it inside Podium's UI. Darrell often texts a customer directly from his personal phone, then logs that same message as an activity/note in FOCUS — and FOCUS has a real, automatic integration that pushes that logged activity through to Podium (confirmed by Darrell: "if in focus it goes to podium" = automatic integration, not manual double-entry into both systems separately). The reason this matters: management uses Podium as the system of record to validate who actually worked a lead — this determines sales-credit if a customer walks onto the lot unannounced later ("be-back" credit). Logging in FOCUS is how Darrell makes sure his personal-phone outreach still counts in that system. So a Podium timestamp/entry attributed to Darrell may reflect a FOCUS log entry that synced over, not something typed live in Podium's compose box — treat it as a valid proof-of-work record either way, but don't assume the delivery channel was Podium itself.
