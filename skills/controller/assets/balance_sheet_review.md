# Balance sheet review template

Line-by-line review notes for the balance sheet at period end. Clean lines get one word; flagged lines get the evidence and a named follow-up.

---

**Company:** [display_name] · **As of:** [period end] vs [prior period end] · **Basis:** [Accrual / Cash]

## Summary

[One or two sentences: how many lines checked, how many flagged, the most important flag.]

## Review table

| Line | Balance | Prior | Movement | Check | Result | Note | Follow-up owner |
|---|---|---|---|---|---|---|---|
| Cash — [account] | [$] | [$] | [$] | Sign, movement | OK / Flag | [Note] | [Name] |
| Accounts receivable | [$] | [$] | [$] | Aging vs control | OK / Flag | [Aging total $ vs control $] | [Name] |
| Undeposited funds | [$] | [$] | [$] | Age of items | OK / Flag | [Oldest item date] | [Name] |
| [Suspense / uncategorized] | [$] | [$] | [$] | Should be zero | Flag | [What's in it] | [Name] |
| Accounts payable | [$] | [$] | [$] | Aging vs control | OK / Flag | [Note] | [Name] |
| Opening balance equity | [$] | [$] | [$] | Should be zero | OK / Flag | [Note] | [Name] |

## Support a reviewer should obtain

- [Line]: [document, e.g. bank statement for account ending ••12 as of period end]. MosoFin can't see this; the balance is **not** confirmed as reconciled.

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- "OK" means the check passed on the data MosoFin can read. It does not mean reconciled or audited.
- Mask account numbers to the last two or four digits.
- Every flag has an owner. A flag without an owner doesn't get fixed.
