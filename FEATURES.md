# Workday Nudge: features and how to change them

Every feature below is a few lines in [`PROMPT.md`](PROMPT.md). Change the words and you change the behaviour. Run the task once by hand after any edit.

## Flags client action items that are overdue or nearly due

**What it does.** STEP 2b reads your meeting tracker sheet, keeps open items owned by you or unassigned, and sorts them by age: 14 or more days old is overdue, 7 to 13 days is nearly due. Items that say the same thing in different words are counted once. STEP 4 turns that into one line with the two oldest items by client.

**Why it's there.** The tracker has no due-date column, so the age of an item stands in for a due date, and the sheet collects duplicates from repeated meetings, which is why they are collapsed.

**How to change it.**
- Turn it off: delete STEP 2b and the "Open client action items" line in STEP 4.
- Loosen or tighten it: change the 14 and 7 day thresholds in STEP 2b.
- Show it on every run: remove "Only on the first run of the day unless something is overdue" from STEP 4.
- Use a real due date: if your tracker has a due-date column, add it to the column list in STEP 2b and replace the age rule with "overdue when the due date has passed, nearly due when it is within [N] days".
- Point it at different items: change the Owner filter in STEP 2b.

## Gives you one thing to pivot to for the rest of the day

**What it does.** STEP 4 ends the message with one concrete pivot for the rest of today (or tomorrow on the late run), naming a client. STEP 6 puts that pivot in the summary it logs.

**Why it's there.** No reason is written down beyond what it does. Keep it if it's useful to you.

**How to change it.**
- Turn it off: delete the "One concrete pivot" line in STEP 4 and remove "the pivot plus" from the summary in STEP 6.
- Swap it: replace the line with whatever you'd rather end on, for example the client furthest under their cap.

## Lands as a push notification and pins itself in your dashboard

**What it does.** STEP 5 sends the message as one push notification. STEP 6 writes the same lines as one new entry in your dashboard's activity log, so you can look back at it.

**Why it's there.** No reason is written down beyond what it does. Keep it if it's useful to you.

**How to change it.**
- Only the push: delete STEP 6.
- Only the dashboard: delete STEP 5.
- Somewhere else: replace STEP 5 with one Slack DM, an email to yourself or a note, and keep it to one destination.

## Runs weekdays at [your times, e.g. 1pm and 5pm]

**What it does.** The schedule is set on the scheduled task itself, not in the prompt text. The prompt's STEP 4 only knows there is a first run and a late run.

**Why it's there.** No reason is written down beyond what it does. Keep it if it's useful to you.

**How to change it.**
- Change the times on the scheduled task. If your workday is different, also change the working hours in STEP 1.
- One run a day: keep one time, and remove "(or tomorrow on the late run)" from STEP 4.

## The rulebook (not a feature, but it runs everything)

STEP 0 and STEP 3 decide how tracked time becomes client hours. Change caps, tool rules and splits in your rulebook doc (see [`time_rules.example.json`](time_rules.example.json)), not in the prompt. If you don't want the agent updating the rulebook from Slack, delete STEP 0 c).
