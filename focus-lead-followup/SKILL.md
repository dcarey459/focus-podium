---
name: focus-lead-followup
description: Follow up on overdue/assigned leads in FOCUS by Reynolds and Reynolds (the dealer CRM at focus.dealer.reyrey.net, used by McElveen dealership, logged in as CAREY, DARRELL). Use this whenever Darrell asks to check his leads, find overdue calls, catch up on follow-ups, or draft outreach messages to prospects in FOCUS. Covers finding overdue activities (via focus_workplan.py), categorizing them by type, and drafting one shared-but-personalized message template per batch. Does NOT send — actual sending is handled separately (~/podium-triage/podium_triage.py, not yet wired in).
---

# FOCUS Lead Follow-up

Assists Darrell Carey (salesperson, CAREY, DARRELL at McElveen Inc.) with catching up on overdue CRM activities in FOCUS by Reynolds and Reynolds. Finding/categorizing overdue activities is a **code task** (see below); only contact-info gathering and drafting remain browser-automation. **This skill stops at drafting — it does not send.** FOCUS is already connected to Podium somehow (activity/notes sync over — see [[project_focus_podium_split]]), and sending will go through Darrell's own `~/podium-triage/podium_triage.py` once that's wired in; don't improvise a Podium-tab send path in the meantime.

## Setup

- Requires `mcp__claude-in-chrome__*` tools, but only for the per-lead contact-info steps. Load the core set in one `ToolSearch` call: `tabs_context_mcp, navigate, computer, read_page, find, get_page_text, browser_batch`.
- FOCUS only works from **Darrell's already-logged-in tab**/Chrome window — do not navigate a fresh tab to the FOCUS URL yourself and do not reload it. Reynolds and Reynolds enforces single-session login; a second tab/reload on the same URL triggers "Open session detected, signing in will close any open session" and logs Darrell out. If FOCUS isn't visible via `tabs_context_mcp` in the Claude extension's tab group, ask Darrell to drag the tab in rather than opening it yourself.
- Prefer `browser_batch` to chain click → type → screenshot sequences in one round trip.

## Finding + categorizing overdue activities (code, not browser)

Run the dedicated scraper instead of browsing Daily Work Plan by hand — it runs in its own Chrome window (its own profile + debug port 9333, separate from the Claude extension's tab group, so it can't trigger the single-session logout) and costs zero Claude tokens per run:

```
cd ~/focus-triage && source venv/bin/activate && python focus_workplan.py --no-open
```

- First run of the day: Chrome opens on its own; Darrell clicks "Sign On" once if not already logged in (same session FOCUS window he should keep open/logged in all day — see [[project_focus_triage]]).
- Output: a tiered report at `reports/focus_workplan_<date>.txt` (read this instead of re-scraping) plus `.html`, and the raw grouped-by-person JSON at `data/workplan_<date>.json`.
- Tiers: 🔥 hot (prospect, opted in to text) / 🤝 customers (sold-customer check-ups & anniversaries — treat as pre-opted-in, see [[feedback_existing_customer_optin]]) / 📞 call-email-only (prospect, no text status) / ⏸ pending (prospect opt-in pending — skip per [[feedback_skip_optin_no_ask]]) / 🚫 optout.
- Each person's row already groups all their overdue activity types together (e.g. "Day 0 Internet Follow up Email + Day 0 Internet Follow up Call · 9d overdue") — no need to re-derive categories from raw rows.
- Present the tier counts from the report and let Darrell pick which tier/person to work first — don't assume.
- If the report is stale (Darrell worked leads since, or it's a new day), just re-run the script — it's cheap. `--from-json data/workplan_<date>.json` rebuilds the report instantly without touching the browser if you only need to re-tier/re-print.

## Gathering per-lead contact info

- Click the small triangle at the left of a row to expand inline details (Status, Source, Created, Contact phone/email, Trade Vehicle, History Sold/Service) without leaving the list.
- The list is virtualized: expanding/collapsing reflows the list and invalidates prior `find` refs. Re-run `find`/`get_page_text` after each expand rather than reusing old element references.
- Clicking directly on a lead's name instead opens a full two-panel view (Activity panel + Client Record panel) with richer detail — use this when you need CRM Advisor notes, talking points, or duplicate-record flags too.

## Drafting the message

- Write **one shared template**, personalized per lead using whatever's known (first name, vehicle of interest, trade vehicle, lead source). Keep it short enough for SMS (roughly 250 characters or less; FOCUS auto-appends a ~45-character "Reply STOP to OptOut from McElveen Buick GMC" footer).
- Never place a phone call as the outreach method unless Darrell explicitly asks — default channel is email or text.
- **Present all personalized drafts at once as a single batch** — Darrell has explicitly said per-message back-and-forth burns time/tokens.
- **Stop here.** Don't send, don't open Podium, don't log an activity in FOCUS to trigger a sync-over. Hand Darrell the finished batch of drafts (grouped by tier/person) and let him decide how they go out — sending is `~/podium-triage/podium_triage.py`'s job once it's wired in, not this skill's.

## Known blockers — stop and ask Darrell rather than working around these

- **"You cannot send email because you do not have Email set up. Please contact your administrator."** This is an account-level restriction FOCUS shows on the Send Email form. Don't try to force it — surface it and ask whether to switch to text, or wait on the admin to enable email.
- **Unconfirmed "Text Opt In" status.** If the Client Record's Text Opt In doesn't have a "(c)" tag, CRM Advisor shows a "TEXT OPT IN — Apply" suggestion. Don't click it — opting someone in isn't this skill's call. Just skip the lead (per default policy below) and note it as pending.
- **Business/company-type records with no text option at all** (no chat-bubble icon, no "Draft Text" recommended action) — these leads may only be reachable by email, so if email is also down, flag as fully blocked rather than guessing at a workaround.
- Stale element references after list reflow, or "Couldn't determine which page this action targets" errors — re-screenshot or re-run `find`/`get_page_text` rather than retrying the same ref.

## Reporting back

After each batch, summarize: how many drafts are ready (and for whom), how many held/skipped and why, and what decision (if any) is needed from Darrell before sending.

## Default policy (unless Darrell overrides)

When Darrell front-loads a category to work (see phrasing examples below), apply these defaults so no mid-task questions are needed:

- **Channel**: text only. Never touch email setup or attempt email if it's account-blocked — just note it and move on.
- **Opt-in**: skip any lead without confirmed "Text Opt In" — don't click "Apply"/send an opt-in-confirmation request.
- **Output**: draft all messages for the category, present once as a batch, then stop — no sending.
- **Reporting**: end with a short tally — drafted / skipped (and why).

### Example instructions per category (front-loaded, one-shot)

- **Internet/New Lead Follow-ups**: "Work my Internet/New Lead follow-ups: one template personalized per vehicle interest, text-opted-in leads only, skip the rest, batch the drafts and report."
- **PAYOFF Follow Up**: "Work my PAYOFF follow-ups: draft a trade-in/payoff-value message, opted-in leads only, skip the rest, batch and report."
- **1st Xtream Follow Up**: "Work my 1st Xtream follow-ups: draft an appointment-confirmation style message, opted-in leads only, skip the rest, batch and report."
- **Salesperson Day 1 Call**: "Work my Salesperson Day 1 leads: text drafts only (no calls), skip anyone not opted in, batch and report."
- **Post-Sale Check-Ins**: existing customers — treated as pre-opted-in per [[feedback_existing_customer_optin]].
- **Anniversary/Referral/Misc**: "Work these ad hoc: draft appropriate message per lead type, batch, skip unconfirmed opt-in, report."
