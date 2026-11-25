---
name: controller
description: Battle-tested month-end review workflows for controllers, accountants and close reviewers, using the MosoFin connector. Use whenever the user is reviewing a closed or closing period rather than asking one quick number. Trigger on phrases like "flux analysis," "P&L variance," "why did expenses jump," "review the balance sheet," "does the trial balance tie," "cutoff review," "accruals," "unusual expenses," "gross margin by customer," "budget vs actual," "close sign-off," "month-end review." Also trigger when the user wants a variance memo, review notes, or a close sign-off summary from QuickBooks or other connected books, even if they don't say "MosoFin".
---

# MosoFin workflows for controllers and close reviewers

This skill helps controllers, staff accountants and reviewers check a period's books the way a reviewer would before sign-off: what moved, whether the balance sheet makes sense, whether the trial balance ties, and what needs follow-up. Output from these workflows lands in front of owners, partners and auditors, so it is built to be traceable to the source reports, not just plausible.

## Required: the MosoFin connector

These workflows rely on the **MosoFin connector**, which reads live data from the connected accounting platform. Before running anything, check that MosoFin tools are in this session (`list_workspaces`, `invoke_datasource_api_tool`; your host prefixes them, e.g. `mcp__plugin_mosofin_mosofin__list_workspaces`).

If they aren't present, follow the `connect` skill: keep the user's request, give the one sign-in step for this host, and continue when they say *done*. Never answer from memory.

Don't fabricate placeholder figures. Review conclusions get signed on real numbers.

## Before you analyze

- **Confirm scope.** Workspace by name (see `workspaces`), company by `display_name` (see `connections`), period as concrete `YYYY-MM-DD` dates, and accounting basis (accrual or cash). Read the scope back before the first data call.
- **Load the review profile.** Use the `review-profile` skill for materiality, fiscal year, basis, close calendar and known context. Judge against those, not generic rules of thumb. If nothing is saved and the analysis depends on a threshold, ask **one** question and offer to save the answer.
- **Check books health first.** If the trial balance doesn't balance, uncategorized or suspense balances are material, or the period has unreconciled activity the user mentions, say so before any variance commentary. See `/mosofin:books-health`.
- **Evidence discipline:**
  - Treat a variance below the materiality threshold (default: the greater of 10% and 5% of the line's prior-period amount or a fixed amount the profile sets) as noise.
  - Never call an item an error, misstatement or fraud without evidence from the retrieved records. Label causes as *observed*, *possible* or *unknown*.
  - Check whether a "variance" is a reclass (offsetting move between two accounts) before treating it as a real change.
  - If the user disputes a number, re-pull the report before conceding.
- **Answer shape:** conclusion first, then the material items with amounts, then follow-ups with an owner. Close with a **Data sources** line (datasource + tool + `fetched_at` from `provenance`) and say whether figures are live or fixture (`mock`).

## Core principles

1. **Two-step pattern: pull, then analyze.** Pull the reports, confirm what you got in one line ("P&L Jul and Jun 2026, accrual, 42 lines; balance sheet as of 31 Jul"), then analyze. If the pull is wrong, every comment built on it is wrong, and the reviewer won't catch it until the client does.
2. **Show, don't tell.** Reviewers scan. When the answer compares periods, accounts or entities, build a visual or a tight table. See "Choosing the right visualization".
3. **Batch small reads, sequence heavy ones.** Independent report reads (P&L, balance sheet, aging) may run in parallel. General ledger and transaction searches over long ranges are heavy; run them one at a time and follow pagination to the end.
4. **Read-only, always.** MosoFin cannot post, reclass, accrue or reconcile. Proposed entries are written as review-only suggestions for a person to book.

## Choosing the right visualization

Decide by the question, not by the workflow.

### Step 1 — What is the reviewer trying to see?

| Question type | Visualization family |
|---|---|
| Which lines moved most period over period | Sorted horizontal bar of variance amount (largest absolute first) |
| Trend of a line or ratio over months | Line chart, with a reference line for budget or prior year |
| Revenue to net income walk | Waterfall (bridge) chart |
| Budget vs actual by account | Grouped bars or a variance table with conditional colour |
| Balance sheet composition | Stacked bar per period (not pie) |
| Margin by customer or item | Sorted bar, with the blended margin as a reference line |
| Many accounts × months | Heatmap of % change |

### Step 2 — What's the headline?

Name the one thing the chart must make obvious. "Gross margin fell 4 points because subcontractor cost grew faster than revenue" is a headline. "July P&L" is not. Sort by the metric of interest, highlight the standout, add the reference line, annotate the biggest mover. If you can't name the headline, the analysis isn't finished.

### Step 3 — Build it

In chat, build an inline chart (an HTML or React artifact with Recharts where the host supports it). For a file, embed the chart via the relevant document skill.

### Standard color palette

- **Favourable:** `#10b981` (green)
- **Unfavourable:** `#ef4444` (red)
- **Neutral / baseline:** `#6366f1` (indigo)
- **Needs review:** `#f59e0b` (amber)
- **Gridlines:** `#f3f4f6` · **Text:** `#374151`

Colour by **business meaning**, not by sign: revenue +10% is green, expenses +10% is red, a reclass with no net effect is neutral.

### Skip the chart when

- The answer is one to three numbers.
- The deliverable is a memo where prose reads better.
- The user asked for a table or a spreadsheet.

### Ad hoc analysis

For questions the workflows don't cover ("what share of opex is payroll-related?", "did the new pricing change margin by customer?"), pull the reports, compute in chat, and pick the visual that fits.

## Other Claude capabilities to leverage

**Document skills.** Variance memo or review notes → `docx`; flux workbook → `xlsx`; owner or audit-committee slides → `pptx`. Read that skill's SKILL.md before building the file.

**Other connectors, when already available.** Drive for saving the workbook, Gmail for an **unsent draft** to the client, Slack for a one-line close status. Never send on the user's behalf.

**Web search.** Useful for public context only (a tax rate change, a vendor price increase announced publicly). Never for the company's own numbers.

**Clarifying questions.** For multiple-choice inputs (basis, comparison period, materiality), use the host's question tool (`AskUserQuestion` in Claude Code) rather than prose.

## When to use the bundled workflows

`references/workflows.md` contains the step-by-step sequences for seven scenarios:

- **P&L flux analysis** — period-over-period and year-over-year variance with explanations. "Flux," "variance," "why did X change."
- **Balance sheet review** — every balance sheet line checked for sign, movement and support. "Review the balance sheet," "anything odd on the BS."
- **Trial balance tie-out** — debits equal credits, TB agrees to P&L and balance sheet, retained earnings rolls. "Does the TB tie," "tie out."
- **Cutoff and accrual review** — transactions dated around period end, missing recurring bills, prepaid and accrued balances. "Cutoff," "accruals," "did we miss bills."
- **Unusual expense review** — expenses unusual by amount, frequency, vendor or account. "Unusual expenses," "anything weird in spending."
- **Gross margin by customer and item** — revenue and cost by customer or product. "Margin by customer," "which jobs are unprofitable."
- **Budget vs actual variance** — actuals against the saved budget with commentary. "BvA," "budget vs actual," "are we on budget."

Read `references/workflows.md` when the user's request matches.

## Bundled calculation rules

`references/calculations.md` holds the formulas and thresholds these workflows use (variance %, materiality, reclass detection, TB checks, accrual expectations, margin). This plugin ships no executable code: run the calculations in chat from the pulled data and show the inputs.

## Bundled templates

- `assets/flux_analysis_memo.md` — variance memo with material items, causes and follow-ups.
- `assets/balance_sheet_review.md` — line-by-line balance sheet review notes.
- `assets/variance_commentary.md` — budget vs actual commentary for owners.
- `assets/unusual_expense_log.md` — fact / possible explanation / missing evidence / owner log.
- `assets/close_signoff_summary.md` — one-page close summary for the approving reviewer.

## Things to watch for

**Basis changes the answer.** Cash and accrual P&Ls disagree by design. State the basis on every figure, and never compare a cash period with an accrual period.

**Reclasses look like variances.** A cost moved from one account to another shows as an increase and a decrease. Net them before commenting.

**Partial periods mislead.** A month still open, or a 4-vs-5-week month, distorts the comparison. Say so; normalize per day or per week when it matters.

**Report totals vs control accounts.** The A/R and A/P aging totals should agree with the balance sheet control accounts as of the same date. A difference is a finding, not a rounding note.

**Review-only output.** Suggested adjusting entries are written as proposals with accounts, amounts and support, for a person to book. Never imply they were posted.

**No formal close or audit claims.** A read-only review is not a closed period, a reconciliation, an audit, or a consolidation. Don't say it is.
