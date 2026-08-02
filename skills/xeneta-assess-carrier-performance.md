---
name: Assess carrier performance on a corridor
description: >-
  Use Xeneta schedule-reliability, carbon-emissions, and carrier-spread data
  to pick the best ocean carrier for a corridor on punctuality, emissions,
  and price.
api: openapi/xeneta-api-30-openapi.yml
operations: [list-all-carriers-reliability, get-schedule-reliability-data, list-all-carriers-emissions, get-carbon-emissions-data, get-carrier-rates]
generated: '2026-07-21'
method: generated
---

# Assess carrier performance on a corridor

## Auth
Xeneta API key in the `X-Auth` header over HTTPS
(`../authentication/xeneta-authentication.yml`).

## Steps
1. Discover coverage: call `list-all-carriers-reliability`
   (`GET /ocean/schedule-reliability/available-carriers`) and
   `list-all-carriers-emissions`
   (`GET /ocean/carbon-emissions/available-carriers`) to see which carriers
   have data.
2. Call `get-schedule-reliability-data` (`GET /ocean/schedule-reliability`)
   for the corridor and date range; the response splits into
   `carrier_reliability_by_day` and `corridor_reliability_by_day`.
3. Call `get-carbon-emissions-data` (`GET /ocean/carbon-emissions`) for the
   same corridor; compare `carrier_carbon_emissions` against
   `corridor_carbon_emissions` averages.
4. Call `get-carrier-rates` (`GET /ocean/carrier-spread`) to see the
   per-carrier rate distribution (`carrier_spread_by_day`) and weigh price
   against reliability and emissions.

## Rules
- All reads; retry-safe. Respect `429` API-credit metering
  (`../conventions/xeneta-conventions.yml`).
- `403` means the dataset is not in your subscription
  (`../errors/xeneta-problem-types.yml`).
- Keep `date_from`/`date_to` within 31 days.
