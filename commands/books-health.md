---
description: Can you trust these books? Trial balance tie, uncategorized and suspense, undeposited funds, opening balance equity, aging vs control accounts and stale balances
argument-hint: "[company or as-of date, optional]"
---

Check books health for: $ARGUMENTS (default: the confirmed company as of the last month end). Run this before trusting any analysis, because a books problem invalidates every other finding.

1. Trial balance: debits equal credits; net income agrees to the P&L (controller skill, "Trial balance tie-out").
2. Uncategorized income and expense, suspense and "ask my accountant" balances, with the amount and the age of the oldest item.
3. Undeposited funds made of items older than 7 days.
4. Opening balance equity not zero after the first year.
5. A/R and A/P aging totals vs the balance sheet control accounts.
6. Unexpected signs: negative cash or A/R, debit-balance A/P or credit cards.
7. Possible duplicates: same vendor, same amount, within 14 days.

Output: a verdict (**Trustworthy**, **Use with caution** or **Needs cleanup**), a table of the issues with amounts, and the fixes in priority order for the bookkeeper. Read-only. MosoFin can't see bank statements, so never call an account reconciled.
