# Unusual expense log template

A neutral log for expenses that stood out. It separates what the records show from what might explain it, so no one reads an allegation into a review note.

---

**Company:** [display_name] · **Period:** [dates] · **Comparison:** [dates] · **Threshold:** [amount / %]

| # | Date | Vendor | Account | Amount | Test triggered | Observed fact | Possible explanation | Missing evidence | Follow-up owner |
|---|---|---|---|---|---|---|---|---|---|
| 1 | [YYYY-MM-DD] | [Vendor] | [Account] | [$] | [e.g. ≥3× vendor median] | [What the records show] | [Plausible reason, labelled as possible] | [Receipt, contract, approval…] | [Name] |

**Cleared on review:** [Items checked and found explained, one line each, so they aren't re-raised next month.]

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- "Observed fact" is only what the retrieved records show.
- Never use "fraud", "error", "misclassified" or "suspicious" in this log. "Unusual against [test]" is enough.
- A short log with clear owners beats a long one nobody finishes.
