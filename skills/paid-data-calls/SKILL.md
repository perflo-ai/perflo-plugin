---
name: paid-data-calls
description: Use when buying information through Perflo — research on a person or company, web and social data, images, gift cards. Covers how to call `spend`, how to collect a slow result without paying twice, and how to check whether a charge landed.
---

# Paying for data with Perflo

`spend` is the one tool that buys things. It routes a plain-English task to one of
550+ providers, pays for the call, and returns the result. Every other tool here is
free.

## Before you promise anything

Call `get_spending_status`. It gives the balance and the limits the user set. A task
you cannot afford should be said out loud before it is attempted, not after it is
refused.

## Calling `spend`

Pass the user's request **verbatim** in `task`. The provider is chosen from that exact
text, so summarising or rewording it changes which provider you get.

Pass the provider's own fields in `input` when the user gave you the values:

```json
{
  "task": "find me the similarweb traffic of stripe.com",
  "input": { "websites": ["stripe.com"] }
}
```

Two rules that matter:

- **A field NAME is safe to supply.** A wrong one is rejected free, before any
  payment, and the refusal names the field it wanted. Add it and call `spend` again.
- **A field VALUE is not.** A wrong-but-plausible value passes validation and gets
  charged. Never invent one the user did not give you. `list_services` with a slug
  shows every field a provider takes.

## When a call is slow

A provider that takes a while returns a receipt instead of the data:

> Paid and still running. Collect it with get_task_result using id `run_…`.
> Do NOT call spend again — this is already paid for.

Do exactly that. Call `get_task_result` with that id. It is free, idempotent, and safe
to call repeatedly until the result lands.

**Never call `spend` again to see the result.** The first call already paid. A second
call pays a second time for the same thing.

## When a result is too large to show

A very large result is cut at a **record boundary**, so what you get is complete
entries rather than a string that stops mid-way. The message says how many of how
many, and names a `get_task_result` id that still holds all of it.

If the user needs the rest, collect it with that id. Do not re-run the call. Many
providers also take a `limit` or `count` field if a smaller result would do.

## When you are not sure whether a charge landed

Call `get_transactions`. It is the only thing that settles the question.

**Never re-run a paid call to find out whether the first one worked.** That is how
people get charged twice.

If a refusal names a `transactionId`, `get_transaction` looks up that exact row.

## Stopping

`stop_spending` is the kill switch. It revokes the spending permission immediately, so
no further paid call can go through. Use it the moment the user asks to stop, pause or
cancel — and offer it if they sound unsure. It is reversible from the Perflo app.

## Things worth telling the user

- The amount charged can be **less** than the catalog price. Several providers are
  metered, and the catalog figure is a ceiling rather than a quote.
- A failed call does not charge. A call that was charged and returned nothing usable
  says so explicitly.
- Delivered goods (gift cards, purchased items) are read back with `get_resources`,
  not by buying them again.
