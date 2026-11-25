# Calculation rules — payables workflows

This plugin ships no executable code. Do these calculations in chat from the pulled data, show the inputs next to each result, and state the basis and dates.

## Materiality

Use the review profile's threshold. Default when none is saved: the greater of **$250** and **1% of period expenses**; say which default you used.

## Aging and DPO

- Buckets by days past due as of the as-of date: current (not yet due), 1–30, 31–60, 61–90, 90+.
- `days_past_due = as_of − due_date`; when a bill has no due date, use the bill date and count it under "no terms".
- Aging total must equal the A/P control account on the balance sheet at the same date; report any difference.
- `DPO = A/P balance / (cost of sales + operating expenses for the trailing 90 days) × 90`. Use the trailing 365 days for a smoother figure and say which.

## Payment prioritization

Rank open bills (net of open credits for the same vendor) in this order until the cash limit is reached:
1. Critical vendors from the profile (payroll-related, taxes, rent, key suppliers) that are due or overdue.
2. Bills 61+ days past due.
3. Bills due within the run window (default: next 7 days).
4. Bills with an early-payment discount in their terms, when the terms are visible.
5. Everything else by due date, oldest first.

Hold out: candidate duplicates, bills with a disputed or unknown status the user mentions, bills from vendors with open credits larger than the bill. `cash_limit = user's stated amount` or `book cash − minimum buffer` (label the second as books, not bank).

## Duplicate matching

Normalize vendor name and amount (to the cent).
- **Strong:** same vendor, same amount, and the same bill number (or one missing), or bill dates within 7 days.
- **Weak:** same vendor and amount within 60 days, different bill numbers.
- **Exclude** a vendor's regular cycle (same amount, ≥ 3 prior occurrences ~monthly or ~weekly) unless two land in the same cycle.
- **Over-payment:** `Σ payments applied to a bill > bill amount`, or two payments to the same vendor and amount within 7 days.

## Spend change

- `change = current − comparison`; `change_pct = change / |comparison|` (blank when comparison is 0: report as "new").
- Material when `|change| ≥ materiality` and `|change_pct| ≥ 20%`.
- `share = vendor (or category) spend / total spend`.
- Trailing average = mean of the prior 3 complete months.

## Concentration

- `top_n_share = Σ spend of top n vendors / total spend`.
- `HHI = Σ (share_i × 100)²` over all vendors. Below 1,500 low; 1,500–2,500 moderate; above 2,500 high.
- Single-vendor flag: any vendor ≥ 20% of total, or ≥ 30% of cost-of-sales spend.

## Recurrence detection

A vendor is **recurring** when, over the last 12 months:
- Monthly: charges in ≥ 4 of the last 6 months, spaced 25–35 days.
- Quarterly: ≥ 3 charges spaced 80–100 days.
- Annual: a charge 350–380 days after a prior charge of similar amount.
- Amount stable: each charge within ±15% of the median.
- `annualized = median × 12` (monthly), `× 4` (quarterly), `× 1` (annual).
- **Price change:** latest charge differs from the median by > 10%.
- **Stopped:** no charge in the expected window after the last one.

## Due-date windows

- Overdue: `due_date < today`.
- Next 14 days: `today ≤ due_date ≤ today + 14`.
- Days 15–28: `today + 15 ≤ due_date ≤ today + 28`.
- Expected recurring (optional): next expected date from *Recurrence detection* inside the window and no bill entered yet; amount = median. Always labelled "expected — not in the books".
