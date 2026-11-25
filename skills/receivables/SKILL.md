---
name: receivables
description: Battle-tested accounts receivable and collections workflows for AR specialists, bookkeepers and owners chasing cash, using the MosoFin connector. Use whenever the user is working on who owes money, how old it is, and who to follow up with first. Trigger on phrases like "A/R aging," "who owes us," "overdue invoices," "collections list," "who should I chase," "DSO," "days sales outstanding," "how long do customers take to pay," "customer concentration," "credit risk," "unapplied payments," "open credit memos," "draft a reminder email." Also trigger when the user wants a collections priority list or payment reminder drafts from QuickBooks or other connected books, even if they don't say "MosoFin".
---

# MosoFin workflows for receivables and collections

This skill helps AR specialists, bookkeepers and owners turn the receivables ledger into action: what is owed, how old it is, whether the aging agrees with the books, which customers to contact first, and what to say. Output from these workflows reaches customers indirectly through a person, so every balance must be supported and every message stays a review-only draft.

## Required: the MosoFin connector

These workflows rely on the **MosoFin connector**, which reads live data from the connected accounting platform. Before running anything, check that MosoFin tools are in this session (`list_workspaces`, `invoke_datasource_api_tool`; your host prefixes them, e.g. `mcp__plugin_mosofin_mosofin__list_workspaces`).

If they aren't present, follow the `connect` skill: keep the user's request, give the one sign-in step for this host, and continue when they say *done*. Never answer from memory.

Don't fabricate placeholder balances. A customer chased for money they don't owe is a damaged relationship.

## Before you analyze

- **Confirm scope.** Workspace by name (see `workspaces`), company by `display_name` (see `connections`), the as-of date as a concrete `YYYY-MM-DD`, and for trends the period range. Read the scope back before the first data call.
- **Load the review profile.** Use the `review-profile` skill for payment terms, collections thresholds, customers on hold or in dispute, and known context ("Northwind pays quarterly by agreement"). Judge against those. If nothing is saved and the analysis depends on a threshold, ask **one** question and offer to save the answer.
- **Tie the aging first.** If the aging total doesn't agree with the A/R control account on the balance sheet as of the same date, say so before any collections advice. See Workflow 1.
- **Evidence discipline:**
  - Net each customer's balance for unapplied payments and open credit memos before calling anything overdue.
  - Never call a customer delinquent, disputed or a credit risk without evidence from the retrieved records. Label causes as *observed*, *possible* or *unknown*.
  - Treat balances under the profile's minimum (default: the greater of 100 in the company currency and 0.5% of total A/R) as noise in priority lists.
  - If the user disputes a balance, re-pull the invoices and payments before conceding.
- **Answer shape:** conclusion first (total A/R, share past due, the one customer that matters most), then the prioritized list, then follow-ups with an owner. Close with a **Data sources** line (datasource + tool + `fetched_at` from `provenance`) and say whether figures are live or fixture (`mock`).

## Core principles

1. **Two-step pattern: pull, then analyze.** Pull the aging and supporting records, confirm what you got in one line ("A/R aging as of 31 Jul 2026, 38 customers, $412k; balance sheet A/R $412k"), then analyze.
2. **Show, don't tell.** Collections people scan. Aging buckets, concentration and trends read faster as a chart or a tight table.
3. **Batch small reads, sequence heavy ones.** Aging, balance sheet and customer balance reads may run in parallel. Invoice and payment searches over long ranges are heavy; run them one at a time and follow pagination to the end.
4. **Read-only, always.** MosoFin cannot apply payments, issue credit memos, write off balances or send emails. Reminders are drafts for a person to review and send; adjustments are suggestions for a person to book.

## Choosing the right visualization

Decide by the question, not by the workflow.

### Step 1 — What is the user trying to see?

| Question type | Visualization family |
|---|---|
| How old is the A/R | Stacked bar of aging buckets (current, 1–30, 31–60, 61–90, 90+) |
| Who owes the most past due | Sorted horizontal bar of past-due balance by customer |
| Is collection getting slower | Line chart of DSO by month, with the terms days as a reference line |
| How concentrated is A/R or revenue | Sorted bar of share by customer with a cumulative line (Pareto) |
| How long each customer takes to pay | Dot or bar of average days to pay, with terms as a reference line |
| Aging movement month to month | Small multiples of bucket balances per month |

### Step 2 — What's the headline?

Name the one thing the chart must make obvious. "Two customers hold 61% of past-due A/R, and both slipped past 60 days this month" is a headline. "A/R aging" is not. Sort by balance, highlight the standout, add the terms reference line, annotate the biggest mover.

### Step 3 — Build it

In chat, build an inline chart (an HTML or React artifact with Recharts where the host supports it). For a file, embed the chart via the relevant document skill.

### Standard color palette

- **Favourable:** `#10b981` (green)
- **Unfavourable:** `#ef4444` (red)
- **Neutral / baseline:** `#6366f1` (indigo)
- **Needs review:** `#f59e0b` (amber)
- **Gridlines:** `#f3f4f6` · **Text:** `#374151`

Colour by **business meaning**: current balances neutral or green, 61–90 amber, 90+ red; DSO falling is green.

### Skip the chart when

- The answer is one to three numbers.
- The deliverable is a reminder draft or a memo.
- The user asked for a table or a spreadsheet.

### Ad hoc analysis

For questions the workflows don't cover ("which sales rep's customers pay slowest?", "how much A/R is from invoices over 10k?"), pull the records, compute in chat, and pick the visual that fits.

## Other Claude capabilities to leverage

**Document skills.** Collections list or aging workbook → `xlsx`; credit risk brief → `docx`. Read that skill's SKILL.md before building the file.

**Other connectors, when already available.** Gmail or Outlook for an **unsent draft** per customer, Drive for the workbook, Slack for a one-line A/R status. Never send on the user's behalf.

**Web search.** Public context only (a customer's announced bankruptcy filing or acquisition). Never for the company's own balances.

**Clarifying questions.** For multiple-choice inputs (as-of date, which customers to include, tone of reminders), use the host's question tool (`AskUserQuestion` in Claude Code) rather than prose.

## When to use the bundled workflows

`references/workflows.md` contains the step-by-step sequences for seven scenarios:

- **A/R aging review and control-account tie** — aging by bucket, agreed to the balance sheet. "A/R aging," "who owes us," "does the aging tie."
- **Collections priority list** — who to contact first and why. "Who should I chase," "collections list."
- **DSO trend** — days sales outstanding by month. "DSO," "are we collecting slower."
- **Customer concentration and credit risk** — dependence on a few customers and early warning signs. "Concentration," "credit risk," "which customers worry you."
- **Unapplied payments and open credit memos** — balances that make the aging wrong. "Unapplied payments," "open credits."
- **Invoice-to-cash cycle** — actual days to pay by customer vs terms. "How long do customers take to pay."
- **Reminder email drafts** — review-only drafts per customer. "Draft reminder emails," "write to overdue customers."

Read `references/workflows.md` when the user's request matches.

## Bundled calculation rules

`references/calculations.md` holds the formulas and thresholds these workflows use (aging buckets, net supported balance, priority score, DSO, days to pay, concentration). This plugin ships no executable code: run the calculations in chat from the pulled data and show the inputs.

## Bundled templates

- `assets/ar_aging_review.md` — aging summary with the control-account tie and exceptions.
- `assets/collections_priority_list.md` — ranked contact list with reason and next step.
- `assets/customer_reminder_drafts.md` — review-only reminder drafts by aging stage.
- `assets/dso_summary.md` — DSO trend note for owners.
- `assets/credit_risk_brief.md` — concentration and early-warning brief.

## Things to watch for

**Net before you chase.** An open invoice with a matching unapplied payment or credit memo is not owed. Never chase a customer whose net supported balance is zero or negative.

**Aging date basis.** Aging by invoice date and aging by due date give different buckets. State which one the report used; prefer due date for collections.

**Disputes and agreements.** A balance under dispute or on an agreed payment plan belongs on a separate list, not the chase list. Use known context from the review profile.

**Report totals vs control accounts.** The aging total should equal the balance sheet A/R as of the same date. A difference is a finding (often journal entries posted straight to A/R), not a rounding note.

**Review-only output.** Reminder emails are drafts. Suggested write-offs or credit memos are proposals with amounts and support for a person to decide. Never imply anything was sent, applied or written off.

**Customer data stays private.** Show customer names and balances only within the confirmed workspace; never mix one client's customers into another client's review.
