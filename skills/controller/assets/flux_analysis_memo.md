# Flux analysis memo template

The memo a reviewer reads before signing off a period. It's written for someone who knows the client but hasn't looked at the numbers yet: they want the net effect, the few lines that matter, and what still needs an answer.

---

**To:** [Reviewer / partner / owner]
**Company:** [Company display_name] · **Workspace:** [Workspace name]
**Period:** [current period] vs [comparison period] · **Basis:** [Accrual / Cash]
**Materiality used:** [amount] and [%] ([from review profile / default])

## Headline

[One sentence. E.g. "Net income fell $18.4k (−22%) to $65.1k, driven by $14.2k of new subcontractor cost on the Harbor project and a $6.0k one-off software renewal; revenue was flat."]

## Material changes

| Line | Current | Comparison | Change | % | Driver | Status |
|---|---|---|---|---|---|---|
| [Account] | [$] | [$] | [$] | [%] | [Driver from the records] | Observed / Possible / Unknown |

## Reclasses netted

- [Account A] ↔ [Account B]: [$] moved, net effect [$]. [Journal entry reference if seen.]

## Open questions

| # | Question | For | Due |
|---|---|---|---|
| 1 | [Specific question tied to a line and amount] | [Name / role] | [Date] |

## Not covered

[Anything this review could not see — bank statements, payroll provider detail, a second entity — and what would cover it.]

**Data sources:** [datasource] · [tool] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- The headline is the most-edited sentence. It names the net effect and the one or two drivers, with numbers.
- "Driver" is what the records show, not a guess. When it is a guess, the Status column says *Possible*.
- Keep the table to material lines. Ten rows is a lot; twenty means the threshold is too low.
- Never write "error" or "misstatement" here. Write what was observed and what is missing.
