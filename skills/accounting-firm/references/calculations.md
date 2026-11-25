# Calculation rules — accounting firm workflows

This plugin ships no executable code. Do these calculations in chat from each client's pulled reports, show the inputs next to each result, and state the client, basis and dates. Never compute a figure from two clients' data unless the workflow is an explicitly labelled comparison.

## Materiality per client

Use the client's review profile. Defaults when none is saved: `pct_threshold = 10%`; `amount_threshold = 1% of the client's revenue for the period` (P&L) or `1% of total assets` (balance sheet), with a floor of 500 in the client's currency. Say which defaults you used.

## Portfolio flags

Flag a client when any of these hold (last complete month vs the month before unless the user set another period):

| Flag | Condition |
|---|---|
| Net income swing | `|Δ net income| > 30%` of the prior month and above materiality |
| Margin drop | Gross margin % down ≥ 5 points |
| Negative cash | Any bank account on the balance sheet below zero |
| Old receivables | A/R over 90 days > 20% of total A/R, or over 60 days > 35% |
| Overdue payables | A/P past due > 30% of total A/P, or any bill over 90 days above materiality |
| Suspense | Uncategorized / suspense / "ask my accountant" balance above materiality |
| Undeposited funds | Payments older than 7 days still undeposited |
| TB out of balance | `Σ debits − Σ credits ≠ 0` |
| Stale books | No transactions in the last 30 days for a client that normally has weekly activity |
| Not read | Company disconnected, policy-disabled or access error — never treated as clean |

Ignore swings on clients with fewer than ~10 transactions in the month; mark them "thin month".

## Month-end step tests

| Step | Done when |
|---|---|
| Scope confirmed | Workspace, company, period, basis read back and confirmed |
| Dates and basis valid | P&L and balance sheet pulled for the same period end and basis |
| Material changes identified | Every material P&L line has an observed / possible / unknown driver |
| Cash and receivables reviewed | No negative cash; A/R aging agrees to control account; old items listed |
| Missing support recorded | Each flagged balance names the document needed |
| Follow-up list | Every open item has an owner and a due date |

## Cleanup sizing

- **Last normal month:** the last month whose transaction count is ≥ 50% of the trailing 12-month median.
- **Months behind:** complete months between the last normal month and now.
- **Uncategorized volume:** count and amount of transactions in uncategorized / suspense / "ask my accountant" accounts, per month.
- **Undeposited funds age:** oldest undeposited item and total over 30 days.
- **Structural issues:** opening balance equity ≠ 0, negative asset or liability balances with the wrong sign, A/R or A/P items older than 365 days, TB out of balance, retained earnings not rolled.
- **Effort drivers:** months behind × monthly transaction volume, plus uncategorized count, plus structural issues. Report volumes; leave pricing to the user.

## Client KPIs

- Revenue growth = `(current − prior) / prior` for the same period length.
- Gross margin % = `(revenue − cost of sales) / revenue`.
- Net margin % = `net income / revenue`.
- Operating expense ratio = `operating expenses / revenue`.
- DSO = `A/R at period end / revenue for the period × days in period`.
- DPO = `A/P at period end / (cost of sales + operating expenses for the period) × days in period`.
- Current ratio = `current assets / current liabilities`.
- Cash runway (months) = `cash / average monthly net cash outflow` over the last 3 months; "not burning" when outflow ≤ 0.
- Top-customer share = largest customer's revenue / total revenue.

Book statistics: median and quartiles across the selected clients, always with `n`. Don't report a median for fewer than 5 clients; list the values instead.

## Anonymization

In anything that might be shared outside the firm: replace client names with "Client A, B, C…" (or an industry label), round figures to two significant digits or show only ratios, drop any detail (vendor, customer, city) that could identify a client, and keep the focus client as "You" only in that client's own copy.

## Year-end checks

| Check | Ready when |
|---|---|
| TB ties | `Σ debits = Σ credits`; TB net income = P&L net income |
| Suspense cleared | Uncategorized / suspense / "ask my accountant" = 0 |
| Opening balance equity | = 0 |
| Retained earnings rolled | Opening retained earnings = prior year closing retained earnings + prior year net income − distributions |
| Owner equity activity | Draws / contributions in equity accounts, not expenses or income |
| Fixed assets and loans | Additions and loan balances present where the GL shows purchases or loan payments |
| Old A/R and A/P | No items older than 365 days without a decision |
| Vendor tax info | Vendors paid ≥ the reporting threshold the user or preparer confirms have tax ID / 1099 fields filled, as far as the vendor record shows |
