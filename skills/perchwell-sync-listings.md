---
name: Sync real estate listings from Perchwell
description: Incrementally pull sale and rental listings from Perchwell's JSON API for an account feed, paginating and filtering by modification time.
api: openapi/perchwell-json-api-openapi.yml
operations: [listListings]
---

# Sync real estate listings from Perchwell (JSON API)

Use this to pull listings into your own store and keep them fresh.

## Auth
Send your Perchwell-issued token in the `Authorization` header (raw token, no
`Bearer` prefix for the JSON API). Credentials come from support@perchwell.com.

## Steps
1. **Full pull.** Call `listListings` at
   `GET https://www.perchwell.com/api/feeds/{account_name}`. Optionally pass
   `listing_type` (`sales`, `rentals`) and `listing_status`
   (`active`, `in_contract`, `closed`, ...).
2. **Paginate.** Read `total` from the response. Loop `page` from 1 upward with
   `page_size` up to 200 until you have collected `total` records.
3. **Incremental sync.** Persist the run time. On the next run pass
   `updated_since=<last ISO 8601 run>` (optionally bound with `updated_before`)
   so you only fetch changed listings.
4. **Store.** Each `listings[]` item carries `id` plus nested `location`,
   `agents`, `media`, `open_houses`, and detail objects — persist by `id`.

## Rules
- `page_size` max is 200; `updated_since`/`updated_before` take ISO 8601 dates or datetimes.
- This is a read-only feed; there is no idempotency key (see conventions/perchwell-conventions.yml).
