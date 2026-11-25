# Vendor year-end readiness template

A data-completeness view of the vendor list for year-end information returns (1099s in the US). It shows which vendors may need forms and which are missing details. Eligibility and filing decisions belong to the responsible accountant; this is not tax advice.

---

**Company:** [display_name] · **Calendar year:** [YYYY] · **Threshold used:** [$ amount, confirmed by accountant] · **Prepared:** [date]

## Summary

[One sentence. E.g. "14 vendors were paid above the threshold; 9 are complete, 5 are missing a tax ID or address."]

## Vendors above threshold

| Vendor | Paid in year | Tax ID on file | Legal name | Address | 1099 flag | Status | Next step |
|---|---|---|---|---|---|---|---|
| [Name] | [$] | Yes / No / Not visible | Yes / No | Yes / No | Yes / No / Not visible | Complete / Missing details | [e.g. Request W-9] |

## Fields MosoFin couldn't see

[List any of tax ID, 1099 flag or payment method that the catalog didn't return, and what that means — e.g. "card payments could not be excluded from totals".]

## Requests for the client

[Moved to the client question list as unsent drafts.]

**Data sources:** [datasource] · [tools] · fetched [fetched_at] (live / fixture)

---

## Tone notes

- Never show a full tax ID. "On file" is enough.
- "Not visible" is different from "No". Say which.
- Leave the eligibility call to the accountant; this list only says who is above the threshold and what data is missing.
