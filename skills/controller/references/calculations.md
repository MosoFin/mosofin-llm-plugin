# Calculation rules — controller workflows

This plugin ships no executable code. Do these calculations in chat from the pulled reports, show the inputs next to each result, and state the basis and dates.

## Variance and materiality

- `change = current − comparison`
- `change_pct = change / |comparison|`. Leave blank when `comparison = 0` or the sign flips; say "n/m" (not meaningful).
- **Material** when `|change| ≥ amount_threshold` **and** `|change_pct| ≥ pct_threshold`.
  - Defaults when the review profile has none: `pct_threshold = 10%`; `amount_threshold = 1% of revenue for the period` (P&L lines) or `1% of total assets` (balance sheet lines). Say which defaults you used.
- **Favourable** depends on account type: income up / expense down / liability down is favourable.

## Reclass detection

Two lines move in opposite directions by amounts within 5% of each other, in the same period, and the general ledger shows journal entries between them. Report the pair once as a reclass with net effect, not as two variances.

## Balance sheet checks

| Check | Flag when |
|---|---|
| Unexpected sign | A/R, cash, inventory, prepaid or fixed assets negative; A/P, credit cards, loans or payroll liabilities debit balance |
| Suspense | Any balance in uncategorized, suspense, "ask my accountant" or similar |
| Undeposited funds | Non-zero balance made of payments older than 7 days |
| Opening balance equity | Non-zero after the first year |
| Aging vs control | `|aging total − control account| > 0` as of the same date |
| Large movement | Material per the rule above, against prior period end |
| Stale balance | Accrual, prepaid or clearing account unchanged for 3+ periods |

## Trial balance checks

1. `Σ debits − Σ credits = 0`.
2. `Σ income − Σ expense (TB) = P&L net income` for the same dates and basis.
3. TB balance sheet accounts = balance sheet line totals.
4. Balance sheet current-year earnings = P&L net income from fiscal year start to period end.

Report each with both amounts and the difference.

## Recurring cost expectations

A vendor is **recurring** when it has a bill or purchase in at least 3 of the last 4 complete months (or 5 of the last 6). Its **expected amount** is the median of those months. A recurring vendor with nothing dated in the reviewed period is a **candidate accrual** at the expected amount.

## Unusual expense tests

- New vendor with period spend above `amount_threshold`.
- Vendor spend `≥ 2×` its trailing 3-month average and above `amount_threshold`.
- Single transaction `≥ 3×` the vendor's median transaction.
- Exact duplicates: same vendor, same amount, within 14 days, different reference numbers.
- Round amounts (multiples of 1,000) above `amount_threshold`.
- Weekend or public-holiday dates for vendors that usually transact on weekdays.
- Account differs from the account used for that vendor in most prior transactions.

## Margin

- `gross_margin = revenue − cost_of_sales`; `gross_margin_pct = gross_margin / revenue`.
- Blended margin = totals across all customers or items, not the average of percentages.
- Ignore customers or items under 1% of revenue in rankings; group them as "Other".
