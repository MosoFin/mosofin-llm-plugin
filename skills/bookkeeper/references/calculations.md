# Calculation rules — bookkeeper workflows

This plugin ships no executable code. Do these calculations in chat from the pulled records, show the inputs next to each result, and state the basis and dates.

## Materiality

Use the review profile's threshold. Default when none is saved: the greater of **$100** and **0.1% of period revenue** for item-level cleanup; say which default you used. Items below it are still listed in the workbook but not in the summary.

## Close readiness checks

| Check | Pass when |
|---|---|
| Trial balance | `Σ debits − Σ credits = 0` |
| Holding accounts | Uncategorized income, uncategorized expense, suspense, "ask my accountant" and similar are all zero at period end |
| Undeposited funds | No item older than 7 days at period end |
| A/R aging vs control | `aging total = A/R control account` as of the same date |
| A/P aging vs control | `aging total = A/P control account` as of the same date |
| Unapplied items | No unapplied customer payment, customer credit or vendor credit above materiality |
| Journal entries | Every entry in the period has a memo |
| Opening balance equity | Zero after the company's first fiscal year |

Status: **Ready** = all pass; **Ready with open items** = only items below materiality fail; **Not ready** = anything else.

## Suggested account

For a transaction in a holding account, look at the same payee's transactions in the prior 12 months:
- **Observed** suggestion: ≥ 80% of the payee's prior amount went to one account.
- **Possible** suggestion: a most-common account exists but below 80%, or no history and the payee name clearly implies a category.
- **Unknown**: no history and no clear payee → client question.

## Duplicate matching

Normalize payee (case, punctuation, suffixes like "Inc", "LLC") and amount (to the cent).
- **Strong** candidate: same payee, same amount, dates within 7 days, and same reference number or one missing.
- **Weak** candidate: same payee, same amount, dates within 30 days, different references.
- **Exclude** when the payee has the same amount on a regular cycle (≥ 3 prior occurrences spaced 25–35 days or 6–8 days apart) unless two fall in the same cycle.
- Payments: same payee and amount within 7 days, or one payment applied to two bills that sum to more than the bill total.

## Undeposited and unapplied aging

`age_days = period_end − payment_date`. Buckets: 0–7, 8–30, 31–60, 60+. A **likely missed application** is an unapplied credit or payment within ±1% of an open invoice (or bill) for the same customer (or vendor).

## Journal entry flags

- No memo / description.
- Touches cash, A/R, A/P, undeposited funds, payroll liabilities or sales tax payable.
- `amount ≥ materiality × 10`, or a multiple of 1,000 above materiality.
- Dated on the last day of the period or after it, or created after the period end date.
- Marked reversing with no reversal in the next period.
- Touches equity, opening balance equity or retained earnings directly.
- Posts to an account marked inactive.

## List hygiene

- **Near-duplicate names:** equal after normalization, or one is a prefix of the other, or they differ by at most two characters in names longer than 6 characters.
- **Inactive with activity:** inactive record with a non-zero balance or a transaction in the last 90 days.
- **Missing details:** active customer without email or billing address; active vendor without address or payment terms.
- **Year-end information return readiness** (only with fields the catalog returns): vendor paid `≥` the reporting threshold the accountant sets (US default to confirm with the accountant: $600 for most service payments) in the calendar year via non-card methods, missing tax ID, legal name, address or the 1099 flag. If payment method isn't available, say card payments couldn't be excluded.

## Activity sanity bands

- `trailing_avg` = mean of the prior 3 (or 6) complete months.
- Flag the period when `|period − trailing_avg| / trailing_avg > 30%` and the difference exceeds materiality.
- Flag a **gap** when an account or payee with activity in every one of the trailing months has none in the period, or a calendar week with no deposits on an account that averages ≥ 2 deposits a week.
