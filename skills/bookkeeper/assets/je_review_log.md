# Journal entry review log template

A log of the period's manual journal entries, so the reviewer can see each one's purpose and support at a glance.

---

**Company:** [display_name] · **Period:** [dates] · **Entries reviewed:** [N] · **Flagged:** [N]

| # | Date | Ref | Accounts (Dr / Cr) | Amount | Memo | Flags | Support | Question for preparer |
|---|---|---|---|---|---|---|---|---|
| 1 | [YYYY-MM-DD] | [JE number] | [Dr Account / Cr Account] | [$] | [Memo or "none"] | [e.g. No memo; touches A/R] | Seen in records / Needed | [Specific question] |

## Recurring entries

[Entries that repeat every period (depreciation, prepaid amortization, payroll accrual) with whether this period's amount matches the usual amount.]

## Cleared

[Flagged entries explained during review, one line each.]

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- A flag describes a property of the entry ("touches cash", "no memo"), not a judgment.
- "Support: Seen in records" only when the retrieved records show it (for example a linked transaction). MosoFin can't see attachments or source documents.
- Questions go to whoever booked the entry, by role or name, never to the client.
