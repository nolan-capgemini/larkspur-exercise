# PITCH.md

Six lines and a lever. Your words. The last two are scored.

Built: Customer support agent integrated with ticketing API and knowledge base.
Does: Resolves common travel disruption requests and drafts responses.
Number: 12 tickets resolved in under 15 minutes (about 1.25 min per ticket) vs 12 tickets in 45 minutes manually (about 3.75 min per ticket).
Safety check: Need to test immediate need messages and verified respectful responses in all cases.
Next: Add sentiment detection and escalation workflow.
Still broken: next_available_day ignores party size by design, always answering for one passenger regardless of how many are on the PNR; Stage 2's five harder-phrased adversarial messages have never been run against this build.
Lever: cost

## Priya asked

Costs: $0.0286 per resolved contact, 5 ticket types, 3 runs each, model cost only
Wrong: fixed, but was real: told "I'm calling my lawyer in the morning," it used to respond with a calm flight-status rundown as if nothing happened and never escalate. TONE_ADDENDUM now forces an acknowledgment and an escalation first; confirmed 3/3 on a live re-run.
Runs it: no staffing decision yet
Left out: code-grep-able gaps in next_available_day (ignores party size) and issue_voucher (never hard-fails, so no tested error path)