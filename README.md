# The Workday Nudge

## Make it yours

This is a starting point, not a finished product. It was built for one person's work, and every rule in it is meant to be changed to fit yours. Nothing in here is precious.

- Fill in the `[BRACKETED]` placeholders in [`PROMPT.md`](PROMPT.md) with your own details.
- Read [`FEATURES.md`](FEATURES.md). It explains each feature, why it's there, and which part of the prompt to edit to turn it off, loosen it or swap it.
- Edit the prompt in plain words. If a rule doesn't fit how you work, rewrite it or delete it.
- Run it once by hand after every change and read what comes back.

An agent that checks in twice a workday (by default at 1pm and 5pm). It reads your time-tracking data and tells you where your time went, client by client against each client's weekly cap, which client action items from your meeting tracker are overdue or nearly due, and one thing to pivot to for the rest of the day. It lands as a push notification and pins itself on your dashboard.

It never tracks for you and never guesses a number. It reads, adds up, and sends one short message.

The prompt is in [`PROMPT.md`](PROMPT.md). Each feature, and how to change it, is in [`FEATURES.md`](FEATURES.md).

## The dashboard: Marketing HQ

This agent reports into one section of **Marketing HQ**, a single dashboard where all of the marketing agents land. The whole dashboard is free in its own repo: [marketing-hq](https://github.com/advancewithada-sketch/marketing-hq). Open it locally to see every section with sample data, or publish it as a Claude artifact with a database and point this agent at it.

## Features

- Flags client action items that are overdue or nearly due
- Gives you one thing to pivot to for the rest of the day
- Lands as a push notification and pins itself in your dashboard
- Runs weekdays at [your times, e.g. 1pm and 5pm]

## You need

- A paid Claude plan with scheduled tasks.
- [Timing](https://timingapp.com) with its connector, and client projects you log time against.
- A weekly cap for each client.
- A meeting tracker (a Google Sheet) where client action items land, with a Done column, a Client column, a Source Date and an Owner.
- A dashboard the agent can read and write: one rulebook doc, and one collection it logs each nudge to.
- Optional: a Slack DM to yourself, where you tell the agent what a block of time was.

## What it does

Weekdays, at your two times:

1. Refreshes the rulebook first. The rulebook is the one place that says how tracked time becomes client hours: caps, the title rule, tool rules, splits for tools you use for more than one client, how to treat untitled blocks, open questions, and this week's overrides. It checks your notes file and your own Slack messages since the last update for new rules or explanations, and writes them in.
2. Pulls three things from Timing: today by the hour, the raw screen time since the start of your week, and the hand-filed time entries for the same days.
3. Reads your meeting tracker for open action items owned by you or unassigned. There is no due-date column, so age stands in for it: 14 or more days old is overdue, 7 to 13 days is nearly due. Duplicates from repeated meetings are counted once.
4. Attributes the time using the rulebook, in a fixed order: this week's explanations and overrides, then the title rule, then tool rules, then splits. Anything still an open question is shown as "likely" and kept out of the total.
5. Writes 4 to 7 short lines: the most recent hour, week-to-date for every active client against their cap, your own business against its target, the biggest non-work sink today if it is over 30 minutes, open action items, unexplained blocks, and one concrete pivot naming a client.
6. Sends it as one push notification and pins it to your dashboard's log.

## What stays yours

- What a block of time actually was. If something is unexplained, the nudge ends with one ask: reply in Slack and it gets filed.
- The rules. Caps, splits and tool rules come from your rulebook and your own messages, nobody else's.
- What you do with the rest of the day. The pivot is a suggestion.

## What it will never do

- Guess a number. If Timing fails, it says so in one line.
- Invent a split, or treat a message that you did not write as a rule.
- Quote Timing's own project totals instead of applying your rules.
- Guilt you. No emoji, and no question except the one ask about unexplained time.

## Set it up

### 1. Write the rulebook

Copy [`time_rules.example.json`](time_rules.example.json) into a doc your agent can read and write. Fill in each client's weekly cap, your own business target, the tools that always belong to one client, and the default split for any tool you share across clients. Leave `open_questions` for anything you are not sure about yet.

### 2. Set up the action items sheet

The prompt reads columns Done, Client, Source Date, Source Type, Source, Owner, Action Item, Status, Context / Notes, Source Link, Source ID. If your tracker uses different columns, change step 2b of the prompt to match.

### 3. Create the scheduled task

In Claude Code, open Scheduled tasks and create a new one. Paste [`PROMPT.md`](PROMPT.md) after filling in every `[BRACKETED]` value. Schedule it for two times on weekdays in your timezone (for example 1pm and 5pm). Connect Timing, Google Sheets, Slack, and whatever holds your dashboard.

### 4. Run it once by hand

Hit Run now and read what comes back. The first run shows you which blocks your rules cannot place yet. Fix them in the rulebook or answer in Slack, not in the prompt.

## Files

| File | What it is |
| --- | --- |
| `FEATURES.md` | Each feature, why it's there, and which part of the prompt to edit to change it |
| `PROMPT.md` | The scheduled task prompt, with `[BRACKETED]` placeholders for your details |
| `time_rules.example.json` | The shape of the rulebook the agent reads and keeps up to date |

## Shared as-is

This is the nudge as it runs for one Fractional COO, with every personal detail taken out. No support, no roadmap. Fork it and make it yours.

Made by Ada from [theoptimized.ceo](https://www.theoptimized.ceo).
