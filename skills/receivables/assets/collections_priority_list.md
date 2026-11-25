# Collections priority list template

A ranked contact list for the person doing collections this week. Short, ordered, and every row says why it's there and what to do next.

---

**Company:** [display_name] · **As of:** [YYYY-MM-DD] · **Minimum balance:** [$] · **Customers past due:** [N]

**This week:** [One sentence, e.g. "Contact 8 customers holding $96k of net past-due A/R; start with Harbor Logistics ($31k, 74 days, no payment since May)."]

| Rank | Customer | Net past due | Oldest invoice (days) | Last payment | Reason | Next step | Owner |
|---|---|---|---|---|---|---|---|
| 1 | [Customer] | [$] | [#] | [YYYY-MM-DD] | [e.g. 61–90 bucket, no payment in 45+ days] | [Reminder / call / escalate to owner] | [Name] |

## Handle separately (not on the chase list)

| Customer | Balance | Why | Owner |
|---|---|---|---|
| [Customer] | [$] | [Disputed / payment plan / net balance $0 / on hold per known context] | [Name] |

## Data gaps

- [Customers missing an email or contact name — for the bookkeeper to fix in the books.]

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- Ten rows is a good week. If the list is longer, show the top ten and say how many more.
- "Reason" comes from the score inputs; no adjectives about the customer.
- Nobody on this list has a net supported balance of zero or less.
