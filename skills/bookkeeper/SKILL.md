---
name: bookkeeper
description: Battle-tested month-end close and cleanup workflows for bookkeepers and close preparers, using the MosoFin connector. Use whenever the user is getting a company's books ready to close or cleaning them up, rather than asking one quick number. Trigger on phrases like "are the books ready to close," "close checklist," "uncategorized transactions," "suspense account," "ask my accountant," "duplicate bills," "duplicate payments," "undeposited funds," "unapplied payments," "open credits," "review journal entries," "clean up the vendor list," "1099 vendors," "missing customer details," "deposits look low," "cleanup." Also trigger when the user wants a client question list, cleanup findings or a close readiness checklist from QuickBooks or other connected books, even if they don't say "MosoFin".
---

# MosoFin workflows for bookkeepers and close preparers

This skill helps bookkeepers and close preparers get a company's books into a state a reviewer can sign: nothing left uncategorized, no duplicates, payments applied, journal entries explained, and master data clean enough for year-end. Output from these workflows goes to the reviewing accountant and, as questions, to the client, so it is built to be specific and traceable to the source records.

## Required: the MosoFin connector

These workflows rely on the **MosoFin connector**, which reads live data from the connected accounting platform. Before running anything, check that MosoFin tools are in this session (`list_workspaces`, `invoke_datasource_api_tool`; your host prefixes them, e.g. `mcp__plugin_mosofin_mosofin__list_workspaces`).

If they aren't present, follow the `connect` skill: keep the user's request, give the one sign-in step for this host, and continue when they say *done*. Never answer from memory.

Don't fabricate placeholder figures or transactions. A cleanup list with invented items costs the client time and trust.

## Before you analyze

- **Confirm scope.** Workspace by name (see `workspaces`), company by `display_name` (see `connections`), period as concrete `YYYY-MM-DD` dates, and accounting basis (accrual or cash). Read the scope back before the first data call.
- **Load the review profile.** Use the `review-profile` skill for materiality, fiscal year, close calendar, known context (for example "owner pays the phone bill personally, reimbursed quarterly") and the client's naming conventions. If nothing is saved and a check depends on a threshold, ask **one** question and offer to save the answer.
- **Know what MosoFin can't see.** MosoFin reads the books, not the bank. It cannot see bank feeds, bank statements, the "For review" queue or reconciliation status. Say so whenever a check would normally need them, and never imply an account is reconciled.
- **Evidence discipline:**
  - A suspected duplicate is a *candidate* until a person checks the source documents. Label every finding **observed**, **possible** or **unknown**.
  - Never call an item an error, misposting or fraud without evidence from the retrieved records.
  - Respect known context: don't re-flag items the profile lists as explained.
  - If the user disputes a finding, re-pull the records before conceding.
- **Answer shape:** conclusion first (ready / not ready, how many items), then the items with amounts and dates, then the follow-ups with an owner. Close with a **Data sources** line (datasource + tool + `fetched_at` from `provenance`) and say whether figures are live or fixture (`mock`).

## Core principles

1. **Two-step pattern: pull, then analyze.** Pull the records, confirm what you got in one line ("312 purchases and 48 bills, 1–31 Jul 2026; balance sheet as of 31 Jul"), then analyze. If the pull missed a page, the cleanup list is incomplete and nobody will notice until next month.
2. **Show, don't tell.** Cleanup work is a list, so a tight table usually beats a chart. Use a visual when the question is about size or trend. See "Choosing the right visualization".
3. **Batch small reads, sequence heavy ones.** Independent report reads (balance sheet, trial balance, aging) may run in parallel. Transaction searches over long ranges are heavy; run them one at a time and follow `pagination.has_more` to the end before concluding "none found".
4. **Read-only, always.** MosoFin cannot recategorize, delete, apply, void or post. Every fix is written as a review-only suggestion for a person to make in the books.

## Choosing the right visualization

Decide by the question, not by the workflow.

### Step 1 — What is the bookkeeper trying to see?

| Question type | Visualization family |
|---|---|
| How much is left to clean up, by type | Sorted horizontal bar of amount (or count) per issue type |
| Is the backlog shrinking month to month | Line chart of uncategorized / suspense balance by month |
| Deposits or purchases vs prior months | Bar per month with the trailing average as a reference line |
| Age of undeposited funds or open credits | Bucketed bar (0–7, 8–30, 31–60, 60+ days) |
| Which accounts journal entries touch | Sorted bar of JE amount by account |
| Item-level cleanup | Table, not a chart |

### Step 2 — What's the headline?

Name the one thing the chart must make obvious. "$4,180 is still uncategorized, 70% of it from three card merchants" is a headline. "Cleanup status" is not. Sort by amount, highlight the biggest bucket, add the reference line. If you can't name the headline, the analysis isn't finished.

### Step 3 — Build it

In chat, build an inline chart (an HTML or React artifact with Recharts where the host supports it). For a file, embed the chart via the relevant document skill.

### Standard color palette

- **Favourable / done:** `#10b981` (green)
- **Unfavourable / blocking close:** `#ef4444` (red)
- **Neutral / baseline:** `#6366f1` (indigo)
- **Needs review:** `#f59e0b` (amber)
- **Gridlines:** `#f3f4f6` · **Text:** `#374151`

Colour by **business meaning**, not by sign: a shrinking suspense balance is green; a growing one is red.

### Skip the chart when

- The answer is one to three numbers.
- The deliverable is an item list or a client question list.
- The user asked for a table or a spreadsheet.

### Ad hoc analysis

For questions the workflows don't cover ("which merchants keep landing in uncategorized?", "how many transactions have no memo?"), pull the records, compute in chat, and pick the format that fits.

## Other Claude capabilities to leverage

**Document skills.** Cleanup findings or close checklist → `docx`; item lists for working through → `xlsx`. Read that skill's SKILL.md before building the file.

**Other connectors, when already available.** Drive for saving the workbook, Gmail for an **unsent draft** of the client question list, Slack for a one-line close status. Never send on the user's behalf.

**Web search.** Useful for public context only (what a merchant descriptor belongs to, 1099 filing deadlines). Never for the company's own numbers.

**Clarifying questions.** For multiple-choice inputs (period, basis, which duplicate rule), use the host's question tool (`AskUserQuestion` in Claude Code) rather than prose.

## When to use the bundled workflows

`references/workflows.md` contains the step-by-step sequences for seven scenarios:

- **Month-end close readiness** — one pass over everything that blocks a close. "Are the books ready to close," "close checklist," "what's left before close." Also `/mosofin:month-end-close`.
- **Uncategorized and suspense cleanup** — every balance in uncategorized, suspense or "ask my accountant", itemized. "Uncategorized," "suspense," "clean up the books."
- **Duplicate transaction check** — candidate duplicate bills, purchases and payments. "Duplicate bills," "did we pay this twice."
- **Undeposited funds and unapplied payments/credits** — payments not deposited, payments and credits not applied to invoices or bills. "Undeposited funds," "unapplied payments," "open credits."
- **Journal entry review** — manual journal entries in the period, with reason and support. "Review journal entries," "JE review."
- **Customer and vendor list hygiene** — missing details, inactive records with balances, near-duplicate names, 1099 readiness. "Clean up the vendor list," "1099 vendors," "duplicate customers."
- **Deposits and purchases sanity vs prior months** — period totals against the trailing months to catch missing or doubled activity. "Deposits look low," "did we miss transactions."

Read `references/workflows.md` when the user's request matches.

## Bundled calculation rules

`references/calculations.md` holds the rules these workflows use (cleanup balances, duplicate matching, aging of undeposited and unapplied items, JE flags, list hygiene, activity sanity bands). This plugin ships no executable code: run the calculations in chat from the pulled data and show the inputs.

## Bundled templates

- `assets/close_readiness_checklist.md` — pass/fail checklist of what blocks the close.
- `assets/cleanup_findings_report.md` — itemized cleanup findings with suggested fixes and owners.
- `assets/client_question_list.md` — questions for the client, as an unsent draft.
- `assets/je_review_log.md` — journal entry review log with support status.
- `assets/vendor_1099_readiness.md` — vendor list status for year-end information returns.

## Things to watch for

**No bank view.** MosoFin can't see bank feeds or statements. A clean MosoFin pass is not a reconciliation; say so in every close readiness answer.

**Duplicates are candidates.** Same vendor and amount can be two real invoices (monthly rent). Show both records' dates, reference numbers and memos so a person can decide.

**Pagination hides items.** Transaction searches return pages. Follow `pagination.next_offset` until `has_more` is false before reporting a count or "none found".

**Cash vs accrual.** Undeposited funds, open bills and unapplied credits behave differently by basis. State the basis on every figure.

**1099 is jurisdiction-specific.** Treat the vendor readiness check as a data completeness check, not tax advice. Eligibility decisions belong to the responsible accountant.

**Review-only output.** Suggested recategorizations, deletions and applications are proposals for a person to make. Never imply they were done. Client questions are drafts, never sent.

**Hand off when it's review, not prep.** Variance explanations and balance sheet sign-off belong to the `controller` skill; bill payment planning to `payables`; collections to `receivables`; multi-client status to `accounting-firm`.
