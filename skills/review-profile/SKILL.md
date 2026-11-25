---
name: review-profile
description: Save and reuse the company's review settings — accounting basis, fiscal year, materiality, close calendar, KPI targets, cash floor, chart-of-accounts conventions, and "known context" like a one-off legal fee or an account being cleaned up. Use when the user states a threshold or target, says "remember that…", explains why a number looks odd, asks to set up their profile or targets, runs /mosofin:set-profile, or before any analysis that judges results against goals or materiality.
---

# Review profile

Every analysis in this plugin judges results against the company's own settings rather than generic rules of thumb. The review profile holds them.

MosoFin has no separate settings store, so the profile is kept as a **private MosoFin skill** named `review-profile-<company-slug>` in the confirmed workspace, saved with `create_skill` only after the user agrees. It's private to the user, scoped to that workspace, and readable from any AI host connected to MosoFin.

## Read it before analysis

At the start of any review that uses thresholds or targets:

1. If the profile is already in this conversation, use it.
2. Otherwise call `get_skills` for the confirmed workspace and look for `review-profile-<company-slug>` (or `review-profile` for a workspace-wide one). If found, say "I found your saved review profile for [company] — use it?" and, on yes, call `get_my_skill(skill_id, confirmed="yes", workspace_id=…)`. `get_my_skill` requires that yes; never set `confirmed` without it.
3. Apply what you find:
   - Use the saved basis, fiscal year and currency.
   - Use the saved materiality instead of the default thresholds in each skill's `references/calculations.md`.
   - Judge KPIs against saved targets.
   - Don't report items listed under known context as findings (for example "Legal fees high in Q3 — one-off settlement, expected").

If nothing is saved and the analysis depends on a setting, ask **one** question (for example "What materiality should I use — 5% or a fixed amount?"), use the answer, and offer to save it at the end.

## Set up or update the profile

When the user runs `/mosofin:set-profile` or asks for setup, ask briefly and only about what's missing:

1. **Company and role:** which company file; whether they're the bookkeeper, controller, CFO/owner, or an accounting firm managing the client.
2. **Accounting:** basis (accrual or cash), fiscal year start month, reporting currency, close deadline (e.g. business day 5).
3. **Materiality:** a percentage and an amount, for P&L and for the balance sheet.
4. **Targets:** any of gross margin %, net margin %, opex as % of revenue, DSO, DPO, minimum cash or months of runway, budget name in the books.
5. **Known context** Claude should not flag: one-offs, accounts being cleaned up, intentional timing (annual prepayments), seasonality. Date each item so stale ones can be pruned.
6. **Conventions:** class or location tracking, accounts to ignore, how they want output (table vs memo, level of detail).

Use the host's question tool for multiple-choice items where available.

## Save it

Saving is an additive write. Show the proposed profile first and get an explicit yes, then call `create_skill`:

- `name`: `review-profile-<company-slug>` (kebab-case, e.g. `review-profile-acme-beauty`)
- `description`: "Use before any MosoFin review of [company display_name] to apply its basis, fiscal year, materiality, targets and known context."
- `destination`: ask `mosofin`, `claude` or `both`; don't default
- `confirmed`: `"yes"` only after the user's yes to this exact text
- `workspace_id`: the confirmed handle
- `files`: a JSON **string** with one `SKILL.md`, frontmatter `name` and `description`, and these sections:

```markdown
# Review profile — [company display_name]

## Accounting
- Basis: Accrual
- Fiscal year starts: January
- Currency: USD
- Close deadline: business day 5

## Materiality
- P&L: 10% and $2,500
- Balance sheet: $5,000

## Targets
- Gross margin: 45%
- DSO: ≤ 40 days
- Minimum cash: $150,000

## Known context
- 2026-09: Legal fees elevated (one-off settlement). Expected.
- 2026-10: Old suspense account being cleaned up by the bookkeeper.

## Conventions
- Class tracking: by location
- Output: memo first, then table
```

No `datasources` and no `run-recipe.json` are needed: the profile reads no data.

`create_skill` creates a new version each time; the latest version is the profile. Confirm what you saved in one line.

When the user explains an anomaly mid-analysis ("that spike was the annual insurance prepayment"), offer to add it to known context and save a new version.

## Never

- Save without a fresh, explicit yes after the user has seen the text.
- Put customer names, bank details or any sign-in material in the profile.
- Treat the profile as permission to change anything in the books. It only changes how results are judged.
