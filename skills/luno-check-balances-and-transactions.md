---
name: Review Luno balances and account transactions
description: Read wallet balances and paginate account transactions for reconciliation on Luno.
api: https://www.luno.com/developers/api
auth: HTTP Basic — API Key ID as username, API Key Secret as password
operations: [get_balances, list_transactions, get_transaction]
rest_endpoints:
  - GET /api/1/balance
  - GET /api/1/accounts/{id}/transactions
  - GET /api/1/accounts/{id}/pending
---

# Review Luno balances and account transactions

Use this skill to reconcile a Luno account. Needs an API key with at least the
`Perm_R_Balance` and `Perm_R_Transactions` permissions.

## Steps

1. **List balances.** Call `get_balances` (REST `GET /api/1/balance`) to get each
   wallet's `account_id`, `asset`, `balance`, `reserved`, and `unconfirmed`.
2. **Pull transactions.** For a chosen `account_id` call `list_transactions`
   (REST `GET /api/1/accounts/{id}/transactions`) with `min_row`/`max_row` to
   page through the ledger.
3. **Check pending.** Call `GET /api/1/accounts/{id}/pending` for entries not yet
   settled.
4. **Inspect one entry.** Use `get_transaction` to resolve a single row's detail.

## Rules

- Balances are read-only; this skill performs no writes.
- Respect `x-ratelimit-remaining`; the ledger can be large — page rather than
  request huge ranges.
- Correlate support issues with the `x-luno-trace-id` response header.
