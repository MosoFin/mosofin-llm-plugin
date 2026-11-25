# Accounting firm and CAS workflows

Seven workflow patterns for work that spans a book of clients. Each ends with a specific deliverable. Operation names below are the QuickBooks catalog names; confirm each one is listed by `get_datasource_tools` for the chosen company before invoking, and pass that client's `workspace_id` (and `data_source_id` when several companies are live) on every call.

**Client scope, every time:** `list_workspaces` → the user picks clients by name → `list_workspaces(workspace_ids=[…], mode="multi")` (or `mode="single"` for one) → `get_agent_datasources` per workspace → company by `display_name`. Never auto-pick.

## Workflow 1: Portfolio health sweep

**When to use:** "how are all my clients doing?", Monday triage, a partner asking which clients need attention.

**Operations (per client):** `get_profit_and_loss` (last complete month and the month before), `get_balance_sheet` (last month end), `get_aged_receivables`, `get_aged_payables`; optionally `get_trial_balance`.

### Step 1 — Confirm scope
Confirm the client workspaces in `mode="multi"` and the company per workspace. Agree the period (default: last complete month vs the month before) and say it.

### Step 2 — Delegate the sweep
For more than a handful of clients, delegate to the `client-portfolio-analyst` agent. Pass, per client: workspace name, workspace handle, company `display_name` and `data_source_id`, the period dates, and any materiality from the review profile. Run the sweep in chat yourself only for two or three clients.

If the agent returns `selection_required`, `entity_required` or `sign_in_required`, resolve it with the user and re-delegate only the affected clients.

### Step 3 — Visualize
A sorted table, flagged clients first, flags in amber. If the user wants a picture, a sorted bar of the one metric behind most flags (e.g. A/R over 90 days as % of A/R) with the book median as a reference line.

### Step 4 — Output
Build from `assets/client_portfolio_report.md`: the one-line count, the table, one line per top flagged client (at most 10) with the likely cause and next step, and **Not read**.

### Common variations
- **Only my clients / one manager's clients** — the user names the subset; sweep only those.
- **Weekly instead of monthly** — use last 7 days vs the 7 before for cash and A/R; skip P&L flags (a week is too thin).

---

## Workflow 2: Multi-client month-end status

**When to use:** running the same month-end review across several clients without mixing them.

**Operations (per client):** `get_trial_balance`, `get_profit_and_loss`, `get_balance_sheet`, `get_aged_receivables`, `get_aged_payables`, `search_accounts` for suspense / uncategorized balances.

### Step 1 — Agree the review structure
Use the same steps for every client: confirm scope, validate P&L and balance sheet dates and basis, identify material changes, review cash and receivables exceptions, record missing support, produce a follow-up list with owner and due date. Read them back once.

### Step 2 — One client at a time
Select **one** client. Run the steps for that client only, using `calculations.md` → *Month-end step tests*. Show the result for that client.

### Step 3 — Stop between clients
Ask the user to confirm before moving on. Never carry a fact, threshold override or explanation from one client into the next.

### Step 4 — Status grid
After the last client, build a client × step status grid (Done / Open / Blocked / Not read), coloured by state, from the per-client results only.

### Step 5 — Output
Per client: the follow-up list (item, amount, owner, due). Book-level: the status grid. Offer `xlsx` for the tracker. Point to the public [multi-client month-end review checklist](https://www.mosofin.com/multi-client-quickbooks-month-end-review-checklist) for the full 17-step method and reviewer approval.

---

## Workflow 3: Client advisory meeting prep

**When to use:** "prep for my meeting with [client] on Thursday."

**Operations:** `get_profit_and_loss` (last 3 months, YTD, prior YTD), `get_balance_sheet` (last month end and prior year end), `get_aged_receivables`, `get_cash_flow`; `get_customer_sales` and `get_vendor_expenses` for concentration.

### Step 1 — Pull the data
> For [client], pull the P&L for the last three months and YTD vs prior YTD, the balance sheet as of [month end] and [prior year end], A/R aging, and the cash flow for YTD.

### Step 2 — Find the three things worth discussing
Apply `calculations.md` → *Client KPIs*. Pick at most three topics: one about results (revenue, margin), one about cash (runway, A/R, profit vs cash), one about risk or opportunity (customer concentration, a cost trend, a pending year-end item). Each topic gets a number and a question for the client.

### Step 3 — Visualize
One trend chart for the results topic (monthly revenue and gross margin, prior year as reference). Keep everything else as numbers in the brief.

### Step 4 — Output
Build from `assets/advisory_meeting_brief.md`. Default is inline markdown; offer `docx` or `pptx`. The brief is for the advisor, not the client, unless the user says otherwise.

---

## Workflow 4: Cleanup scoping

**When to use:** a new client, a client who's fallen behind, or a quote for catch-up work.

**Operations:** `get_company_info`, `get_trial_balance`, `get_balance_sheet` (now and at each recent year end), `search_accounts`, `get_general_ledger` for suspense / uncategorized / undeposited funds, `search_purchases`, `search_deposits`, `search_journal_entries` by month.

### Step 1 — Pull the data
> For [client], pull the balance sheet now and at [last two year ends], the trial balance, and activity in uncategorized, suspense and undeposited funds accounts for the last [12–24] months.

### Step 2 — Size the job
Apply `calculations.md` → *Cleanup sizing*: last month with normal activity, months with uncategorized activity, count and amount of uncategorized transactions by month, undeposited funds age, opening balance equity, negative balances, A/R and A/P items older than 365 days, TB checks. MosoFin can't see bank statements: say the reconciliation status is unknown, not done.

### Step 3 — Visualize
Bar of uncategorized transaction count (and amount) by month, so the client sees where the backlog is.

### Step 4 — Output
Build from `assets/cleanup_scope_estimate.md`: findings, effort drivers and what the firm still needs from the client. Leave fees to the user; give volume, not price.

---

## Workflow 5: Selected-company comparison

**When to use:** an owner or firm comparing several companies the user picks — entities of one owner, or a few clients on one metric.

**Operations (per company):** whichever report the metric needs, same dates and basis for every company.

### Step 1 — Confirm the selection
Ask the user to confirm exactly which workspaces and companies are included. Confirm with `mode="multi"`.

### Step 2 — Pull per company
> For each selected company, pull [metric or report] for [period] on [basis].

Use identical dates and basis. Where a company differs (fiscal year, currency, basis), say so beside its figures.

### Step 3 — Show each company separately first
One row or one mini-chart per company with company, period, basis, currency and source.

### Step 4 — Combined total (only if asked)
If the user wants a total, label it **"illustrative combined analysis — not a consolidation"**. Do not imply eliminations, intercompany matching, ownership rules, currency translation, close controls or formal reconciliation were performed. Don't sum different currencies.

### Step 5 — Output
The per-company table or small multiples, then the labelled combined figure if requested.

---

## Workflow 6: Client KPI benchmarking across the book

**When to use:** "how does this client compare to my other clients?", building a firm benchmark for advisory work.

**Operations (per client):** `get_profit_and_loss` (trailing 12 months), `get_balance_sheet` (period end), `get_aged_receivables`, `get_aged_payables`.

### Step 1 — Confirm the peer set
The user picks the clients, and ideally an industry or size grouping. Comparing a law firm's margin with a contractor's is not useful; say so if the set is mixed.

### Step 2 — Compute each client separately
`calculations.md` → *Client KPIs* for each client, from that client's pulls only. Then compute the book median and quartiles per KPI with the client count.

### Step 3 — Visualize
Sorted bar per KPI with the focus client highlighted and the others as anonymized bars; or a scatter of revenue growth vs net margin.

### Step 4 — Output
Internal view: client names allowed. Anything that may reach a client: apply `calculations.md` → *Anonymization*. State the sample size on every median.

---

## Workflow 7: Year-end and tax-prep readiness

**When to use:** before handing a client's books to the tax preparer, or a fourth-quarter readiness sweep across clients.

**Operations:** `get_trial_balance`, `get_balance_sheet` (year end and prior year end), `get_profit_and_loss` (fiscal year), `search_vendors`, `get_vendor_expenses` (fiscal year), `search_accounts`, `get_general_ledger` for owner draws, fixed assets and loans, `get_aged_receivables`, `get_aged_payables`.

### Step 1 — Pull the data
> For [client], pull the trial balance, balance sheet and P&L for fiscal year [year], vendor expenses for the year, and the vendor list.

### Step 2 — Run the checks
`calculations.md` → *Year-end checks*: TB ties; suspense and uncategorized cleared; opening balance equity zero; retained earnings rolled from prior year; fixed asset and loan activity present where expected; owner draws/contributions classified; old A/R and A/P reviewed; vendors paid over the reporting threshold missing tax ID or 1099 flag (as far as the vendor fields show).

### Step 3 — Output
Build from `assets/year_end_readiness_checklist.md`: Ready / Ready with items / Not ready, the item list with owner and due date, and what the tax preparer will still need from the client. No tax advice; tax positions are for the preparer.

### Common variations
- **Across all clients** — run the checks per client (or via the `client-portfolio-analyst` agent for the data pulls), then a status grid.
