# Customer reminder drafts template

Review-only payment reminders, one per customer. A person checks each balance against the latest remittance, edits the tone, and sends it themselves. Pick the stage by the oldest past-due invoice.

Every draft starts with the label **Draft — not sent. Check the balance before sending.**

---

## Stage 1 — Friendly (1–30 days past due)

**Subject:** Invoice [number] — [amount] due [due date]

Hi [contact first name],

A quick note that invoice [number] for [amount], due [due date], is showing as open on our side. If it's already on its way, thank you, and please ignore this.

If you need a copy of the invoice or anything else to process it, just reply here.

Thanks,
[Sender name]

## Stage 2 — Firm (31–60 days past due)

**Subject:** Past due: invoices [numbers] — [total]

Hi [contact first name],

Our records show [count] invoices past due, totalling [total]:

| Invoice | Date | Due | Amount |
|---|---|---|---|
| [number] | [date] | [due] | [amount] |

Could you let me know when we can expect payment, or if anything is holding these up? Happy to resend any of them.

Thanks,
[Sender name]

## Stage 3 — Escalation (61+ days past due)

**Subject:** Action needed: [total] past due since [oldest due date]

Hi [contact first name],

We haven't received payment for [count] invoices totalling [total], the oldest due on [date]. I'd like to resolve this with you directly — could we set a time this week to agree a payment date?

[Optional, only if the user provides it: reference to agreed terms or next steps.]

Thanks,
[Sender name]

---

## Rules

- Only invoice numbers, dates and amounts from the retrieved records. No interest, fees or consequences unless the user supplies them.
- No draft for a customer whose net supported balance is zero or less, or who is disputed or on a payment plan in known context.
- If unapplied payments or credits exist for the customer, don't draft; put them on the "Handle separately" list.
- Save as drafts through an email connector only when one is available and the user asks. Never send.
