---
name: moving-money
description: Use when moving the user's own money through Perflo — paying a person, sending to an email, withdrawing to a wallet, or moving funds between their accounts. Covers what executes immediately, what needs switching on, and how to speak about money without jargon.
---

# Moving money with Perflo

These tools move real funds. Read this before calling any of them.

## They execute on call

There is no dry run. `transfer`, `pay_from_spending`, `send_to_email`,
`pay_beneficiary` and `pay_saved_beneficiary` all move money the moment they are
called.

**Do not ask the user to confirm in chat.** The MCP client shows its own approval
prompt, and that prompt *is* the approval. Asking again on top of it trains people to
click through both.

For a multi-step plan, state the plan in one line, then run the steps.

## Paying somebody

`pay_from_spending` pays somebody **else**. It is not a move between the user's own
accounts — `transfer` is the tool for that.

For a recipient the user has saved, `list_beneficiaries` and `pay_saved_beneficiary`
work off the address book. `create_beneficiary` adds a new one; some corridors need
specific details first, which `get_beneficiary_requirements` will tell you.

The beneficiary and payout tools can only send money to somebody already saved. They
**cannot look a person up**. A question about who somebody is, where they work, or
what a company does is a paid lookup — use `spend`.

## Features that are switched off

Trading, Predictions and Earning are switched on per account. A refusal that names one
of them means **nothing moved**. Do not retry it, and never report it as having gone
through.

`get_features` shows what is on. `enable_feature` switches one on. The Perflo app can
do it too.

## Checking what happened

- `get_transactions` and `get_transaction` for paid calls and charges
- `get_transfers` and `get_sends` for money sent out
- `get_balance` and `get_portfolio` for what the user holds

If an outcome is uncertain, **read** rather than retry. Re-running a money call to
find out whether the first one worked is how a payment happens twice.

## Speaking plainly

Say gold, cash, bitcoin, ethereum. Do not show the user tickers, token names, wallet
addresses or chain names unless they ask for them specifically.

## What Perflo does not do

It does not buy or sell holdings, and it does not convert one holding into another.
There is no exchange here. It reads balances and it moves money.
