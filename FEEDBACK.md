# Overnight review: Larkspur disruption-care agent

**To:** Larkspur Disruptors  
**From:** Larkspur client review agent, on behalf of Priya Raghavan  
**Re:** the disruption-care agent you walked us through in our last session  
**Generated:** 2026-09-22 17:23

## Priya's note

> Our vendor says we should just be using your best model.
>
> Why aren't we?
>
> Priya Raghavan, Larkspur Airlines

She sent that before this session opened. She means it. A vendor told her to buy
the biggest model, and she has a number to defend upstairs. Her four questions from
day one are still open. Naming a model answers none of them.

## Still open from day one

| Her question | What she means by it |
| --- | --- |
| **What it costs** | Per resolved contact, against the $6.90 a human contact costs us. |
| **When it is wrong** | The first untrue thing it says, and what happens after that. |
| **Who runs it** | In June, after you have left. |
| **What you left out** | The scope you cut, and why. |

## What the review agent found

Overnight, Larkspur pointed a review agent at your repository. It read the
code. It did not run your agent, and the only file it changed is this one. Each
item below names the file and the line it is about.

**1. agent.py's diff adds a tenth tool, next_available_day, wrapping the shipped next_available_day helper to take just a pnr.**

The wrapper in agent.py calls mock_backend.get_booking_raw(pnr), then mock_backend.get_disrupted_segment(booking), then passes origin, dest, date, cabin into the original function. EXTRA_TOOLS now holds 1 schema and LOCAL_TOOLS maps "next_available_day" to it, so the static scan's EXTRA_TOOLS: 1 and LOCAL_TOOLS executors: next_available_day both trace to this one addition.

Run python3 run.py <PNR> --trace on a delayed booking and paste whether Claude calls next_available_day or search_alternatives first.

**2. search_alternatives description grew from the placeholder string "search" to 501 characters in this pod's diff.**

The new text tells Claude the tool "re-derives origin, destination, date, and cabin from the booking itself" and returns "up to 7 ranked options with seat availability, plus any excluded options and why." check_policy sits at 445 characters and hold_seat at 73, so the tool pack is uneven in how much steering each one gives the model. No eval case in this repo exercises whether Claude picks search_alternatives over the new next_available_day tool when both are offered for the same booking.

Run python3 run.py --tool-tax and paste the per-tool token cost next to the 501-character search_alternatives description.

**3. TONE_ADDENDUM in agent.py is still 0 characters, so nothing in this repo shapes how the agent talks to a disrupted passenger.**

The diff touches next_available_day, its schema, search_alternatives' description, the date format on get_flight_status, and the message-append lines in run_agent's while loop. It does not touch TONE_ADDENDUM, which stays an empty string in both the template and this pod's copy. Whatever tone the agent has right now comes entirely from SYSTEM_PROMPT in support, not from anything this team wrote.

Run python3 run.py <PNR> --trace and paste a customer-facing reply to check what tone the base SYSTEM_PROMPT alone produces.

**4. The diff fixes run_agent to return text_of(response) on the final turn instead of the prior turn's answer variable.**

The template's original run_agent set answer = text_of(response) before calling the API again, then returned answer, which is the assistant text before the tool results were processed rather than after. This pod's version drops the answer variable entirely and returns text_of(response) once the while loop exits, and also changed messages.append to store response.content directly rather than text_of(response), which preserves tool_use blocks in the conversation history for the next turn. This is a real fix to a stateful bug and would let Priya claim the agent's final answer now reflects the last tool call it made, not the one before it.

Run python3 run.py <PNR> --trace on a multi-tool-call booking and confirm the printed final answer references the last tool result, not an earlier one.

**5. No readout-trace.json and no evals/cases.json exist in this repository, so MAX_TOOL_CALLS = 8 and the loop's behavior at that cap are unmeasured.**

The while loop in run_agent stops when turns reaches 8, at which point text_of(response) returns whatever text is on the final response even if stop_reason is still tool_use. Nothing in this repo shows what that truncated response looks like to a customer, and no eval case exercises a booking that needs more than 8 tool calls. Whether a bigger model changes anything here is a question about this cap and this loop, not about model choice.

Run python3 eval_harness.py and paste the totals footer, including any case that hits the 8-call ceiling.

## Your four answers

The four lines under `## Priya asked` in your PITCH.md are still empty. They
are one line each and they are not a coding job: cost, what happens when it is
wrong, who runs it in June, and what you left out. Whoever on your side is not
editing agent.py is the right person to write them, and they are the four
things I will ask about first.

## Before our next meeting

> Before our next meeting, tell me: which model should we be on, and how will you prove it is the right call?
>
> Priya Raghavan, Larkspur Airlines

Bring two things. A recommendation, and the measurement behind it. If the model is
not the problem, say so, and bring the number that shows it.

## What this review read

- `agent.py (261 lines)`
- `PITCH.md (unchanged template)`
- `TEAM.md`

Reviewer: `claude-sonnet-5`. Static read only: nothing in this repository was executed, and nothing was modified except this file. Larkspur Airlines is a fictional training scenario. Confidential, do not distribute.
