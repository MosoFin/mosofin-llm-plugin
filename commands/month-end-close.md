---
description: Month-end close review — readiness checklist, trial balance tie-out, balance sheet review, P&L flux and a sign-off summary
argument-hint: "[company and month, optional]"
---

Run a month-end close review for: $ARGUMENTS (default: the confirmed company, last complete month).

1. Run the `bookkeeper` skill's "Month-end close readiness" workflow.
2. Run the `controller` skill's "Trial balance tie-out", "Balance sheet review" and "P&L flux analysis" workflows, in that order. If the trial balance doesn't tie or suspense is material, say so before the flux commentary.
3. Finish with `skills/controller/assets/close_signoff_summary.md`.

Read-only: proposed entries are review-only suggestions for a person to book. Never call the period closed or reconciled.
