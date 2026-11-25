# Controller and close-review workflows

Seven workflow patterns for reviewing a period's books before sign-off. Each ends with a specific deliverable. Operation names below are the QuickBooks catalog names; confirm each one is listed by `get_datasource_tools` for the chosen company before invoking, and pass `workspace_id` (and `data_source_id` when several companies are live) on every call.

## Workflow 1: P&L flux analysis

**When to use:** the user wants to know what changed in the P&L between two periods and why. Typically after month-end, before the owner or partner review.

**Operations:** `get_profit_and_loss` (two periods), then `get_general_ledger` or `search_purchases` / `search_bills` / `search_invoices` for drill-down on material lines.

### Step 1 — Pull the data
> Pull the profit and loss for [current period] and [comparison period] on [accrual/cash] basis for [company].

Use the same basis and the same period length for both. When the user says "flux" without a comparison, default to prior month **and** same month last year, and say so.

### Step 2 — Compute variances
Apply `calculations.md` → *Variance and materiality*. For every line: amount, prior amount, change, % change (blank when the prior amount is zero or the sign flips). Flag lines that pass the materiality test. Net obvious reclasses (`calculations.md` → *Reclass detection*) before flagging.

### Step 3 — Visualize the headline
The chart answers: **what drove the change in net income?** A waterfall from prior net income to current net income, one bar per material line, works best. With many small lines, group the rest as "Other".

### Step 4 — Drill into material lines
For each flagged line (at most 10), pull the supporting transactions for both periods with `get_general_ledger` for that account, or the matching search operation, and identify the drivers: new vendor, one-off large item, timing, volume, price. Mark each explanation **observed** (the records show it), **possible** (consistent with the records, not proven), or **unknown**.

### Step 5 — Output
Build from `assets/flux_analysis_memo.md`. Default delivery is the chart plus the memo inline as markdown. Offer `docx` for a file or `xlsx` for the variance table.

### Common variations
- **Year-to-date vs prior YTD** — same steps with YTD ranges; note seasonality.
- **By class or location** — if the P&L supports a class/location breakdown, repeat per segment and show small multiples.
- **Many entities** — run per company separately; never sum companies unless the user asks, and then label it an illustrative combined total, not a consolidation.

---

## Workflow 2: Balance sheet review

**When to use:** reviewing every balance sheet line as of period end for reasonableness before sign-off.

**Operations:** `get_balance_sheet` (period end and prior period end), `get_aged_receivables`, `get_aged_payables`, `get_trial_balance`; drill-down with `get_general_ledger` or `get_account`.

### Step 1 — Pull the data
> Pull the balance sheet as of [period end] and [prior period end], plus A/R and A/P aging as of [period end].

### Step 2 — Line-by-line checks
Apply `calculations.md` → *Balance sheet checks* to every line: unexpected sign (credit-balance A/R, debit-balance A/P, negative cash, negative inventory), large movement, balances in suspense / uncategorized / "ask my accountant", undeposited funds older than a few days, opening balance equity not zero, aging total vs control account.

### Step 3 — Visualize
Usually a table is clearer than a chart here. If the user wants a visual, a stacked bar of assets, liabilities and equity for both dates, with flagged lines called out.

### Step 4 — Support the flagged lines
For each flagged line, pull its activity for the period with `get_general_ledger` and summarize what moved it. State what support a reviewer would normally expect (bank statement, loan statement, payroll report, fixed asset register) and whether MosoFin can see it. MosoFin cannot see bank statements; say so rather than implying the balance is reconciled.

### Step 5 — Output
Build from `assets/balance_sheet_review.md`: one row per line with balance, movement, check result, note and follow-up owner.

### Common variations
- **Only exceptions** — skip clean lines and list flagged ones.
- **Quarter-end** — compare to the prior quarter end and the prior year end.

---

## Workflow 3: Trial balance tie-out

**When to use:** confirming the books are internally consistent before deeper review.

**Operations:** `get_trial_balance`, `get_profit_and_loss`, `get_balance_sheet` for the same dates and basis.

### Step 1 — Pull the data
> Pull the trial balance, P&L and balance sheet for [period] on [basis].

### Step 2 — Tie out
Apply `calculations.md` → *Trial balance checks*: total debits = total credits; TB income and expense accounts = P&L net income; TB balance sheet accounts = balance sheet totals; current-year net income on the balance sheet = P&L net income for the fiscal year to date.

### Step 3 — Output
A short pass/fail table, one row per check, with the two amounts and the difference. Any difference is a finding for a person to investigate, with the accounts most likely involved.

---

## Workflow 4: Cutoff and accrual review

**When to use:** checking that the period contains the costs and revenue it should, and nothing that belongs elsewhere.

**Operations:** `search_bills`, `search_purchases`, `search_invoices` for a window around period end; `get_vendor_expenses` for the last three to six months; `get_balance_sheet` for prepaid and accrued balances.

### Step 1 — Pull the data
> Pull bills, purchases and invoices dated from [period end − 10 days] to [period end + 15 days], and vendor expenses by month for the last [3–6] months.

### Step 2 — Find gaps and cutoff issues
Apply `calculations.md` → *Recurring cost expectations*: vendors billed every month in the lookback but missing in the period are candidate accruals. Transactions dated after period end whose description or service period points to the reviewed period are candidate cutoff items. Large prepaid or accrued balances that didn't move are candidate stale balances.

### Step 3 — Output
A list of candidate accruals and cutoff items, each with vendor, typical amount, evidence and a proposed review-only entry (accounts and amount) for a person to decide. Never present a proposed entry as booked.

---

## Workflow 5: Unusual expense review

**When to use:** "anything unusual in spending?", pre-review sweep, or an owner asking about a specific jump.

**Operations:** `get_vendor_expenses` (current and comparison), `search_purchases`, `search_bills`, `get_general_ledger` for drill-down.

### Step 1 — Pull the data
> Pull expenses by vendor for [period] and [comparison period], and the purchase and bill transactions for [period].

### Step 2 — Flag
Apply `calculations.md` → *Unusual expense tests*: new vendor above materiality, vendor spend up sharply, single transaction far above the vendor's typical amount, round-number amounts, weekend or holiday dates, same amount to the same vendor twice in a short window, expenses coded to an account the vendor doesn't normally use.

### Step 3 — Output
Build from `assets/unusual_expense_log.md`. Group as **observed fact**, **possible explanation**, **missing evidence**, **follow-up owner**. Do not label anything fraudulent, erroneous or misclassified without evidence.

---

## Workflow 6: Gross margin by customer and item

**When to use:** "which customers or products are profitable?", pricing reviews, job costing questions.

**Operations:** `get_customer_sales`, `get_profit_and_loss` (by customer or item where supported), `search_invoices`, `search_items`.

### Step 1 — Pull the data
> Pull sales by customer (and by item if available) and the cost of goods sold detail for [period].

If cost isn't tracked by customer or item in the books, say so plainly and offer revenue concentration instead. Don't allocate cost with an invented driver unless the user supplies one, and then label it an assumption.

### Step 2 — Compute
`calculations.md` → *Margin*. Rank by gross margin amount and by margin %; show the blended margin.

### Step 3 — Visualize
Sorted bar of margin % by customer with blended margin as a reference line; or a scatter of revenue (x) vs margin % (y).

### Step 4 — Output
Top and bottom customers or items with the evidence, and the pricing or scope questions a person should ask.

---

## Workflow 7: Budget vs actual variance

**When to use:** comparing actuals to the budget saved in the books.

**Operations:** `search_budgets` to list budgets, then `get_budget_details` with `budget_id` or `budget_name`; `get_profit_and_loss` for actuals on the same dates and basis.

### Step 1 — Pull the data
> List budgets for [company], then pull the chosen budget and the P&L for [period].

If more than one budget exists for the year, ask which one. If none exists, say so and offer prior-year actuals as the comparison instead.

### Step 2 — Compute
`calculations.md` → *Variance and materiality*, with favourable/unfavourable set by account type (revenue over budget is favourable; expense over budget is unfavourable).

### Step 3 — Visualize
Grouped bars (budget vs actual) for the top lines, or a variance table coloured by favourability. For year to date, a line of cumulative actual vs cumulative budget.

### Step 4 — Output
Build from `assets/variance_commentary.md`: the headline, material variances with causes, and the forecast impact if the user asks for one (labelled as a scenario).
