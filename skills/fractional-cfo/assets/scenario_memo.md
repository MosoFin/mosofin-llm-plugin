# Scenario memo template

For a 13-week cash view or any what-if. The memo makes it impossible to mistake a scenario for a forecast: the label is on every section and every assumption has a source.

---

**SCENARIO — NOT A FORECAST**

**Company:** [display_name] · **Start date:** [YYYY-MM-DD] · **Horizon:** [13 weeks / N months] · **Prepared:** [date] · **Prepared for:** [Name]

## Question

[The decision this scenario informs, e.g. "Can we fund the June payroll run without drawing on the line of credit?"]

## Result under these assumptions

[One sentence. E.g. "Under these assumptions, closing cash dips to $64k in week 9, below the $100k minimum, and recovers in week 11."]

## Starting facts (from the books)

| Item | Amount | Source |
|---|---|---|
| Opening cash | [$] | balance sheet, [date] |
| Open A/R | [$] | aging, [date] |
| Open A/P | [$] | aging, [date] |

## Assumptions

| # | Assumption | Value | Source |
|---|---|---|---|
| 1 | Customers pay at historical median days to pay | [N days] | Historical, confirmed by user |
| 2 | New sales per week | [$] | User |
| 3 | Payroll | [$ on dates] | User |
| … | | | |

**Not included (no confirmed assumption):** [list]

## Weekly view

| Week | Opening | Receipts | Disbursements | Closing | Below minimum? |
|---|---|---|---|---|---|

## Sensitivity (only if requested)

[How the low point moves if one assumption changes, e.g. collections 10 days slower.]

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- Never write "will"; write "under these assumptions".
- Every assumption has a source. An assumption the user didn't confirm is not used.
