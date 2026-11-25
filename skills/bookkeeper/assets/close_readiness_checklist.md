# Close readiness checklist template

The checklist a bookkeeper hands the reviewer. It answers one question — can review start? — and lists exactly what stands in the way.

---

**Company:** [display_name] · **Workspace:** [Workspace name]
**Period:** [dates] · **Basis:** [Accrual / Cash] · **Prepared:** [date]
**Materiality used:** [amount] ([from review profile / default])

## Status: [Ready / Ready with open items / Not ready]

[One sentence. E.g. "Not ready: $4,180 still uncategorized and two customer payments ($3,250) sit in undeposited funds from June."]

## Checks

| Check | Result | Amount | Note |
|---|---|---|---|
| Trial balance balances | Pass / Fail | [difference] | |
| Uncategorized / suspense / "ask my accountant" cleared | Pass / Fail | [$ remaining] | [N items] |
| Undeposited funds: nothing older than 7 days | Pass / Fail | [$] | [oldest date] |
| A/R aging agrees to control | Pass / Fail | [$ difference] | |
| A/P aging agrees to control | Pass / Fail | [$ difference] | |
| No unapplied payments or credits above materiality | Pass / Fail | [$] | [N items] |
| Journal entries all have memos | Pass / Fail | | [N without memo] |
| Opening balance equity is zero | Pass / Fail / n/a | [$] | |

## Open items

| # | Item | Amount | Owner | Due |
|---|---|---|---|---|

## Outside what MosoFin can see

Bank and credit card reconciliations, the bank feed "For review" queue, payroll provider reports and source documents. A pass above is **not** a reconciliation.

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- The status line is the one the reviewer reads. Give the amount and the reason.
- Every failed check has an owner and a due date, or it will be there next month too.
- Don't soften "Not ready". It's useful information, not a judgment of the preparer.
