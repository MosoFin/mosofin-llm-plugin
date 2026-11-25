# Calculation rules — fractional CFO workflows

This plugin ships no executable code. Do these calculations in chat from the pulled reports, show the inputs next to each result, and state the basis and dates.

## Burn and runway

- `net_cash_change_m = cash_end_m − cash_end_(m−1)` (cash accounts only; exclude restricted and clearing accounts).
- `financing_m` = loan draws − repayments + equity in − owner draws/distributions, from the cash flow financing section.
- `operating_burn_m = −(net_cash_change_m − financing_m)` (positive = burning).
- `avg_burn_3m` = mean of the last 3 months' operating burn; also show 6-month.
- `runway_months = (cash_now − minimum_cash_balance) / avg_burn_3m`. If `avg_burn_3m ≤ 0`, the business is cash-generative: report "not burning" instead of a runway.
- Remove one-offs listed in known context from the average and show both.

## Cash bridge

`Δcash = net income + non-cash items (depreciation, amortization) − ΔA/R − Δinventory − Δprepaid + ΔA/P + Δaccrued liabilities − capital purchases + net financing − owner draws`

Use balance sheet changes between the period's opening and closing dates. `residual = actual Δcash − bridged Δcash`; any residual above materiality is a finding (often other balance sheet accounts).

## KPI definitions

| KPI | Formula | Note |
|---|---|---|
| Gross margin % | (revenue − cost of sales) / revenue | Accrual |
| Operating expense ratio | operating expenses / revenue | Exclude cost of sales |
| Net margin % | net income / revenue | |
| EBITDA (approx.) | net income + interest + taxes + depreciation + amortization | Only lines visible in the P&L; say "approx." |
| DSO | ending A/R / revenue × days | Accrual revenue |
| DPO | ending A/P / (cost of sales + operating expenses paid on terms) × days | State the denominator used |
| Current ratio | current assets / current liabilities | |
| Quick ratio | (cash + A/R) / current liabilities | |
| Revenue growth | (revenue − comparison) / comparison | Same period length |
| Runway | see above | |

**Status:** on target; within 10% of target (amber); off target (red). Direction depends on the KPI (higher DSO is worse).

## Concentration

- `share_i = customer revenue / total revenue` over 12 months.
- Flag top-1 ≥ 20% or top-5 ≥ 50%.
- `HHI = Σ (share_i × 100)²`: < 1,500 low, 1,500–2,500 moderate, > 2,500 high.
- `share_of_growth_i = Δrevenue_i / Δtotal revenue` (only when total grew).

## 13-week scenario

Weekly, for weeks 1–13:

- `opening_cash_1` = cash at the start date (fact).
- `receipts_w` = open invoices expected in week w under the user's collection assumption + new sales assumption × collection assumption.
- `disbursements_w` = open bills by due date (or the user's payment timing) + payroll + recurring costs + debt service + taxes + owner draws + one-offs, each from a stated assumption.
- `closing_cash_w = opening_cash_w + receipts_w − disbursements_w`; `opening_cash_(w+1) = closing_cash_w`.
- Flag any week with `closing_cash_w < minimum_cash_balance`.

Every line carries its assumption source: **fact** (from the books), **user**, or **historical, confirmed by user**. Lines without a confirmed assumption are excluded and listed as "not included".
