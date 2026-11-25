---
name: accounting-firm
description: Battle-tested multi-client workflows for accounting firms, CAS teams, outsourced bookkeepers and fractional controllers who manage many client workspaces, using the MosoFin connector. Use whenever the user is working across a book of clients rather than inside one company's numbers. Trigger on phrases like "how are all my clients doing," "which clients need attention," "client portfolio," "month-end status across clients," "prep for my client meeting," "advisory meeting," "scope a cleanup," "how far behind are their books," "compare these companies," "benchmark my clients," "year-end readiness," "ready for the tax preparer," "1099 prep." Also trigger when the user mentions a firm, a practice, a client list or several workspaces, even if they don't say "MosoFin".
---

# MosoFin workflows for accounting firms and CAS teams

This skill helps firm partners, client managers and CAS teams run the work that spans many clients: which clients need attention this week, where each one is in month-end, what to bring to the next advisory meeting, how big a cleanup is, and whether a client is ready for year-end. Output from these workflows goes to partners, staff and clients, so every figure stays attached to the one client it came from.

## Required: the MosoFin connector

These workflows rely on the **MosoFin connector**, which reads live data from each client's connected accounting platform. Before running anything, check that MosoFin tools are in this session (`list_workspaces`, `invoke_datasource_api_tool`; your host prefixes them, e.g. `mcp__plugin_mosofin_mosofin__list_workspaces`).

If they aren't present, follow the `connect` skill: keep the user's request, give the one sign-in step for this host, and continue when they say *done*. Never answer from memory.

Don't fabricate placeholder figures. A client conversation built on invented numbers costs the firm the client.

## Before you analyze

- **Confirm the client scope.** Call `list_workspaces`, show workspaces by **name**, and ask which clients are in scope. For more than one, confirm with `list_workspaces(workspace_ids=[…], mode="multi")`; for one, `mode="single"`. Each later call still takes **one** `workspace_id` — the client that call is about. Never auto-pick, and never sweep "everything" unless the user said so.
- **Confirm company, period and basis per client.** Companies by `display_name` (see `connections`); period as concrete `YYYY-MM-DD`; accrual or cash. Clients can differ on fiscal year and basis — check the review profile per client.
- **Load the review profile.** Use the `review-profile` skill per client for materiality, fiscal year, basis and known context ("payroll moved to a new provider in March"). If nothing is saved and a threshold matters, ask **one** question and offer to save the answer.
- **Keep clients apart.** One client's facts never appear in another client's conclusion, draft or meeting brief. Name the client on every table and every figure.
- **Evidence discipline:**
  - Treat moves below materiality as noise; treat a month with fewer than ~10 transactions as too thin to trend.
  - Never call a client's books wrong, late or careless without evidence. Label causes as *observed*, *possible* or *unknown*.
  - A disconnected company is "not read", not "fine" and not "behind". Give the `reconnect_url`.
  - If the user disputes a number, re-pull before conceding.
- **Answer shape:** conclusion first (who needs attention and why), then the evidence per client, then next actions with an owner. Close with a **Data sources** line per client (datasource + tool + `fetched_at` from `provenance`) and say whether figures are live or fixture (`mock`).

## Core principles

1. **Two-step pattern: pull, then analyze.** Pull each client's reports, confirm what you got in one line per client ("Acme Consulting · Acme Beauty LLC: P&L Jul and Jun 2026, accrual; balance sheet as of 31 Jul"), then analyze. A sweep with one silently missing client reads as "all clear" when it isn't.
2. **Show, don't tell.** Partners scan a portfolio. One sorted table with the clients that need a look first beats a page per client.
3. **Batch within a client, sequence across clients.** Independent reads for one client may run in parallel. Move to the next client only after the current one is complete; for large books, delegate the sweep to the `client-portfolio-analyst` agent.
4. **Read-only, always.** MosoFin cannot post, reclass, reconcile, send or close anything. Client emails are unsent drafts; proposed entries are review-only suggestions for a person to book.

## Choosing the right visualization

Decide by the question, not by the workflow.

### Step 1 — What is the user trying to see?

| Question type | Visualization family |
|---|---|
| Which clients need attention | Sorted table, flagged clients first, flag column in amber |
| One KPI across clients | Sorted horizontal bar, one bar per client, book median as a reference line |
| Two KPIs across clients (e.g. growth vs margin) | Scatter, one dot per client, labelled |
| One client's trend for a meeting | Line chart, with prior year or budget as a reference line |
| Month-end progress across clients | Status grid (client × step), coloured by state |
| Cleanup size | Bar of uncategorized / unreviewed volume by month |
| Selected-company comparison | Small multiples — one mini-chart per company, never one stacked total |

### Step 2 — What's the headline?

Name the one thing the visual must make obvious. "3 of 14 clients need a look; Harbor Builders has $22k in uncategorized expenses" is a headline. "Client portfolio, July" is not. Sort by the metric of interest, highlight the outliers, add the reference line. If you can't name the headline, the analysis isn't finished.

### Step 3 — Build it

In chat, build an inline chart or table (an HTML or React artifact with Recharts where the host supports it). For a file, embed the visual via the relevant document skill.

### Standard color palette

- **Favourable:** `#10b981` (green)
- **Unfavourable:** `#ef4444` (red)
- **Neutral / baseline:** `#6366f1` (indigo)
- **Needs review:** `#f59e0b` (amber)
- **Gridlines:** `#f3f4f6` · **Text:** `#374151`

Colour by **business meaning**, not by sign: a client's revenue +10% is green, its A/R over 90 days +10% is red.

### Skip the chart when

- The answer is one to three numbers.
- The deliverable is a client email or meeting brief where prose reads better.
- The user asked for a table or a spreadsheet.

### Ad hoc analysis

For questions the workflows don't cover ("which clients still have opening balance equity?", "who hasn't had a transaction in 30 days?"), pull per client, compute in chat, and show one row per client.

## Other Claude capabilities to leverage

**Document skills.** Portfolio report or meeting brief → `docx`; book-wide KPI table or month-end tracker → `xlsx`; client advisory slides → `pptx`. Read that skill's SKILL.md before building the file.

**Other connectors, when already available.** Drive for the client folder, Gmail for an **unsent draft** to one client, Slack for an internal status line, a practice-management tool if one is connected. Never send on the user's behalf, and never put two clients in one message.

**Web search.** Useful for public context only (filing deadlines, 1099 thresholds, an industry benchmark for a meeting). Never for a client's own numbers, and cite the source.

**Clarifying questions.** For multiple-choice inputs (which clients, single vs multi, period), use the host's question tool (`AskUserQuestion` in Claude Code) rather than prose.

## When to use the bundled workflows

`references/workflows.md` contains the step-by-step sequences for seven scenarios:

- **Portfolio health sweep** — one table across the confirmed clients, clients that need a look first. "How are all my clients doing," "which clients need attention."
- **Multi-client month-end status** — the same review run one client at a time, stopping between clients. "Month-end across clients," "where are we on close."
- **Client advisory meeting prep** — a one-page brief for a client meeting. "Prep for my meeting with," "what should I bring up with."
- **Cleanup scoping** — how far behind a new or neglected client is and how big the job is. "Scope a cleanup," "how far behind are they," "catch-up."
- **Selected-company comparison** — the same metric across companies the user picks, never called a consolidation. "Compare these companies," "side by side."
- **Client KPI benchmarking** — each client's KPIs against the rest of the book, anonymized in anything shared. "Benchmark my clients," "how does this client compare."
- **Year-end and tax-prep readiness** — what a client needs before the books go to the tax preparer. "Year-end ready," "tax prep," "1099 prep."

Read `references/workflows.md` when the user's request matches.

## Bundled calculation rules

`references/calculations.md` holds the formulas and thresholds these workflows use (portfolio flags, month-end step tests, cleanup sizing, KPI definitions, anonymization, year-end checks). This plugin ships no executable code: run the calculations in chat from the pulled data and show the inputs.

## Bundled templates

- `assets/client_portfolio_report.md` — one-table portfolio view with flagged clients first.
- `assets/advisory_meeting_brief.md` — one-page brief for a client advisory meeting.
- `assets/cleanup_scope_estimate.md` — cleanup findings and effort drivers for an engagement proposal.
- `assets/client_monthly_email.md` — unsent monthly update draft to one client.
- `assets/year_end_readiness_checklist.md` — year-end / tax-prep readiness checklist per client.

## Things to watch for

**Cross-client leakage.** The biggest risk in multi-client work is one client's figure in another client's output. Build each client's section from that client's pulls only, and re-check names before anything leaves the chat.

**Different fiscal years and bases.** A June year-end client and a December one aren't comparable on "YTD". Compare like periods or say why not.

**Disconnected is not clean.** A client whose company file is disconnected has no data, not good data. List it under **Not read** with the `reconnect_url`.

**Benchmarks inside one book are small samples.** Ten clients aren't an industry. Say how many clients a median is based on, and don't rank clients from different industries on margin.

**Shared outputs are anonymized.** Anything that compares clients and might be seen by a client uses "Client A / B / C" or industry labels, never names.

**No formal close, consolidation or tax advice.** A read-only review isn't a closed period, a consolidation, or a tax position. Say what a person must still decide.
