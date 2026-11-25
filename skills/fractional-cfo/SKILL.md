---
name: fractional-cfo
description: Battle-tested finance leadership workflows for fractional CFOs, finance leads and business owners, using the MosoFin connector. Use whenever the user wants the decision-level view of the business rather than a bookkeeping check. Trigger on phrases like "owner pack," "monthly financial package," "how much runway do we have," "burn rate," "why are we profitable but out of cash," "cash flow," "KPI scorecard," "are we hitting our targets," "gross margin trend," "customer concentration," "13-week cash," "cash forecast," "board update," "investor update," "quarterly summary." Also trigger when the user wants a deck, memo or email for owners, a board or investors built from QuickBooks or other connected books, even if they don't say "MosoFin".
---

# MosoFin workflows for fractional CFOs and owners

This skill helps fractional CFOs, finance leads and owners turn the books into decisions: how the month went, how long the cash lasts, why profit and cash differ, whether the KPIs are on target, and what to tell a board. Output from these workflows lands in front of owners, lenders and investors, so it is tuned for decision-ready conclusions that trace back to the source reports.

## Required: the MosoFin connector

These workflows rely on the **MosoFin connector**, which reads live data from the connected accounting platform. Before running anything, check that MosoFin tools are in this session (`list_workspaces`, `invoke_datasource_api_tool`; your host prefixes them, e.g. `mcp__plugin_mosofin_mosofin__list_workspaces`).

If they aren't present, follow the `connect` skill: keep the user's request, give the one sign-in step for this host, and continue when they say *done*. Never answer from memory.

Don't fabricate placeholder figures. Owners and boards decide on real numbers.

## Before you analyze

- **Confirm scope.** Workspace by name (see `workspaces`), company by `display_name` (see `connections`), period as concrete `YYYY-MM-DD` dates, and accounting basis. Read the scope back before the first data call.
- **Load the review profile.** Use the `review-profile` skill for KPI targets, fiscal year, minimum cash balance, materiality and known context ("loan drawn in March", "annual insurance paid in January"). Judge against those, not generic benchmarks. If nothing is saved and the analysis depends on a target, ask **one** question and offer to save the answer.
- **Check books health first.** If the period isn't reviewed, the trial balance doesn't tie, or suspense balances are material, say so before drawing conclusions. See `/mosofin:books-health` and the `controller` skill.
- **Facts vs assumptions.** Historical figures come from the books. Anything forward-looking (runway beyond the current burn, a 13-week view, a hiring plan) is a **scenario** built only on assumptions the user provides or confirms, labelled as such on every page. Never present a scenario as a forecast of what will happen.
- **Evidence discipline:**
  - Treat moves below materiality (from the profile; default 10% and 1% of revenue) as noise.
  - Label causes *observed*, *possible* or *unknown*.
  - Normalize one-offs the profile lists as known context before trending, and say so.
  - If the user disputes a number, re-pull the report before conceding.
- **Answer shape:** conclusion first, then business impact, then next actions with an owner. Close with a **Data sources** line (datasource + tool + `fetched_at` from `provenance`) and say whether figures are live or fixture (`mock`).

## Core principles

1. **Two-step pattern: pull, then analyze.** Pull the reports, confirm what you got in one line ("P&L Jan–Jul 2026 accrual by month; balance sheet at each month end; cash flow YTD"), then analyze.
2. **Show, don't tell.** Owners and boards scan. Every number worth comparing gets a visual or a tight table.
3. **Batch small reads, sequence heavy ones.** Monthly P&L, balance sheet and aging reads may run in parallel. Ledger and transaction searches over long ranges are heavy; run them one at a time and follow pagination.
4. **Read-only, always.** MosoFin cannot move money, pay bills or change records. Recommendations are for a person to decide and execute.

## Choosing the right visualization

Decide by the question, not by the workflow.

### Step 1 — What is the owner trying to see?

| Question type | Visualization family |
|---|---|
| How the month or quarter went | Headline metric cards (revenue, gross margin %, net income, cash) with change vs prior and target |
| Cash over time and runway | Line of month-end cash with the minimum cash balance as a reference line; scenario months dashed |
| Why profit and cash differ | Waterfall from net income to change in cash |
| KPIs vs target | Scorecard table with status colour, or bullet charts |
| Trend of a ratio | Line chart, target as a reference line |
| Revenue concentration | Pareto: sorted bar of customer share with cumulative line |
| Scenario comparison | Lines per scenario, actuals solid, scenarios dashed |

### Step 2 — What's the headline?

Name the one thing the chart must make obvious. "Cash covers 7.5 months at the last quarter's burn; the March loan draw is masking a rising operating burn" is a headline. "Cash position" is not. Sort by the metric of interest, highlight the standout, add the target or minimum line, annotate the turning point.

### Step 3 — Build it

In chat, build an inline chart (an HTML or React artifact with Recharts where the host supports it). For a deck or file, embed the chart via the relevant document skill.

### Standard color palette

- **Favourable:** `#10b981` (green)
- **Unfavourable:** `#ef4444` (red)
- **Neutral / baseline:** `#6366f1` (indigo)
- **Needs review:** `#f59e0b` (amber)
- **Gridlines:** `#f3f4f6` · **Text:** `#374151`

Colour by **business meaning**, not by sign: burn up is red, cash up from a loan draw is neutral, margin up is green.

### Skip the chart when

- The answer is one to three numbers.
- The deliverable is a short email or a narrative memo.
- The user asked for a table or a spreadsheet.

### Ad hoc analysis

For questions the workflows don't cover ("what does a 10% price rise do to margin?", "how much of opex is fixed?"), pull the reports, compute in chat, label assumptions, and pick the visual that fits.

## Other Claude capabilities to leverage

**Document skills.** Board or investor deck → `pptx`; owner pack or memo → `docx`; KPI or scenario workbook → `xlsx`. Read that skill's SKILL.md before building the file.

**Other connectors, when already available.** Drive for the pack, Gmail for an **unsent draft** to the owner or board, Slack for a one-line summary. Never send on the user's behalf.

**Web search.** Public context only (industry margin ranges, interest rate changes). Cite it and keep it separate from the company's figures.

**Clarifying questions.** For multiple-choice inputs (scenario assumptions, comparison period, which KPIs), use the host's question tool (`AskUserQuestion` in Claude Code) rather than prose.

## When to use the bundled workflows

`references/workflows.md` contains the step-by-step sequences for seven scenarios:

- **Monthly owner pack** — the month in one page plus supporting schedules. "Owner pack," "monthly financials," "how did we do last month."
- **Cash runway and burn** — months of cash at the current burn. "Runway," "burn rate," "how long does our cash last."
- **Profit vs cash bridge** — why net income and cash moved differently. "Profitable but no cash," "where did the cash go."
- **KPI scorecard vs targets** — committed KPIs against saved targets. "KPIs," "are we on target," "scorecard."
- **Revenue and customer concentration** — dependence on a few customers or lines. "Concentration," "how dependent are we on X."
- **13-week cash view** — a labelled scenario on user-supplied assumptions. "13-week cash," "cash forecast," "can we make payroll in May."
- **Quarterly board / investor summary** — quarter results, KPIs, cash and asks. "Board update," "investor update," "QBR."

Read `references/workflows.md` when the user's request matches.

## Bundled calculation rules

`references/calculations.md` holds the formulas and thresholds these workflows use (burn, runway, cash bridge, KPI definitions, concentration, scenario mechanics). This plugin ships no executable code: run the calculations in chat from the pulled data and show the inputs.

## Bundled templates

- `assets/owner_monthly_pack.md` — the monthly package for an owner.
- `assets/cash_runway_brief.md` — runway and burn on one page.
- `assets/kpi_scorecard.md` — KPI table vs targets with status.
- `assets/board_update_outline.md` — slide-by-slide quarterly board or investor deck.
- `assets/owner_email.md` — 30-second summary email for an owner.
- `assets/scenario_memo.md` — labelled scenario memo (13-week or what-if).

## Things to watch for

**Profit is not cash.** Receivables, payables, inventory, loan draws and repayments, owner draws and capital purchases all separate the two. Always bridge before advising on cash.

**Financing masks burn.** A loan draw or equity injection raises cash without improving operations. Report operating burn separately from financing.

**Lumpy items distort run rates.** Annual insurance, tax payments, bonuses and one-off purchases spike a single month. Use a trailing 3-month average and name the exceptions.

**Scenarios are not forecasts.** Every forward-looking figure names its assumptions, the source of each (user / historical pattern the user confirmed), and is labelled "Scenario". Never fill an assumption silently.

**Basis consistency.** KPIs mixing cash-basis revenue with accrual-basis A/R are wrong. State the basis on every KPI.

**No formal close, audit or consolidation claims.** Combining several companies is an illustrative combined total, not a consolidation. Read-only analysis is not a closed period.

**Hand off when the question is narrower.** Variance detail → `controller`; who to collect from → `receivables`; what to pay and when → `payables`; many clients at once → `accounting-firm`.
