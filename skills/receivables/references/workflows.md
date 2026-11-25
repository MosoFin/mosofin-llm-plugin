# Receivables and collections workflows

Seven workflow patterns for understanding and collecting what customers owe. Each ends with a specific deliverable. Operation names below are the QuickBooks catalog names; confirm each one is listed by `get_datasource_tools` for the chosen company before invoking, and pass `workspace_id` (and `data_source_id` when several companies are live) on every call.

## Workflow 1: A/R aging review and control-account tie

**When to use:** "who owes us?", month-end A/R review, or before any collections work. Run it first; the other workflows assume the aging is trustworthy.

**Operations:** `get_aged_receivables`, `get_balance_sheet` (same as-of date), `get_customer_balance`; drill-down with `search_invoices`, `search_payments`, `search_credit_memos`.

### Step 1 — Pull the data
> Pull the A/R aging and the balance sheet as of [date] for [company], plus customer balances as of the same date.

Use the same `report_date` for every as-of report. Default to today only if the user didn't name a date, and say so.

### Step 2 — Tie the aging
Apply `calculations.md` → *Control-account tie*: aging total vs balance sheet A/R. Any difference is a finding. Look for journal entries posted to A/R with `search_journal_entries` for the period, which often explain it.

### Step 3 — Group and net
Group balances by bucket (`calculations.md` → *Aging buckets*). For each customer, compute the **net supported balance** (`calculations.md` → *Net supported balance*): open invoices less unapplied payments and open credit memos.

### Step 4 — Visualize the headline
The chart answers: **how old is our A/R and where is it concentrated?** A stacked bar of buckets, plus a sorted bar of the top past-due customers.

### Step 5 — Output
Build from `assets/ar_aging_review.md`: totals by bucket, the tie result, exceptions (credit balances, unapplied items, disputes from known context), and the customers that need a person's review before contact.

### Common variations
- **Compare to last month** — pull the prior month-end aging and show bucket movement.
- **Many companies** — run per company separately; never mix customers across clients.

---

## Workflow 2: Collections priority list

**When to use:** "who should I chase this week?"

**Operations:** `get_aged_receivables`, `search_invoices` (open, past due), `search_payments` (last 90 days), `search_credit_memos`, `get_customer` for contact fields.

### Step 1 — Pull the data
> Pull the A/R aging as of [date], open invoices, and payments and credit memos from the last 90 days.

### Step 2 — Net and filter
Net each customer (`calculations.md` → *Net supported balance*). Drop customers with net supported balance ≤ 0 or below the minimum. Move customers marked disputed, on a payment plan or on hold in the review profile to a separate "Handle separately" list.

### Step 3 — Score
Apply `calculations.md` → *Priority score*. Rank by score.

### Step 4 — Output
Build from `assets/collections_priority_list.md`: rank, customer, net past due, oldest invoice, last payment date, reason, suggested next step (reminder, call, escalate to owner). Missing contact details are a finding for the bookkeeper, not something to guess.

### Common variations
- **Top 10 only** — cap the list and say how many more are past due.
- **By owner or rep** — if the books carry a sales rep or class, group by it.

---

## Workflow 3: DSO trend

**When to use:** "are we collecting slower?", monthly owner reporting.

**Operations:** `get_aged_receivables` or `get_balance_sheet` at each month end, `get_profit_and_loss` or `get_customer_sales` for revenue per month.

### Step 1 — Pull the data
> Pull A/R at each month end and revenue for each month for the last [6–12] months.

Use accrual basis for revenue. If only cash basis is available, say DSO isn't meaningful and offer days-to-pay (Workflow 6) instead.

### Step 2 — Compute
`calculations.md` → *DSO*. Show monthly DSO and the trailing 3-month DSO.

### Step 3 — Visualize
Line chart of DSO by month with the standard terms days (from the review profile) as a reference line.

### Step 4 — Output
Build from `assets/dso_summary.md`: the trend, the customers driving any change, and whether the change is collection speed or a revenue swing (DSO moves when revenue drops even if collections don't).

---

## Workflow 4: Customer concentration and credit risk

**When to use:** "how dependent are we on a few customers?", "which customers worry you?", lender or investor questions.

**Operations:** `get_customer_sales` (last 12 months and the prior 12), `get_aged_receivables`, `search_payments`, `get_customer_balance`.

### Step 1 — Pull the data
> Pull sales by customer for the last 12 months and the prior 12, the A/R aging as of [date], and payments for the last 6 months.

### Step 2 — Compute
`calculations.md` → *Concentration* (top-1, top-5 share, HHI) and *Early-warning signals* per customer.

### Step 3 — Visualize
Pareto chart: sorted bar of revenue share by customer with a cumulative line.

### Step 4 — Output
Build from `assets/credit_risk_brief.md`. Signals are labelled **observed** (in the records) with no claim about the customer's solvency. Public news from web search is context, cited, and separate from the ledger facts.

---

## Workflow 5: Unapplied payments and open credit memos

**When to use:** the aging looks wrong, a customer says they paid, or before sending any reminders.

**Operations:** `search_payments`, `search_credit_memos`, `search_deposits`, `search_invoices`, `get_aged_receivables`.

### Step 1 — Pull the data
> Pull payments, credit memos and deposits for the last [6] months and open invoices as of [date].

### Step 2 — Match
`calculations.md` → *Unapplied matching*: unapplied payments and open credits, each with candidate invoices for the same customer (exact amount first, then sum of invoices). Also flag deposits recorded straight to income for customers who have open invoices of the same amount — a common cause of overstated A/R.

### Step 3 — Output
A table: customer, item (payment / credit / deposit), date, amount, candidate invoice(s), match confidence (exact / partial / none), suggested action for a person (apply, refund, investigate). Never present a match as applied.

---

## Workflow 6: Invoice-to-cash cycle

**When to use:** "how long do customers take to pay?", setting terms, cash forecasting inputs.

**Operations:** `search_invoices` (paid in the period), `search_payments`, `read_invoice` for terms where needed.

### Step 1 — Pull the data
> Pull invoices paid in the last [6–12] months with their invoice dates, due dates and payment dates.

### Step 2 — Compute
`calculations.md` → *Days to pay*. Per customer: invoices paid, average and median days to pay, average days past due, trend vs the prior period.

### Step 3 — Visualize
Sorted bar of median days to pay by customer with terms days as a reference line; customers above the line are paying late.

### Step 4 — Output
A table and two or three findings ("Customer X moved from 32 to 58 days in the last quarter"). Feeds the `fractional-cfo` 13-week cash scenario as a user-confirmed assumption.

---

## Workflow 7: Reminder email drafts

**When to use:** "draft reminders for overdue customers". Always after Workflow 2 (or a fresh net check) in the same conversation.

**Operations:** reuse the netted list from Workflow 2; `get_customer` for the contact name; `read_invoice` for invoice numbers and amounts.

### Step 1 — Confirm the list
Show the customers that will get a draft, with net past-due amounts. Remove any customer with net supported balance ≤ 0, an open dispute, or a payment plan in known context. Ask the user to confirm the list.

### Step 2 — Draft
Build from `assets/customer_reminder_drafts.md`, choosing the stage by the oldest past-due invoice. Quote only invoice numbers, dates and amounts from the retrieved records.

### Step 3 — Output
Drafts inline as markdown, each labelled **Draft — not sent**. If an email connector is available, offer to save them as drafts; never send. Remind the user to check each balance with the customer's latest remittance before sending.
