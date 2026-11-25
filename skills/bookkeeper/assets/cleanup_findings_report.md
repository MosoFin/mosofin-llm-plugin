# Cleanup findings report template

The working list of what needs fixing in the books, with a suggested fix for each item. A person makes every fix in the accounting platform; this report only proposes.

---

**Company:** [display_name] · **Period covered:** [dates] · **Basis:** [Accrual / Cash] · **Prepared:** [date]

## Summary

| Issue type | Items | Amount |
|---|---|---|
| Uncategorized / suspense | [N] | [$] |
| Candidate duplicates | [N] | [$] |
| Undeposited funds > 7 days | [N] | [$] |
| Unapplied payments and credits | [N] | [$] |
| List hygiene | [N] | — |

**Headline:** [One sentence. E.g. "Three merchants account for 70% of the $4,180 uncategorized; all three have a clear coding history."]

## Items

| # | Type | Date | Payee / record | Amount | Current account | Suggested fix | Confidence | Owner |
|---|---|---|---|---|---|---|---|---|
| 1 | [Type] | [YYYY-MM-DD] | [Name] | [$] | [Account] | [e.g. Recode to Software subscriptions] | Observed / Possible / Unknown | [Name] |

## Needs the client

[Items marked Unknown, moved to the client question list.]

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- "Suggested fix" is a proposal. Write "Recode to…", "Apply to invoice…", "Confirm and void one of…" — never "Recoded" or "Fixed".
- Confidence reflects the evidence: *Observed* means the payee's history shows it; *Possible* means it's inferred.
- Group by payee when one merchant has many items; nobody wants 40 rows for the same coffee shop.
