---
description: Two-minute morning cash check — cash balance, what came in and went out, A/R past due, bills due this week, and anything that needs a look. Safe to schedule.
argument-hint: "[company, optional]"
---

Run a read-only morning cash check for: $ARGUMENTS (default: the confirmed company; if none is confirmed and the user has several, list them and stop). Load the review profile first (`review-profile` skill) if one is saved. Don't ask questions mid-run, so it works as a scheduled task; if a workspace or company choice is required, stop and say which choice is needed.

1. Cash: balance sheet cash and bank accounts as of today vs 7 days ago, and against any saved minimum cash.
2. In and out: customer payments and deposits received, and bills and purchases paid, in the last business day and the last 7 days.
3. A/R: total past due, and new items crossing 30, 60 or 90 days.
4. A/P: bills due in the next 7 days, and any already overdue.
5. Hygiene: new uncategorized or suspense balances since the last check.

Open with **All clear** or **Needs a look (N)**, then a short table and at most 3 bullets. Never change anything in the books. End with the **Data sources** line.

At the end, if the user isn't already running this on a schedule, offer once: "Want this every weekday morning? In the Claude desktop app, open the Code tab, then Scheduled > New task, pick a local folder, choose Weekdays and use `/mosofin:daily-cash-check` as the instructions. Click Run now once and always-allow the MosoFin tools so later runs don't stop for approval."
