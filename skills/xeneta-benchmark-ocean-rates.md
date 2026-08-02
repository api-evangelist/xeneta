---
name: Benchmark ocean freight rates against the market
description: >-
  Pull Xeneta ocean market rates for a corridor and compare them with your own
  contracted prices to see where you sit against the market.
api: openapi/xeneta-api-30-openapi.yml
operations: [get-ocean-market-rates, get-my-prices]
generated: '2026-07-21'
method: generated
---

# Benchmark ocean freight rates against the market

## Auth
Every request needs a Xeneta API key in the `X-Auth` header, over HTTPS only
(see `../authentication/xeneta-authentication.yml`). Keys are generated at
https://app.xeneta.com/my-account/security.

## Steps
1. Resolve the corridor. `origin` and `destination` accept UN/LOCODEs
   (`CNSGH`, `NLRTM`) or Xeneta geo-hierarchy regions (`china_east_main`,
   `north_europe_main`). Download the ocean geo-hierarchy from
   https://www.xeneta.com/hubfs/static/ocean-geo-hierarchy.xlsx if you need
   region codes.
2. Call `get-ocean-market-rates` (`GET /ocean/market-rates`) with `origin`,
   `destination`, `date_from`, `date_to` (max 31-day range, `YYYY-MM-DD`),
   and `contract_length` (`long` or `short`). The response returns
   `market_rates_by_day` percentile series plus a `meta` echo of the resolved
   query.
3. Call `get-my-prices` (`GET /ocean/my-prices`) with the same corridor and
   date range to fetch your contracted prices for the corridor.
4. Compare your price series with the market high/median/low by day to
   compute your spread to market.

## Rules
- All operations are GET reads — safe to retry (no idempotency keys exist or
  are needed; see `../conventions/xeneta-conventions.yml`).
- Watch for `403` (dataset not in your subscription) and `429` (API-credit
  metering) — back off on 429. Full code list:
  `../errors/xeneta-problem-types.yml`.
- Date ranges over 31 days are rejected with `400`.
