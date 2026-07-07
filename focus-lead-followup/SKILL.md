---
name: focus-lead-followup
description: Follow up on overdue/assigned leads in FOCUS by Reynolds and Reynolds (the dealer CRM at focus.dealer.reyrey.net, used by McElveen dealership, logged in as CAREY, DARRELL). Use this whenever Darrell asks to check his leads, find overdue calls, catch up on follow-ups, or send outreach messages to prospects in FOCUS. Covers finding overdue activities, categorizing them by type, drafting one shared-but-personalized message template, and sending it individually (email or text, never a phone call). FOCUS itself has no working compose/send UI — the actual send happens in Podium (app.podium.com), a separate tool/tab used alongside FOCUS for texting and AI-driven lead follow-up.
---

# FOCUS Lead Follow-up

Assists Darrell Carey (salesperson, CAREY, DARRELL at McElveen Inc.) with catching up on overdue CRM activities in FOCUS by Reynolds and Reynolds. This is a browser-automation task against a live dealer CRM, not a code task.

## Setup

- Requires `mcp__claude-in-chrome__*` tools. Load the core set in one `ToolSearch` call: `tabs_context_mcp, navigate, computer, read_page, find, get_page_text, browser_batch`.
- FOCUS only works from **Darrell's already-logged-in tab** dragged into the Claude extension's tab group — do not navigate a fresh tab to the FOCUS URL yourself. Reynolds and Reynolds enforces single-session login, so opening/navigating a second tab to the same URL triggers "Open session detected, signing in will close any open session" and will log Darrell out of his working session. If FOCUS isn't visible via `tabs_context_mcp`, ask Darrell to drag the tab into the Claude tab group rather than opening it yourself.
- Sending requires a **second tab on Podium** (app.podium.com/inbox), also dragged into the Claude tab group, alongside the FOCUS tab. Use `tabs_context_mcp` to check whether it's already present before asking Darrell to open it.
- Prefer `browser_batch` to chain click → type → screenshot sequences in one round trip.

## Finding overdue activities

FOCUS has no explicit "Overdue" filter. In the **Daily Work Plan** tab:
1. Set date range filter to something like "Date + Previous N Days" with Status left at its default (Incomplete).
2. Overdue rows are marked in red text with a red "!" icon; anything dated before today is overdue, today/future is not.
3. Cross-check the count against the "Calls Overdue: N" stat in the Activity Overview widget if visible (that stat itself isn't clickable/drillable).
4. Use `get_page_text` rather than screenshots to read full names/vehicles/emails — the visual UI truncates long strings with "...".

## Categorizing

Group overdue rows by their activity type label (e.g. "Day 0 Internet Follow up Call", "PAYOFF Follow Up", "1st Xtream Follow Up Call", "Salesperson Day 1 Phone Call", "After Sale Check Up" variants, "Anniversary Contact", "Referral Call"). Present the categories with counts and let Darrell pick which one to work first — don't assume.

## Gathering per-lead contact info

- Click the small triangle at the left of a row to expand inline details (Status, Source, Created, Contact phone/email, Trade Vehicle, History Sold/Service) without leaving the list.
- The list is virtualized: expanding/collapsing reflows the list and invalidates prior `find` refs. Re-run `find`/`get_page_text` after each expand rather than reusing old element references.
- Clicking directly on a lead's name instead opens a full two-panel view (Activity panel + Client Record panel) with richer detail — use this when you need CRM Advisor notes, talking points, or duplicate-record flags too.

## Drafting the message

- Write **one shared template**, personalized per lead using whatever's known (first name, vehicle of interest, trade vehicle, lead source). Keep it short enough for SMS (roughly 250 characters or less; FOCUS auto-appends a ~45-character "Reply STOP to OptOut from McElveen Buick GMC" footer, and the compose box shows a live segment counter).
- Never place a phone call as the outreach method unless Darrell explicitly asks — default channel is email or text.
- **Present all personalized drafts at once and get a single batch approval**, not one confirmation per lead — Darrell has explicitly said per-message confirmation burns time/tokens. Only send after that one combined "yes."

## Sending

**FOCUS's Client Record panel does not compose or send messages, despite appearances.** The icon row under the client name/photo (phone, envelope, chat-bubble, checkmark, calendar icons) looks like compose shortcuts but isn't: the chat-bubble icon just switches the panel to an "Interactions" view (a read-only log), and the other icons open "Activity Access" dialogs for *logging* an activity (e.g. recording that a call happened), not composing outbound text/email. Don't spend tool calls hunting for a "Send Text Message" button inside FOCUS — it doesn't exist there.

The actual send happens in **Podium** (a separate tab, app.podium.com/inbox):
- Find the lead's conversation via the inbox list (search by name, or check pipeline-stage filters like New lead/Qualifying/Test drive/Engaging/Payment collection/Follow-up in the left nav).
- Compose in the text box at the bottom of the thread ("Text [Name] or use a template"), then use the **"Send and close"** button (there's also a plain send-arrow next to it — behavior difference not yet confirmed live, ask Darrell if unsure which to use).
- Podium runs an **AI Agent** that auto-responds to leads (messages tagged "AI Agent · N sources" in the thread). Check the thread for AI Agent activity before drafting — sending a manual message unassigns the AI Agent from that conversation, so don't step on a reply it already sent.
- Verify a send went through via the conversation thread itself (new message from Darrell) and/or the **Activity Timeline** in the contact's right-hand panel.
- **FOCUS → Podium sync**: logging a note/activity on a lead in FOCUS automatically pushes it into Podium's conversation history. Darrell sometimes texts a customer from his personal phone, then logs that same message as a FOCUS activity purely so Podium (the system management audits for lead-credit) shows he worked it. So a message appearing in Podium isn't proof it was typed there live — either path (Podium compose, or FOCUS log that synced over) counts as "sent" for reporting purposes.
- Not yet verified live: exact opt-in-confirmation flow inside Podium, SMS segment/character limits in Podium's compose box. Treat FOCUS's opt-in blocker (below) as the governing rule until Podium's own opt-in behavior has been observed directly.

## Known blockers — stop and ask Darrell rather than working around these

- **"You cannot send email because you do not have Email set up. Please contact your administrator."** This is an account-level restriction FOCUS shows on the Send Email form. Don't try to force it — surface it and ask whether to switch to text, or wait on the admin to enable email.
- **Unconfirmed "Text Opt In" status.** If the Client Record's Text Opt In doesn't have a "(c)" tag, CRM Advisor shows a "TEXT OPT IN — Apply" suggestion. Clicking Apply opens a "CONFIRM OPT IN" dialog — despite text mentioning an "OPT IN CLIENT" button, in practice the only real action is **"SEND OPT-IN CONFIRMATION"**, which texts the customer a generic consent-request (not the drafted follow-up message). The actual follow-up can only go out later if/when they reply to confirm. This is a meaningfully different outcome than "send the message now" — always confirm with Darrell before opting anyone in, and don't assume consent on his behalf.
- **Business/company-type records with no text option at all** (no chat-bubble icon, no "Draft Text" recommended action) — these leads may only be reachable by email, so if email is also down, flag as fully blocked rather than guessing at a workaround.
- Stale element references after list reflow, or "Couldn't determine which page this action targets" errors — re-screenshot or re-run `find`/`get_page_text` rather than retrying the same ref.

## Reporting back

After each batch send, summarize: how many sent successfully (and to whom), how many held/blocked and why, and what decision (if any) is needed from Darrell to unblock the rest. Don't mark anything as "sent" without verifying it in the Interactions log first.

## Default policy (unless Darrell overrides)

When Darrell front-loads a category to work (see phrasing examples below), apply these defaults so no mid-task questions are needed:

- **Channel**: text only. Never touch email setup or attempt email if it's account-blocked — just note it and move on.
- **Opt-in**: skip any lead without confirmed "Text Opt In" — do not click "Apply"/send an opt-in-confirmation request unless he explicitly says to.
- **Approval**: draft all messages for the category, present once as a batch, get one combined "yes," then send individually.
- **Reporting**: end with a short tally — sent / skipped (and why) / needs-a-decision.

### Example instructions per category (front-loaded, one-shot)

- **Internet/New Lead Follow-ups**: "Work my Internet/New Lead follow-ups: one template personalized per vehicle interest, text only opted-in leads, skip the rest, one batch approval, then send and report."
- **PAYOFF Follow Up**: "Work my PAYOFF follow-ups: draft a trade-in/payoff-value message, text opted-in leads only, skip the rest, one batch approval, report results."
- **1st Xtream Follow Up**: "Work my 1st Xtream follow-ups: draft an appointment-confirmation style message, text opted-in leads only, skip the rest, one batch approval, report results."
- **Salesperson Day 1 Call**: "Work my Salesperson Day 1 leads: text (no calls), one batch approval, skip anyone not opted in, report results."
- **Post-Sale Check-Ins**: existing customers — the opt-in default may reasonably differ since there's already a relationship. Ask Darrell once whether to treat existing customers as pre-opted-in, then reuse that answer for future runs.
- **Anniversary/Referral/Misc**: "Work these ad hoc: draft appropriate message per lead type, batch-approve, skip unconfirmed opt-in, report results."
