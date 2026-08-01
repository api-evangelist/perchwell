---
name: Publish a listing to the RESO Web API
description: Create the listing agent (Member) then the Property in Perchwell's RESO Web API, and update it, respecting RESO required/conditional fields.
api: openapi/perchwell-reso-web-api-openapi.yml
operations: [createMember, createProperty, updateProperty]
---

# Publish a listing to the RESO Web API

## Auth
`Authorization: Bearer {token}`, `Content-Type: application/json`. Base URL
`https://www.perchwell.com/api/v2/OData/{system}`.

## Steps
1. **Ensure the agent exists.** A Property requires a `ListAgentKey`, so the
   Member must exist first. If not, call `createMember` (`POST /Member`) with
   required fields (`MemberFirstName`, `MemberLastName`, `MemberEmail`,
   `MemberStateLicense`, `OfficeKey`, ...). The response returns the new
   `MemberKey`.
2. **Create the Property.** Call `createProperty` (`POST /Property`) with a valid
   `ListPrice` and the `ListAgentKey` from step 1. If `StandardStatus` is
   `Closed`, also supply `ClosePrice`. The response returns `ListingKey` (201).
3. **Update / attach media.** Call `updateProperty`
   (`PATCH /Property('{ListingKey}')`) for partial edits. To set media, send the
   full `Media` array (max 100): include an existing `MediaKey` to update in
   place; omit `MediaKey` to create new; existing keys not present are removed.

## Rules
- Required fields cannot be set to `null` or `""`; autopopulated fields (keys, timestamps) are ignored on write.
- Field types must satisfy the RESO Data Dictionary constraints (see errors/perchwell-problem-types.yml).
- If any nested Media record fails validation, the whole Property write is rejected.
