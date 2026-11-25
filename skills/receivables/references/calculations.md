# Calculation rules — receivables workflows

This plugin ships no executable code. Do these calculations in chat from the pulled reports, show the inputs next to each result, and state the as-of date.

## Aging buckets

Bucket by **days past due** (`as_of_date − due_date`): Current (≤ 0), 1–30, 31–60, 61–90, 90+. If the report aged by invoice date, say so and don't mix the two.

## Control-account tie

`difference = aging total − balance sheet A/R` as of the same date. Any non-zero difference is a finding. Common causes: journal entries to A/R without a customer, multiple A/R accounts, different as-of dates or basis.

## Net supported balance

Per customer: `open invoices − unapplied payments − open credit memos`. Only invoices and credits visible in the retrieved records count. Net ≤ 0 means **do not chase**; a negative net is a customer credit to review.

## Priority score

Rank customers with net past-due balance above the minimum (default: max(100, 0.5% of total A/R)):

`score = net_past_due × age_weight × (1 + no_recent_payment)`

- `age_weight`: 1–30 = 1, 31–60 = 2, 61–90 = 3, 90+ = 4 (use the oldest invoice's bucket).
- `no_recent_payment`: 1 if no payment in the last 45 days, else 0.

Ties: larger balance first. Show the inputs, not just the score.

## DSO

- Monthly: `DSO = ending A/R / revenue for the month × days in month`.
- Trailing 3-month: `ending A/R / revenue for the last 3 months × days in those 3 months`.
- Accrual revenue only. Flag months where revenue fell by more than 20%: DSO rises mechanically.

## Days to pay

Per paid invoice: `days_to_pay = payment_date − invoice_date`; `days_late = payment_date − due_date` (floor at 0). Per customer: count, mean, median, and the same for the prior period. Ignore customers with fewer than 3 paid invoices in rankings.

## Concentration

- `share_i = customer revenue / total revenue` (12 months).
- Top-1 and top-5 share. Flag top-1 ≥ 20% or top-5 ≥ 50%.
- `HHI = Σ (share_i × 100)²`. Under 1,500 low, 1,500–2,500 moderate, over 2,500 high.
- Repeat on A/R balances: a customer whose A/R share is far above its revenue share is paying slower than others.

## Early-warning signals

Per customer, flag when (each is an **observed** signal, not a credit judgment):

- Median days to pay up ≥ 15 days vs the prior period.
- Balance moved into 61+ days for the first time.
- Partial payments replacing full payments.
- Revenue up while payments down over the same 3 months.
- Credit limit exceeded, if the books carry one.

## Unapplied matching

For each unapplied payment or open credit: look for open invoices for the same customer with (1) the exact amount, (2) a combination of up to three invoices summing to the amount, (3) an amount within 2% (possible fee or discount). Report confidence as exact / partial / none.
