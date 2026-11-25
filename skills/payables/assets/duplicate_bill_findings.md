# Duplicate bill findings template

Candidate duplicate bills and payments, side by side, so a person can check each against the vendor's invoice. Nothing here is confirmed until they do.

---

**Company:** [display_name] · **Bills scanned:** [dates] · **Payments scanned:** [dates]

## Summary

[One sentence. E.g. "4 strong and 3 weak candidates, $12,480 in total; two strong candidates are unpaid and are held from the payment run."]

## Candidates

| # | Strength | Vendor | Record A (no. · date · amount · status) | Record B (no. · date · amount · status) | Matching on | Next step | Owner |
|---|---|---|---|---|---|---|---|
| 1 | Strong / Weak | [Vendor] | [No. · YYYY-MM-DD · $ · Open/Paid] | [No. · YYYY-MM-DD · $ · Open/Paid] | [Same no. / same amount within n days] | [Hold and check invoice / Request credit] | [Name] |

## Over-payments

| Vendor | Bill no. | Bill amount | Payments applied | Excess |
|---|---|---|---|---|

## Excluded as regular recurring charges

[Vendors with same-amount charges on a regular cycle, listed once so reviewers know they were considered.]

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- "Candidate", never "duplicate". A person confirms.
- Show both records in full. The deciding detail is usually the bill number or the memo.
- Credit or refund requests to vendors are drafted and left unsent.
