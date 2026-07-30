---
name: Check the market and place a trade on Luno
description: Inspect a Luno market (ticker + order book), place a limit or market order, then confirm or cancel it.
api: https://www.luno.com/developers/api
auth: HTTP Basic — API Key ID as username, API Key Secret as password
operations: [get_ticker, get_order_book, create_order, list_orders, cancel_order]
rest_endpoints:
  - GET /api/1/ticker
  - GET /api/1/orderbook_top
  - POST /api/1/postorder
  - POST /api/1/marketorder
  - GET /api/1/listorders
  - POST /api/1/stoporder
---

# Check the market and place a trade on Luno

Use this skill to evaluate a Luno trading pair and act on it. All private
operations need a Luno API key pair with the `Perm_W_Orders` permission.

## Steps

1. **Read the market.** Call `get_ticker` (REST `GET /api/1/ticker?pair=XBTZAR`)
   for the current bid/ask/last price, and `get_order_book`
   (REST `GET /api/1/orderbook_top?pair=XBTZAR`) for depth. These are public and
   need no auth.
2. **Place the order.** For a limit order call `create_order`
   (REST `POST /api/1/postorder`) with `pair`, `type` (`BID`/`ASK`), `volume`,
   and `price`. For an immediate fill use `POST /api/1/marketorder` with
   `base_volume` or `counter_volume`.
3. **Confirm.** Call `list_orders` (REST `GET /api/1/listorders?pair=XBTZAR`)
   and check the returned `order_id` shows `state: PENDING` or `COMPLETE`.
4. **Cancel if needed.** Call `cancel_order` (REST `POST /api/1/stoporder`) with
   the `order_id` to withdraw an unfilled order.

## Rules

- Luno order placement is **not idempotent** — never blindly retry a
  `postorder` call after a timeout; re-check `listorders` first.
- Watch `x-ratelimit-remaining`; back off on HTTP 429.
- Errors come back as `{"error": "...", "error_code": "Err..."}` — branch on
  `error_code`, not the human string.
