# Fractional CFO and owner workflows

Seven workflow patterns for the decision-level view of a business. Each ends with a specific deliverable. Operation names below are the QuickBooks catalog names; confirm each one is listed by `get_datasource_tools` for the chosen company before invoking, and pass `workspace_id` (and `data_source_id` when several companies are live) on every call.

## Workflow 1: Monthly owner pack

**When to use:** after month-end, "how did we do last month?", the recurring owner meeting.

**Operations:** `get_profit_and_loss` (month, prior month, same month last year, YTD), `get_balance_sheet` (month end and prior month end), `get_cash_flow` (month), `get_aged_receivables`, `get_aged_payables`.

### Step 1 — Pull the data
> Pull the P&L for [month], [prior month], [same month last year] and YTD on [basis]; the balance sheet at [month end] and [prior month end]; cash flow for [month]; and A/R and A/P aging at [month end].

These reads are independent and may run in parallel.

### Step 2 — Visualize the headline
The first thing the owner sees answers: **how did the month go?** Headline cards: revenue, gross margin %, net income, ending cash, each with change vs prior month and vs target from the review profile.

### Step 3 — Supporting schedules
Short P&L summary (revenue, cost of sales, gross margin, opex groups, net income), cash bridge (Workflow 3, abbreviated), A/R and A/P headline (total and 60+ days), KPI scorecard rows that missed target.

### Step 4 — Output
Build from `assets/owner_monthly_pack.md`. Default delivery is headline cards plus the pack inline. Offer `docx` for a file, and `assets/owner_email.md` for the covering email.

### Common variations
- **One-page only** — headline cards, three bullets, one ask.
- **Quarter** — run Workflow 7 instead.

---

## Workflow 2: Cash runway and burn

**When to use:** "how long does our cash last?", fundraising or loan conversations, cost-cutting decisions.

**Operations:** `get_balance_sheet` at each of the last 6–12 month ends, `get_cash_flow` per month, `get_profit_and_loss` per month.

### Step 1 — Pull the data
> Pull month-end cash balances and monthly cash flow for the last [6–12] months.

### Step 2 — Compute
`calculations.md` → *Burn and runway*. Separate operating burn from financing (loan draws, repayments, equity, owner draws). Use the trailing 3-month average burn; show the 6-month average next to it.

### Step 3 — Visualize
Line of month-end cash with the minimum cash balance from the profile as a reference line. If the user asks how it might continue, extend with a **dashed** scenario line at the current burn, labelled "Scenario — assumes burn stays at [$]/month".

### Step 4 — Output
Build from `assets/cash_runway_brief.md`: runway in months at current burn, the drivers of burn, financing items, and the decisions that change runway (with their size from the books, not invented).

### Common variations
- **Cash-generative business** — say runway isn't the right frame; show months of operating costs covered by cash instead.
- **Several bank accounts** — sum only cash accounts; exclude restricted or clearing accounts and say which.

---

## Workflow 3: Profit vs cash bridge

**When to use:** "we're profitable, so where's the cash?" (prompt library §2).

**Operations:** `get_profit_and_loss`, `get_cash_flow`, `get_balance_sheet` (start and end of period), `get_aged_receivables`, `get_aged_payables`.

### Step 1 — Pull the data
> Pull the P&L and cash flow for [period], the balance sheet at [period start − 1 day] and [period end], and A/R and A/P aging at [period end].

### Step 2 — Bridge
`calculations.md` → *Cash bridge*: net income → non-cash items → working capital (A/R, inventory, prepaid, A/P, accrued) → capital purchases → financing (loans, owner draws, equity) → change in cash. Reconcile the bridge to the actual change in cash; any residual is a finding.

### Step 3 — Visualize
Waterfall from net income to change in cash, one bar per bridge line.

### Step 4 — Output
Explanation in plain language with amounts. Separate **recorded movements** (facts from the reports) from **timing hypotheses** ("customers paying slower" is observed only if the aging or days-to-pay show it). List the source checks a person should do before a liquidity decision. Do not produce a forecast unless the user asks and supplies assumptions (then use Workflow 6).

---

## Workflow 4: KPI scorecard vs targets

**When to use:** "are we hitting our targets?", monthly or quarterly KPI review.

**Operations:** `get_profit_and_loss`, `get_balance_sheet`, `get_aged_receivables`, `get_aged_payables`, `get_customer_sales` as needed for the chosen KPIs.

### Step 1 — Confirm the KPIs
Load targets from the review profile. If none are saved, propose a default set (gross margin %, operating expense ratio, net margin %, DSO, DPO, current ratio, cash runway) and ask which to keep and the target for each. Offer to save them via `review-profile`.

### Step 2 — Pull and compute
Pull the reports for the period and the comparison. Compute each KPI with `calculations.md` → *KPI definitions*, same basis throughout.

### Step 3 — Visualize
Scorecard table: KPI, actual, target, prior, status (on target / within 10% / off target) coloured by business meaning. Trend lines for the KPIs off target.

### Step 4 — Output
Build from `assets/kpi_scorecard.md`. For each KPI off target, the driver (observed / possible / unknown) and the next action with an owner.

---

## Workflow 5: Revenue and customer concentration

**When to use:** "how dependent are we on a few customers?", lender or buyer diligence, pricing decisions.

**Operations:** `get_customer_sales` (last 12 months and prior 12), `get_profit_and_loss` by item or class where supported, `get_aged_receivables`.

### Step 1 — Pull the data
> Pull sales by customer for the last 12 months and the prior 12, and revenue by product or class if available.

### Step 2 — Compute
`calculations.md` → *Concentration*: top-1, top-5, HHI; growth by customer; share of growth from the top customers.

### Step 3 — Visualize
Pareto chart of revenue share by customer; a slopegraph of the top customers' revenue this year vs last.

### Step 4 — Output
Headline, concentration measures, the customers that drove growth or decline, and the exposure if a top customer left (shown as the share of revenue and gross margin — not a scenario of what will happen). For payment behaviour, hand off to the `receivables` skill's credit risk workflow.

---

## Workflow 6: 13-week cash view (scenario)

**When to use:** "can we make payroll in May?", "13-week cash", lender requests. Only as a **labelled scenario**.

**Operations:** `get_balance_sheet` (opening cash), `get_aged_receivables`, `get_aged_payables`, `search_invoices` (open), `search_bills` (open), `get_vendor_expenses` (recurring costs, last 3–6 months).

### Step 1 — Pull the starting facts
> Pull cash at [start date], open invoices and open bills with due dates, and expenses by vendor for the last [3–6] months.

### Step 2 — Collect assumptions
Ask the user to supply or confirm each assumption before building: collection timing (e.g. customers pay at their historical median days to pay from the `receivables` invoice-to-cash workflow), new sales per week, payroll amounts and dates, recurring costs, debt service, tax payments, owner draws, one-offs. Record each assumption with its source (**user** / **historical, confirmed by user**). Don't fill gaps silently; mark any unconfirmed line "not included".

### Step 3 — Build
`calculations.md` → *13-week scenario*. Weekly: opening cash, receipts, disbursements, net, closing cash; flag weeks below the minimum cash balance.

### Step 4 — Visualize
Line of weekly closing cash (dashed, labelled Scenario) with the minimum cash balance line. If the user gives alternatives, one line per scenario.

### Step 5 — Output
Build from `assets/scenario_memo.md`. Every page says **Scenario — not a forecast** and lists the assumptions. Offer `xlsx` so the user can change assumptions themselves.

---

## Workflow 7: Quarterly board / investor summary

**When to use:** quarter end, board meetings, investor updates.

**Operations:** `get_profit_and_loss` (quarter, prior quarter, same quarter last year, by month), `get_balance_sheet` (quarter ends), `get_cash_flow` (quarter), plus KPI reads from Workflow 4.

### Step 1 — Pull the data
> Pull the P&L by month for [quarter] and the comparison quarters, balance sheets at quarter ends, and cash flow for the quarter.

### Step 2 — Headline and supporting analysis
Headline cards for the quarter; trend of revenue and gross margin by month; KPI scorecard; cash and runway (Workflow 2); concentration if material.

### Step 3 — What we tried
Include what was tested or changed and what the numbers show about it, when the user provides the initiatives. Boards trust a review that names what didn't work.

### Step 4 — Build the deck
Build from `assets/board_update_outline.md`. Use the `pptx` skill, if available, after reading its SKILL.md. Charts on slides, bullets only, one message per slide. Forward-looking slides are labelled scenarios.

### Step 5 — Companion deliverables (only if asked)
A covering email from `assets/owner_email.md`, a `docx` handout, or an `xlsx` with the supporting schedules.
