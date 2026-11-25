---
name: quickbooks
description: QuickBooks Online know-how for MosoFin — which catalog operation answers which question, required parameters, date rules, pagination, and QuickBooks quirks (cash vs accrual, undeposited funds, uncategorized accounts, classes and locations, budgets). Use for any QuickBooks question through MosoFin such as "QuickBooks P&L", "aged receivables in QBO", "search bills in QuickBooks", "QuickBooks budget".
---

# QuickBooks Online through MosoFin

Platform know-how for the `quickbooks` datasource. For end-to-end workflows use the role skills (`bookkeeper`, `controller`, `receivables`, `payables`, `fractional-cfo`, `accounting-firm`). If MosoFin tools aren't available in this session, follow the `connect` skill first.

Always confirm an operation is listed by `get_datasource_tools(datasource="quickbooks", …)` for the chosen company before invoking it. The list below is a snapshot; the live catalog is the source of truth, and an operation with policy `disabled` must not be called.

## Which operation answers which question

| Question | Operation | Params |
|---|---|---|
| Revenue, expenses, net income for a period | `get_profit_and_loss` | `start_date`, `end_date`; optional `accounting_method` (`Accrual` / `Cash`) |
| Balances at a date (cash, A/R, A/P, equity) | `get_balance_sheet` | optional `report_date` (default today) |
| Operating, investing, financing cash movement | `get_cash_flow` | `start_date`, `end_date` |
| Does the ledger tie | `get_trial_balance` | `start_date`, `end_date` |
| What hit an account | `get_general_ledger` | `start_date`, `end_date` (+ account filter where supported) |
| Who owes us, by age | `get_aged_receivables` | optional `report_date` |
| Whom we owe, by age | `get_aged_payables` | optional `report_date` |
| Sales by customer | `get_customer_sales` | `start_date`, `end_date` |
| Spend by vendor | `get_vendor_expenses` | `start_date`, `end_date` |
| Open balance per customer / vendor | `get_customer_balance`, `get_vendor_balance` | optional `report_date` |
| Find invoices, bills, payments, purchases, deposits, estimates, credit memos, journal entries | `search_invoices`, `search_bills`, `search_payments`, `search_purchases`, `search_deposits`, `search_estimates`, `search_credit_memos`, `search_journal_entries` | `start_date`, `end_date`, optional `offset`, `max_results` |
| Customer, vendor, account, item lists | `search_customers`, `search_vendors`, `search_accounts`, `search_items` | filters as listed in the catalog |
| One record | `get_customer`, `get_vendor`, `get_account`, `read_invoice`, `read_item` | `id` |
| Company details (fiscal year, name) | `get_company_info` | none |
| Budgets | `search_budgets`, then `get_budget_details` | `budget_id` or `budget_name` |

Independent reads may run in parallel in one turn.

## Rules that save a retry

- **Dates:** resolve "last month", "Q3", "YTD" to `YYYY-MM-DD` before invoking. Period reports and searches need both `start_date` and `end_date`; a missing one returns `invalid_params`.
- **Pagination:** search operations return `pagination`. While `has_more` is true, re-invoke with `params.offset = pagination.next_offset`. Don't stop at the first page when totalling.
- **Multi-company workspaces:** pass `data_source_id` on every call, including retries. On `entity_required`, ask which company by `display_name`.
- **Permission policy:** an operation with policy `permission` returns `approval_required` on first invoke; ask the user, then re-invoke with `approved=true`.
- **Fixture data:** check `mock` on every result and say whether numbers are live.

## QuickBooks quirks

- **Basis:** QuickBooks reports default to the company's preference. Pass `accounting_method` explicitly when the comparison depends on it, and state it.
- **Fiscal year:** read `get_company_info` for the fiscal year start before computing YTD.
- **Undeposited Funds:** payments received but not yet grouped into a bank deposit. A balance made of old items usually means deposits were recorded directly to the bank instead of from Undeposited Funds, producing duplicated income or a stale balance.
- **Uncategorized Income / Uncategorized Expense / Ask My Accountant:** bank-feed transactions categorized without a real account. They should be zero at close.
- **Opening Balance Equity:** created when opening balances are entered. It should be closed out to retained earnings after setup.
- **Unapplied payments and credits:** a customer payment not applied to an invoice leaves both the invoice and a credit open; the aging overstates what is owed. Check before any collections draft.
- **Classes and locations:** only present if the company turned them on. If a breakdown returns nothing, say tracking isn't enabled rather than reporting zeros.
- **Budgets:** budgets are optional and often stale. Name the budget used and its fiscal year.
- **Bank feeds and reconciliations:** MosoFin can't see bank statements or the reconciliation status. Never call an account reconciled.

## Other platforms

The catalog may list other platforms (for example Xero, Stripe, Shopify). Until a platform is wired live, its results come back with `mock: true`; tell the user those numbers are demo data. Use `get_datasource_tools` for that platform's operation names rather than assuming the QuickBooks names apply.

Finish with the answer first, then the evidence and the **Data sources** line. For a workflow the user will repeat, offer `/mosofin:save-skill`.
