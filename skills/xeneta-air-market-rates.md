---
name: Pull air freight market rates
description: >-
  Fetch Xeneta air freight market rates for an airport-pair or region
  corridor and date range.
api: openapi/xeneta-api-30-openapi.yml
operations: [get-air-market-rates]
generated: '2026-07-21'
method: generated
---

# Pull air freight market rates

## Auth
Xeneta API key in the `X-Auth` header over HTTPS
(`../authentication/xeneta-authentication.yml`).

## Steps
1. Resolve the corridor using the air geo-hierarchy
   (https://www.xeneta.com/hubfs/static/air-geo-hierarchy.xlsx) — `origin`
   and `destination` accept UN/LOCODEs or air geo-hierarchy regions.
2. Call `get-air-market-rates` (`GET /air/market-rates`) with `origin`,
   `destination`, `date_from`, `date_to` (`YYYY-MM-DD`, max 31-day range).
   The response returns `meta` plus `market_rates_by_day` series.
3. For recurring monitoring, page forward one date window at a time — there
   is no pagination; the date range is the window
   (`../conventions/xeneta-conventions.yml`).

## Rules
- GET-only, retry-safe; back off on `429` (API credits).
- `400` signals missing/incorrect parameters; check the required five query
  params first (`../errors/xeneta-problem-types.yml`).
