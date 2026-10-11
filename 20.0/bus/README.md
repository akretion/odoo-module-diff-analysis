# bus migration guide (19.0 -> 20.0)

## What's new for users

The 19.0 → 20.0 release notes contain **nothing specific to the `bus` addon**. `bus` is the technical plumbing behind Odoo's real-time features: chatter messages, notifications, activity updates and live views. The 20.0 changes are server-side performance work, not new screens, menus or settings.

You will see no new button, field or option in the interface. Real-time behaviour is unchanged from a user's point of view — the work went into *how* the server delivers updates, not *what* it delivers.

## Technical data model changes

From the 19.0 → 20.0 commits:

- No added or removed models, no new or removed fields: `bus.bus` keeps `message`, `channel` and `create_date`.
- **One removed constant:** `TIMEOUT = 50` (long-polling timeout) disappears from `bus/models/bus.py`, together with the inline `fetch_bus_notifications()` helper that used it, including `threshold = fields.Datetime.now() - datetime.timedelta(seconds=TIMEOUT)`.
- **Helpers relocated:** fetching now lives in the new `addons/bus/tools/notifications.py` (`fetch_bus_notifications`, `json_dump`). The `from ..tools import orjson` import is gone; the model imports `fetch_bus_notifications, json_dump` from `..tools.notifications`.
- **`BusBus._poll(channels, last=0, ignore_ids=None)`**: same signature, refined behaviour. It calls `fetch_bus_notifications(cr, {last: list(channels)}, ignore_ids)` — notifications grouped by "last seen id" instead of one entry per channel — and returns the flattened result **sorted by notification `id`**.
- **New dispatcher architecture:** `ImDispatch` becomes `BusDispatcher`, backed by a pool of worker threads.
  - Worker count = available cursors − reserve; 30% of cursors are reserved for regular requests by default, tunable via the `ODOO_BUS_RESERVE_CURSORS_RATIO` environment variable.
  - The listener groups incoming PostgreSQL `NOTIFY` payloads by database; a worker claims every pending topic for that database in one shot, fetches all notifications in a single SQL query, encodes each channel's JSON payload **once**, then fans out to every subscriber.
  - A topic already claimed by a batch is marked dirty and requeued rather than claimed twice.
  - Cursor usage per `NOTIFY` now scales with the number of *databases* dispatching, not the number of subscribed sockets.

## How your habits should change

- **Functional users:** nothing. Same chatter, same notifications, same real-time updates; no retraining needed.
- **Administrators:** no migration action required. You may optionally set `ODOO_BUS_RESERVE_CURSORS_RATIO` on very large deployments — it controls the share of database cursors kept for normal requests. Note the dispatcher pool is sized from available cursors, so a low `db_maxconn` limits dispatch concurrency.
- **Developers:** update imports — `fetch_bus_notifications` and `json_dump` moved out of `bus/models/bus.py`; `fetch_bus_notifications` now expects a `{last_id: [channels]}` mapping; `orjson` is no longer re-exported from `bus.tools`.

## What you gain by migrating

- **Far less database load on busy instances.** Previously one `NOTIFY` was handled independently by every subscribed websocket: N sockets → N cursors → N identical queries → N identical JSON encodings. Now one batch per database queries and encodes once.
- **Better scalability.** Cursor consumption grows with the number of dispatching databases rather than with connected users, so real-time updates stay affordable as your user base grows.
- **Faster, more consistent delivery.** Concurrent workers and single-pass encoding cut latency, while the "dirty topic" requeue ensures a channel is not processed twice while a batch is in flight.
- **Predictable resource control.** The tunable cursor reserve protects interactive requests from real-time traffic spikes.
