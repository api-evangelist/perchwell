---
name: Search and read RESO Property records
description: Query Perchwell's RESO Web API (OData 4.01) for Property records with $filter, then read individual records by ListingKey.
api: openapi/perchwell-reso-web-api-openapi.yml
operations: [getMetadata, queryProperty, getProperty]
---

# Search and read RESO Property records (RESO Web API)

## Auth
Send `Authorization: Bearer {token}`. The base URL is
`https://www.perchwell.com/api/v2/OData/{system}` where `{system}` is your
originating-system identifier.

## Steps
1. **Inspect metadata (optional).** Call `getMetadata`
   (`GET /$metadata`) to discover available fields and lookup values.
2. **Query.** Call `queryProperty` (`GET /Property`) with OData params:
   - `$filter` — e.g. `StandardStatus eq 'Active'` or
     `ModificationTimestamp ge 2020-01-31T22:21:20.00Z`
   - `$top` (max 200, default 10), `$skip` for paging, `$select` for fields.
3. **Paginate.** When `@odata.count` exceeds the returned page, repeat with
   increasing `$skip` (in `$top`-sized steps) until all matches are read.
4. **Read one.** Call `getProperty` (`GET /Property('{id}')`) with a ListingKey
   to fetch a single record, including nested `Media`.

## Filter operators
`eq ne gt ge lt le and or not`; functions `contains endswith startswith tolower toupper`.

## Rules
- Media is nested inside Property, not a top-level queryable resource.
- Use `ModificationTimestamp ge <ISO>` for incremental sync (see conventions/perchwell-conventions.yml).
