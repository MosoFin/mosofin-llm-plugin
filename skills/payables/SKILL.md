---
name: payables
description: Battle-tested accounts payable and spend workflows for AP clerks, controllers and owners, using the MosoFin connector. Use whenever the user is looking at what the business owes, what it spends and with whom, rather than asking one quick number. Trigger on phrases like "A/P aging," "what bills are due," "payment run," "who should we pay this week," "vendor spend," "top vendors," "did we pay this bill twice," "duplicate bills," "spend by category," "where is the money going," "vendor concentration," "subscriptions," "recurring spend," "cash commitments," "bills due next two weeks." Also trigger when the user wants a payment run proposal, vendor spend report or spend review memo from QuickBooks or other connected books, even if they don't say "MosoFin".
---

# MosoFin workflows for payables and spend

This skill helps AP clerks, controllers and owners see what the business owes, when it's due, and where the money goes: which bills to pay first, which vendors matter most, which bills might be duplicates, and which recurring costs could be cut. Output from these workflows feeds payment decisions and spend conversations, so it is built to be traceable to the bills and payments in the books.

## Required: the MosoFin connector

These workflows rely on the **MosoFin connector**, which reads live data from the connected accounting platform. Before running anything, check that MosoFin tools are in this session (`list_workspaces`, `invoke_datasource_api_tool`; your host prefixes them, e.g. `mcp__plugin_mosofin_mosofin__list_workspaces`).

If they aren't present, follow the `connect` skill: keep the user's request, give the one sign-in step for this host, and continue when they say *done*. Never answer from memory.

Don't fabricate placeholder figures. Payment decisions get made on real balances.

## Before you analyze

- **Confirm scope.** Workspace by name (see `workspaces`), company by `display_name` (see `connections`), the as-of date or period as concrete `YYYY-MM-DD` dates, and accounting basis. Read the scope back before the first data call.
- **Load the review profile.** Use the `review-profile` skill for materiality, payment terms policy, minimum cash buffer, critical vendors and known context (for example "rent is paid by direct debit on the 1st"). If nothing is saved and the analysis depends on a policy (for example how much cash to keep), ask **one** question and offer to save the answer.
- **Check the A/P ledger first.** If the A/P aging total doesn't agree with the A/P control account, or vendor credits and payments sit unapplied, say so before proposing payments. See `/mosofin:books-health` and the `bookkeeper` skill.
- **Evidence discipline:**
  - A suspected duplicate bill is a *candidate* until a person checks the vendor invoice. Label findings **observed**, **possible** or **unknown**.
  - Vendor spend in the books is what was recorded, not what was contracted. Don't infer contract terms, prices or renewals the records don't show.
  - Ignore vendors below materiality in rankings; group them as "Other".
  - If the user disputes a number, re-pull before conceding.
- **Answer shape:** conclusion first (how much is due, how much is overdue, the one decision), then the bills or vendors with amounts and dates, then next steps with an owner. Close with a **Data sources** line (datasource + tool + `fetched_at` from `provenance`) and say whether figures are live or fixture (`mock`).

## Core principles

1. **Two-step pattern: pull, then analyze.** Pull the aging and bills, confirm what you got in one line ("A/P aging as of 14 Aug: 63 open bills, $148.2k; vendor expenses Jan–Jul"), then analyze. A payment proposal built on an incomplete bill list pays the wrong vendors.
2. **Show, don't tell.** Owners scan. When the answer compares vendors, categories or weeks, build a visual or a tight table. See "Choosing the right visualization".
3. **Batch small reads, sequence heavy ones.** Aging, balance sheet and vendor balance reads may run in parallel. Bill and purchase searches over a year are heavy; run them one at a time and follow pagination to the end.
4. **Read-only, always.** MosoFin cannot pay, schedule, approve, void or delete bills. A payment run is a **proposal** for a person to approve and execute in their payment system.

## Choosing the right visualization

Decide by the question, not by the workflow.

### Step 1 — What is the user trying to see?

| Question type | Visualization family |
|---|---|
| How much is owed and how late | Stacked bar by aging bucket (current, 1–30, 31–60, 61–90, 90+) |
| What's due when | Bar per week for the next 4–8 weeks, with available cash as a reference line if known |
| Top vendors by spend | Sorted horizontal bar, top 10 + "Other" |
| Spend by category over time | Line chart per category, or stacked bar per month |
| Concentration | Cumulative share (Pareto) line over a sorted vendor bar |
| Recurring spend inventory | Table, sorted by annualized amount |

### Step 2 — What's the headline?

Name the one thing the chart must make obvious. "$38k is due in the next 14 days; two vendors are 60+ days overdue" is a headline. "A/P aging" is not. Sort by the metric of interest, highlight the standout, add the reference line. If you can't name the headline, the analysis isn't finished.

### Step 3 — Build it

In chat, build an inline chart (an HTML or React artifact with Recharts where the host supports it). For a file, embed the chart via the relevant document skill.

### Standard color palette

- **Favourable:** `#10b981` (green)
- **Unfavourable:** `#ef4444` (red)
- **Neutral / baseline:** `#6366f1` (indigo)
- **Needs review:** `#f59e0b` (amber)
- **Gridlines:** `#f3f4f6` · **Text:** `#374151`

Colour by **business meaning**, not by sign: spend up is red unless the profile says it's a planned investment; overdue A/P is red; bills not yet due are neutral.

### Skip the chart when

- The answer is one to three numbers.
- The deliverable is a payment proposal list or a memo.
- The user asked for a table or a spreadsheet.

### Ad hoc analysis

For questions the workflows don't cover ("how much do we spend on software per employee?", "which vendors raised prices this year?"), pull the records, compute in chat, and pick the visual that fits.

## Other Claude capabilities to leverage

**Document skills.** Payment run proposal or vendor list → `xlsx`; spend review memo → `docx`; owner slides → `pptx`. Read that skill's SKILL.md before building the file.

**Other connectors, when already available.** Drive for the workbook, Gmail for an **unsent draft** to a vendor or approver, Slack for a one-line "due this week" note. Never send or pay on the user's behalf.

**Web search.** Useful for public context (a vendor's published price change, what a merchant descriptor is). Never for the company's own numbers.

**Clarifying questions.** For multiple-choice inputs (as-of date, cash available, which vendors are critical), use the host's question tool (`AskUserQuestion` in Claude Code) rather than prose.

## When to use the bundled workflows

`references/workflows.md` contains the step-by-step sequences for seven scenarios:

- **A/P aging and payment run proposal** — what's owed, how late, and a proposed list of bills to pay within a cash limit. "A/P aging," "payment run," "who should we pay." Also `/mosofin:ap-aging`.
- **Vendor spend analysis** — spend by vendor for a period, with change against a comparison period. "Vendor spend," "top vendors."
- **Duplicate bill detection** — candidate duplicate bills and double payments. "Did we pay this twice," "duplicate bills."
- **Spend trend by category** — expense accounts by month, with the categories that moved. "Spend by category," "where is the money going."
- **Vendor concentration** — how dependent spend is on a few vendors. "Vendor concentration," "single-supplier risk."
- **Recurring and subscription spend inventory** — every recurring vendor with its cadence and annualized cost. "Subscriptions," "recurring spend," "what can we cut."
- **Upcoming cash commitments** — bills due in the next 2 and 4 weeks, against available cash if the user gives it. "What's due," "cash commitments," "bills due next two weeks."

Read `references/workflows.md` when the user's request matches.

## Bundled calculation rules

`references/calculations.md` holds the formulas and thresholds these workflows use (aging buckets, days payable outstanding, payment prioritization, duplicate matching, spend change, concentration, recurrence detection, due-date windows). This plugin ships no executable code: run the calculations in chat from the pulled data and show the inputs.

## Bundled templates

- `assets/payment_run_proposal.md` — proposed bills to pay, within a cash limit, for a person to approve.
- `assets/vendor_spend_report.md` — top vendors and changes for a period.
- `assets/duplicate_bill_findings.md` — candidate duplicate bills and payments, side by side.
- `assets/spend_review_memo.md` — category trend memo for an owner or management team.
- `assets/recurring_spend_inventory.md` — recurring vendors with cadence and annualized cost.

## Things to watch for

**Due dates depend on data entry.** A bill entered without a due date or terms defaults to its bill date. Say how many bills had no terms; their "overdue" status may be misleading.

**Unapplied vendor credits shrink the real balance.** Net open vendor credits against that vendor's bills before proposing a payment, and show the netting.

**Card and direct-debit spend isn't in A/P.** Purchases paid by card or direct debit appear as expenses, not bills. Spend analysis uses `get_vendor_expenses` and purchases; the aging only covers bills.

**Cash vs accrual.** On cash basis, unpaid bills aren't expenses yet. State the basis on every spend figure.

**Payment proposals aren't payments.** MosoFin can't pay, schedule or approve. Every proposal says "for approval" and leaves execution to the person and their payment system.

**Hand off when it's not payables.** Variance explanations belong to `controller`; cash runway and owner reporting to `fractional-cfo`; A/P ledger cleanup to `bookkeeper`; customer collections to `receivables`.
