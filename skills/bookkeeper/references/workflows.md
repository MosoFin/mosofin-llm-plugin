# Bookkeeper and close-preparation workflows

Seven workflow patterns for getting a period's books ready for review. Each ends with a specific deliverable. Operation names below are the QuickBooks catalog names; confirm each one is listed by `get_datasource_tools` for the chosen company before invoking, and pass `workspace_id` (and `data_source_id` when several companies are live) on every call. Follow pagination on every search operation.

## Workflow 1: Month-end close readiness

**When to use:** the user wants to know whether a period is ready to hand to the reviewer, and what's left. Also the backbone of `/mosofin:month-end-close`.

**Operations:** `get_trial_balance`, `get_balance_sheet` (period end), `get_aged_receivables`, `get_aged_payables`, `search_journal_entries`, `search_deposits`; drill-down with `get_general_ledger` or `get_account`.

### Step 1 — Pull the data
> Pull the trial balance and balance sheet as of [period end], A/R and A/P aging as of [period end], and journal entries and deposits dated in [period] for [company].

### Step 2 — Run the readiness checks
Apply `calculations.md` → *Close readiness checks*: trial balance balances; uncategorized / suspense / "ask my accountant" balances are zero; undeposited funds hold nothing older than 7 days; A/R and A/P aging totals agree with their control accounts; no unapplied payments or credits above materiality; journal entries have memos; opening balance equity is zero after the first year.

### Step 3 — Visualize
A pass/fail table is usually enough. If the user wants a visual, a sorted bar of the amount blocking each failed check.

### Step 4 — Output
Build from `assets/close_readiness_checklist.md`. Status is **Ready**, **Ready with open items** or **Not ready**. Always include the line that bank and card reconciliations are outside what MosoFin can see. Hand failed checks to the matching workflow below.

### Common variations
- **Quick status only** — the status line and the failed checks, no detail.
- **Several clients** — run per company separately; for a cross-client status board use the `accounting-firm` skill.

---

## Workflow 2: Uncategorized and suspense cleanup

**When to use:** the books have balances in uncategorized income or expense, suspense, "ask my accountant" or similar holding accounts.

**Operations:** `search_accounts` (find holding accounts), `get_balance_sheet` and `get_profit_and_loss` (balances), `get_general_ledger` for each holding account; `search_purchases`, `search_deposits` for detail; `get_vendor_expenses` for the vendor's usual account.

### Step 1 — Pull the data
> Find the uncategorized, suspense and "ask my accountant" accounts for [company], then pull their general ledger detail for [period or since fiscal year start].

### Step 2 — Itemize and suggest
List every transaction in those accounts: date, payee, amount, memo. For each, look up how the same payee was coded in prior transactions (`calculations.md` → *Suggested account*). Group by payee; most cleanup backlogs are a handful of merchants.

### Step 3 — Visualize
Sorted bar of uncategorized amount by payee, top 10, with the rest as "Other".

### Step 4 — Output
Build from `assets/cleanup_findings_report.md`. Each item gets a **suggested account** labelled *observed* (payee always coded there before) or *possible* (no history, inferred from the payee), or goes to `assets/client_question_list.md` when nobody can tell without the client. Never present a suggestion as recategorized.

---

## Workflow 3: Duplicate transaction check

**When to use:** "did we enter this twice?", after an import or a bank feed reconnect, or before a payment run.

**Operations:** `search_bills`, `search_purchases`, `search_payments` for the period plus a 30-day lookback; `read_invoice` / `get-bill` for detail on candidates.

### Step 1 — Pull the data
> Pull bills, purchases and payments dated from [period start − 30 days] to [period end] for [company].

### Step 2 — Match
Apply `calculations.md` → *Duplicate matching*. Rank candidates as **strong** (same payee, same amount, same or missing reference, within 7 days) or **weak** (same payee and amount within 30 days, different references). Exclude known recurring items of the same amount on a regular schedule.

### Step 3 — Output
Table of candidate pairs with both records' dates, reference numbers, amounts, accounts and memos side by side, plus whether either was paid. For bills, hand payment-impact questions to the `payables` skill. Suggested fix is review-only: "a person confirms with the source document, then voids or deletes one".

---

## Workflow 4: Undeposited funds and unapplied payments/credits

**When to use:** undeposited funds has a balance, customers show credit balances, or vendor credits sit unused.

**Operations:** `get_balance_sheet` (undeposited funds balance), `get_general_ledger` for the undeposited funds account, `search_payments`, `search_deposits`, `search_credit_memos`, `get_customer_balance`, `get_vendor_balance`, `get_aged_receivables`, `get_aged_payables`.

### Step 1 — Pull the data
> Pull the undeposited funds activity, customer payments, deposits and credit memos for [period plus 60-day lookback], and customer and vendor balances as of [period end].

### Step 2 — Analyze
`calculations.md` → *Undeposited and unapplied aging*. Find payments received but not deposited, bucketed by age; customer payments or credit memos not applied to an invoice; customers with an open invoice **and** an unapplied credit of a similar amount (likely a missed application); vendors with unused credits and open bills.

### Step 3 — Visualize
Bucketed bar of undeposited and unapplied amounts by age.

### Step 4 — Output
Itemized list in `assets/cleanup_findings_report.md` with the suggested match (payment → invoice, credit → bill) labelled *observed* or *possible*. Note that a deposit may exist in the bank but not in the books; MosoFin can't check the bank.

---

## Workflow 5: Journal entry review

**When to use:** reviewing manual journal entries before the reviewer does.

**Operations:** `search_journal_entries` for the period; `get_account` and `get_general_ledger` for context.

### Step 1 — Pull the data
> Pull all journal entries dated in [period] for [company].

### Step 2 — Flag
Apply `calculations.md` → *Journal entry flags*: no memo; touches cash, A/R, A/P or a control account; large relative to materiality; round amounts; dated on or after period end; reversing entry without its reversal; touches equity or opening balance equity; posted to an inactive account.

### Step 3 — Output
Build from `assets/je_review_log.md`: one row per entry with accounts, amount, memo, flags and support status (*support seen in the records* / *support needed*). Questions for whoever booked the entry go to the log, not to the client.

---

## Workflow 6: Customer and vendor list hygiene

**When to use:** before year-end, before a 1099 season, after a migration, or when reports show near-duplicate names.

**Operations:** `search_customers`, `search_vendors`, `get_customer`, `get_vendor`, `get_vendor_expenses` (payments by vendor for the year), `get_customer_balance`, `get_vendor_balance`.

### Step 1 — Pull the data
> Pull the full customer and vendor lists, vendor payments for [calendar year], and customer and vendor balances as of [date].

### Step 2 — Check
`calculations.md` → *List hygiene*: near-duplicate names; inactive records with balances or recent activity; missing email, address or terms on active records; vendors paid above the reporting threshold with missing tax ID, address or 1099 flag (only where the catalog returns those fields — if it doesn't, say which fields MosoFin couldn't see).

### Step 3 — Output
Customer and vendor fixes in `assets/cleanup_findings_report.md`; vendor year-end status in `assets/vendor_1099_readiness.md`. Requests for missing W-9s or details are drafted in `assets/client_question_list.md`, unsent. 1099 eligibility is the accountant's decision, not this workflow's.

---

## Workflow 7: Deposits and purchases sanity vs prior months

**When to use:** "deposits look low", "did the bank feed miss a week?", or as a completeness check before close.

**Operations:** `search_deposits`, `search_purchases`, `search_payments` for the period and the prior 3–6 months; `get_profit_and_loss` by month for context.

### Step 1 — Pull the data
> Pull deposits, purchases and payments for [period] and the prior [3–6] months, totalled by week and by month.

### Step 2 — Compare
`calculations.md` → *Activity sanity bands*: period total vs the trailing average, and gaps — weeks with no activity on an account that normally has some, or a payee that stopped appearing.

### Step 3 — Visualize
Bar per month (or week) with the trailing average as a reference line; amber for weeks outside the band.

### Step 4 — Output
A short list of gaps and spikes with dates and accounts. Say plainly that MosoFin can't see the bank feed or statement, so a gap is a question for a person to check against the bank, not proof of missing transactions.
