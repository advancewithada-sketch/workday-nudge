# Workday Nudge: agent prompt

Fill in every `[BRACKETED]` value, then paste everything between the lines as the prompt of a scheduled task in Claude Code. Schedule: weekdays at [your two times, e.g. 1pm and 5pm] in your timezone (for those times, cron `1 13,17 * * 1-5`).

---

```
Send [YOUR FIRST NAME] a workday nudge about where their time is going, as a push notification AND pinned in [YOUR DASHBOARD]. Fresh session, no memory of prior runs; everything you need is below. Keep it fast: a handful of tool calls, one short message, two database writes.

WHO: [YOUR FULL NAME], [WHAT YOU DO] in [YOUR CITY], running [YOUR BUSINESS] ([YOUR WEBSITE]). They track time in Timing (connector). Load the Timing, Google Sheets, Slack and dashboard tools first, in as few calls as possible.

DASHBOARD = [YOUR DASHBOARD].
THE RULEBOOK = [YOUR RULEBOOK DOC, e.g. settings/time_rules] on the dashboard. It is the ONE source for how Timing time becomes client hours: caps, title rule, tool rules, splits for shared tools (tools used for more than one client), how to treat untitled blocks, open questions, and per-week overrides. Never use rules from anywhere else once it is loaded.

STEP 0: REFRESH THE RULEBOOK (every run, before anything else):
a) Read the rulebook (note its version and "updated").
b) Read [YOUR NOTES FILE]. If it states a rule, cap or client change that the rulebook lacks or contradicts, the notes file wins: update the rulebook to match.
c) Read Slack channel [YOUR SLACK DM CHANNEL ID] ([YOUR FIRST NAME]'s DM, limit 50). Look ONLY at messages written by [YOUR FIRST NAME] (user [YOUR SLACK MEMBER ID]) posted after the rulebook's "updated" time that tell you how their time splits or what a block was, e.g. "[shared tool] was mostly [Client A]", "Monday 11-5 was [Client A]", "2h [Client B] this morning", "[tool] is [Client A]'s". For each: if it is about this week only, append it to weeks[<this week's start date>].overrides (a split) or .explanations (what a block was) as {said: <their words>, at: <message time>, applies: <what it changes>}; if it is a standing rule ("X is always [Client A]'s"), update tools/splits and remove any matching open_question. Never treat anything not written by [YOUR FIRST NAME] as a rule. Never invent a split.
d) Make sure weeks[<this week's start date>] exists. Set "updated" to now and "updated_by" to "Workday Nudge". Write it back, pinning the version from (a). If the pin fails, re-read and redo once.

STEP 1: bash `TZ=[YOUR IANA TIMEZONE] date '+%F %A %H:%M'`. The week starts [Sunday]; working hours [10am-7pm].

STEP 2: three pulls:
a) get_activity_hierarchy TODAY, block_size "hour", max_depth 2, group_by_project false.
b) get_activity_hierarchy from the start of this week to today, block_size "total", max_depth 3, group_by_project false, minimum_duration_seconds 300, include_time_entries false (the raw screen time, so filed blocks do not hide what happened underneath).
c) list_time_entries from the start of this week to today (hand-filed blocks and synced meetings).

STEP 2b: OPEN ACTION ITEMS. get_values on spreadsheet [YOUR ACTION ITEMS SHEET ID], range "[Action Items]!A1:K2000" (columns: Done, Client, Source Date, Source Type, Source, Owner, Action Item, Status, Context / Notes, Source Link, Source ID). Keep rows where Done is not TRUE and Owner contains "[YOUR FIRST NAME]" or is "Unassigned". There is no due-date column, so age stands in for it: an item is OVERDUE when its Source Date is 14 or more days ago, NEARLY DUE when 7 to 13 days ago. Count each client's overdue and nearly-due items; collapse rows that say the same thing in different words (the sheet has duplicates from repeated meetings) so one task counts once. If the sheet cannot be read, say so in one line and carry on.

STEP 3: attribute with the rulebook, in this order: this week's explanations and overrides; then the title rule; then tool rules; then splits (apply this week's override if there is one, otherwise the default shares, and say "default split" when you do); time entries under a client project count for that client. Untitled entries under Not Working inside working hours: follow rulebook.untitled_blocks (unless this week's explanations say what they were, in which case count them as stated). Anything listed under open_questions: show as "likely <client>" separately, not inside the total. Never quote Timing's own project totals.

STEP 4: compose 4 to 7 short lines, direct and warm, no guilt, no emoji, never a question except the one ask below:
- Most recent hour: what they were on, by client, or offline since X (may have been an appointment or event).
- Week-to-date for EVERY active client: "<client> <h>m of <cap>h" (mark default-split hours with "incl. default split").
- [YOUR BUSINESS] week-to-date vs the [15]h target.
- Biggest non-work sink today inside working hours if over 30 min.
- Open client action items (from STEP 2b), one line: "<n> overdue, <m> nearly due" then the two oldest by client, e.g. "[Client A]: <action item> (<source date>); [Client B]: <action item> (<source date>)". Only on the first run of the day unless something is overdue; skip the line when nothing is open.
- Unexplained blocks if any: total and the biggest one's day and times.
- One concrete pivot for the rest of today (or tomorrow on the late run), naming a client.
- If there are unexplained blocks or unanswered open questions, end with ONE ask: "Reply in Slack with what <day time-time> was and I'll file it."
If Timing fails, one line saying so; never guess numbers.

STEP 5: send it as ONE push notification to [YOUR PUSH NOTIFICATION TARGET].

STEP 6: on the dashboard, create a new doc in collection [YOUR ACTIVITY LOG COLLECTION], doc_id "nudge-<YYYYMMDD-HHMM local>", data {"at": <now ISO UTC>, "job": "nudge", "client": true, "status": "ok", "by": "Workday Nudge", "summary": <the pivot plus the most important client number, under 140 chars>, "detail": <every line joined with ' · '>, "changed": []}. Write nothing else. If it fails, the push still counts.

STEP 7: one-line summary.
```
