# Payment run proposal template

A proposed list of bills to pay, for the approver. It is **not** a payment: MosoFin can't pay, schedule or approve. The approver decides and executes in their own payment system.

The approver wants three things fast: the total, why each bill is in, and what was left out.

---

**Company:** [display_name] · **Run date:** [YYYY-MM-DD] · **A/P as of:** [date] · **Basis:** [Accrual / Cash]
**Cash limit:** [$ — user's stated amount / book cash minus [$] buffer (books, not bank)]

## Headline

[One sentence. E.g. "Proposing 18 bills totalling $41,320 against a $45,000 limit; clears everything 61+ days overdue and all payroll-related vendors, holds two candidate duplicates."]

## Proposed for approval

| # | Vendor | Bill no. | Due date | Days past due | Amount | Credits netted | To pay | Reason |
|---|---|---|---|---|---|---|---|---|
| 1 | [Vendor] | [No.] | [YYYY-MM-DD] | [n] | [$] | [$] | [$] | [Critical / 61+ overdue / Due this week / Discount] |
| | | | | | | | **[$ total]** | |

## Held

| Vendor | Bill no. | Amount | Why held |
|---|---|---|---|
| [Vendor] | [No.] | [$] | [Candidate duplicate / Disputed / Over the limit] |

## After this run

Remaining open A/P: [$] · Still overdue: [$] · Next due date cluster: [dates, $]

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

*Proposal for approval — no payment has been made.*

---

## Tone notes

- Every bill has a reason. "Due" alone isn't a reason when cash is short; say which priority rule put it in.
- Show held bills. An approver who doesn't see them can't overrule the hold.
- Say "book cash" when the limit comes from the balance sheet; it's not the bank balance.
