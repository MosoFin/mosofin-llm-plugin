# A/R aging review template

The month-end A/R note for a reviewer or owner. It answers three things: how much is owed and how old it is, whether the aging agrees with the books, and which balances need a person before anyone contacts a customer.

---

**Company:** [display_name] · **Workspace:** [Workspace name] · **As of:** [YYYY-MM-DD] · **Aged by:** [due date / invoice date]

## Headline

[One sentence. E.g. "A/R is $412k, 27% past due; $61k is over 90 days and two customers hold most of it. The aging ties to the balance sheet."]

## Aging by bucket

| Bucket | Balance | % of total | Change vs [prior month end] |
|---|---|---|---|
| Current | [$] | [%] | [$] |
| 1–30 | [$] | [%] | [$] |
| 31–60 | [$] | [%] | [$] |
| 61–90 | [$] | [%] | [$] |
| 90+ | [$] | [%] | [$] |
| **Total** | [$] | 100% | [$] |

## Control-account tie

Aging total [$] vs balance sheet A/R [$] → difference [$]. [If non-zero: likely cause from the records, labelled observed / possible / unknown.]

## Exceptions for review before contact

| Customer | Item | Amount | Why it matters | Owner |
|---|---|---|---|---|
| [Customer] | [Unapplied payment / open credit / credit balance / dispute] | [$] | [Net supported balance is $0 — do not chase] | [Name] |

## Top past-due customers (net)

| Customer | Net past due | Oldest invoice | Last payment |
|---|---|---|---|

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- Report net balances. Gross balances belong only in the exceptions table.
- A tie difference is a finding, stated with both amounts.
- Never describe a customer as "bad", "delinquent" or "high risk" here; state days past due and amounts.
