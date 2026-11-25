# Payables and spend workflows

Seven workflow patterns for understanding what the business owes and what it spends. Each ends with a specific deliverable. Operation names below are the QuickBooks catalog names; confirm each one is listed by `get_datasource_tools` for the chosen company before invoking, and pass `workspace_id` (and `data_source_id` when several companies are live) on every call. Follow pagination on every search operation.

## Workflow 1: A/P aging and payment run proposal

**When to use:** the user wants to see what's owed and decide what to pay this week or this cycle. Also the backbone of `/mosofin:ap-aging`.

**Operations:** `get_aged_payables` (as of today or the payment date), `get_balance_sheet` (A/P control account and cash), `search_bills` for open bills with due dates, `get_vendor_balance`, `search_vendors` for terms.

### Step 1 — Pull the data
> Pull A/P aging as of [date], the balance sheet as of [date], and open bills with due dates for [company].

### Step 2 — Check the ledger
Confirm the aging total equals the A/P control account (`calculations.md` → *Aging and DPO*). Net open vendor credits against each vendor. Note bills without terms or due dates.

### Step 3 — Prioritize
Ask how much cash is available for this run if the user hasn't said, or use the profile's minimum cash buffer against the balance sheet cash (label it as the books' cash, not the bank balance). Apply `calculations.md` → *Payment prioritization* to rank bills until the limit is reached.

### Step 4 — Visualize
Stacked bar of A/P by aging bucket, with the proposed run highlighted.

### Step 5 — Output
Build from `assets/payment_run_proposal.md`: proposed bills with vendor, bill number, due date, amount and reason; bills deliberately held and why; total vs the limit. It's a proposal for approval; MosoFin can't pay.

### Common variations
- **Aging only** — skip prioritization; show buckets, top overdue vendors and DPO.
- **Critical vendors first** — use the profile's critical vendor list as priority 1.
- **Several entities** — run per company; never pool cash across companies.

---

## Workflow 2: Vendor spend analysis

**When to use:** "who do we spend the most with?", budget conversations, vendor negotiations.

**Operations:** `get_vendor_expenses` for the period and a comparison period; `search_purchases` and `search_bills` for drill-down; `get_vendor` for details.

### Step 1 — Pull the data
> Pull expenses by vendor for [period] and [comparison period] on [basis].

### Step 2 — Compute
`calculations.md` → *Spend change*. Rank vendors by spend; for each, change vs comparison and share of total. Flag new vendors above materiality and vendors with material increases.

### Step 3 — Visualize
Sorted horizontal bar of top 10 vendors, coloured by direction of change.

### Step 4 — Output
Build from `assets/vendor_spend_report.md`. For material increases, drill into transactions and label the driver **observed** (e.g. a new recurring charge), **possible** or **unknown**.

---

## Workflow 3: Duplicate bill detection

**When to use:** before a payment run, after an import, or "did we pay this twice?".

**Operations:** `search_bills` and `search_payments` for the period plus a 60-day lookback; `get-bill` / `read_invoice` style get-by-id operations for detail where listed.

### Step 1 — Pull the data
> Pull bills and bill payments dated from [start − 60 days] to [end] for [company].

### Step 2 — Match
`calculations.md` → *Duplicate matching*. Rank pairs as **strong** or **weak**. Separately check payments: two payments to the same vendor for the same bill, or total payments exceeding the bill amount.

### Step 3 — Output
Build from `assets/duplicate_bill_findings.md`, both records side by side, with paid/open status. Unpaid candidates should be held out of the payment run until a person confirms. Paid duplicates are questions for the vendor (a credit or refund request, drafted and unsent).

---

## Workflow 4: Spend trend by category

**When to use:** "where is the money going?", cost reviews, monthly owner reporting.

**Operations:** `get_profit_and_loss` by month for the last 6–12 months; `get_general_ledger` for drill-down on categories that moved.

### Step 1 — Pull the data
> Pull the P&L by month for [last 6–12 months] on [basis].

### Step 2 — Compute
Group expense accounts into the categories the profile uses (or the P&L's own sections). `calculations.md` → *Spend change*: latest month vs trailing 3-month average, and year-to-date vs prior YTD.

### Step 3 — Visualize
Line chart per top category, or a stacked bar per month; highlight the category with the largest material change.

### Step 4 — Output
Build from `assets/spend_review_memo.md`: the headline, categories that moved with drivers, and questions for the owner. For formal variance commentary hand off to the `controller` skill.

---

## Workflow 5: Vendor concentration

**When to use:** supplier risk reviews, lender or investor questions, "how dependent are we on one vendor?".

**Operations:** `get_vendor_expenses` for the last 12 months; `get_aged_payables` for current exposure.

### Step 1 — Pull the data
> Pull expenses by vendor for the last 12 months and A/P aging as of [date].

### Step 2 — Compute
`calculations.md` → *Concentration*: top-1, top-5 and top-10 share, HHI, and the vendors above the single-vendor threshold. Separate cost-of-sales vendors from overhead where the accounts allow; concentration in suppliers matters more than in rent.

### Step 3 — Visualize
Pareto: sorted bar of vendor spend with a cumulative share line.

### Step 4 — Output
Concentration figures, the vendors that drive them, current A/P owed to each, and the questions a person should ask (alternatives, contract terms). Don't infer contract terms the records don't show.

---

## Workflow 6: Recurring and subscription spend inventory

**When to use:** "what subscriptions do we have?", cost-cutting, renewal planning.

**Operations:** `search_purchases` and `search_bills` for the last 12 months; `get_vendor_expenses` for totals.

### Step 1 — Pull the data
> Pull purchases and bills for the last 12 months for [company].

### Step 2 — Detect
`calculations.md` → *Recurrence detection*: vendors with a regular cadence (monthly, quarterly, annual) and stable amounts. Annualize each. Flag price changes, vendors that appear duplicated (two tools for one job — *possible* only), and charges that stopped.

### Step 3 — Visualize
Table sorted by annualized amount; optionally a bar of annualized cost by category.

### Step 4 — Output
Build from `assets/recurring_spend_inventory.md`. Cancellation candidates are questions for the owner, not recommendations to cancel; MosoFin can't see usage.

---

## Workflow 7: Upcoming cash commitments

**When to use:** "what's due in the next two weeks?", weekly cash planning.

**Operations:** `search_bills` (open, due in the window), `get_aged_payables`, `get_balance_sheet` (cash, credit cards, loans); recurring vendors from Workflow 6 when the user wants expected-but-not-yet-entered bills.

### Step 1 — Pull the data
> Pull open bills due between [today] and [today + 28 days], A/P aging as of today, and the balance sheet as of today.

### Step 2 — Compute
`calculations.md` → *Due-date windows*: overdue now, due in 0–14 days, due in 15–28 days. Optionally add expected recurring bills not yet entered, labelled **expected — not in the books**. Compare to cash per the books if the user wants a coverage view.

### Step 3 — Visualize
Bar per week of bills due, with book cash (or the user's stated cash) as a reference line.

### Step 4 — Output
A short list by window with totals, the largest commitments, and the overdue amount. Say plainly that customer receipts aren't included unless the user asks; for a full cash view hand off to the `fractional-cfo` skill and for expected collections to `receivables`.
