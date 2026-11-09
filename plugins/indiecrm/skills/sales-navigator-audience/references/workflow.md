# Persisted tool flow

Tools share `appId` (the workspace's app/data-scope ID). The draft locks that app,
product and user; inputs cannot move it. Seed contact IDs are resolved only in
that app. Each person needs independent visible-source verification.

1. Start once: `{appId, productId, requestId, name, mode, seeds}`. For interview
   omit seeds; for seed mode pass 1–3 `{contactId}` or `{profileUrl}` objects.
   A repeated requestId with changed inputs is rejected.
2. Read/resume: `get_sales_navigator_audience({appId,draftId})`. Listing accepts
   optional exact productId, offset and limit; follow nextOffset until null.
3. Before draft or browser mutations, inspect the actual host's executable tool
   inventory. Server schema discovery is not host availability. Missing native
   steps stop the workflow at its saved checkpoint without any pending-request,
   revision or UI change. No local CLI/SDK/direct-HTTP/generic-executor fallback.
   Each ordinary product update uses `{appId,draftId,expectedRevision,operationId}`
   plus the operation's fields directly, without an `operation` selector:

| tool                           | Fields / purpose                                                                                                              |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| request_audience_filter_change | instruction: precise intended addition/removal; invalidates observation and final selection                                   |
| verify_audience_seed           | seedIndex, observedProfileUrl, linkedIdentityUrls, fullName, company, location, title, evidence, sourceAccountUrl, observedAt |
| observe_audience_search        | actual searchUrl, visibleFilterSummary, count, sourceAccountUrl, observedAt; parses actual URL spec                           |
| finalize_audience              | selectionBasis; requires observed audience and count, and every seed verified                                                 |
| archive_audience               | ends this draft; does not delete any contacts, lists or source results                                                        |

Require request/change, observation and finalization tools before starting;
require seed verification only for unverified seeds, and archive only when
requested. `update_sales_navigator_audience` and
`record_sales_navigator_audience_step` retain the old compatibility contract for
existing clients; they are not substitute executors for unavailable native
product operations. Never infer permission or host availability from their
schema being discoverable.

`linkedIdentityUrls` must include the exact input seed link when one exists and
the observed profile link. Public and Sales Navigator links can identify the same
person only when their connection was actually observed. Sales IDs stay
case-sensitive; transient Sales Navigator path tags do not create a new person.
For CRM seeds lacking a link, inspect the saved context and resolve the person
with independent contextual evidence; do not accept a name-only match.

Counts are `{kind:"exact"|"lower_bound"|"approximate",value:<observed number>,label:<actual UI text>}`
or `{kind:"unknown",value:null,label:<actual loading/failure limitation>}`.
The source account URL and observedAt timestamp bind every observation. The server
rejects changed source accounts, future timestamps and observations older than
24 hours; it does not prove a caller actually used a browser. Refresh before
collection, even when an older count is still within that contract window.
The history records actual criteria hashes, count limitations, change intent and
operation receipts. Replaying an identical operation returns the saved result
without appending another step; use a new ID for changed data. History is bounded
at 100 steps and 256KiB per draft; an explicit error preserves the old state.

4. Final audience is `audience_ready`. Its count may be a lower bound or
   approximation. Zero is a valid observation; it does not start a collection.
5. Separate collection preparation:
   `{appId,draftId,audienceRevision,sessionId,collectionInstruction,limit,browsers}`.
   `collectionInstruction` records the actual separate authorized request;
   it is not an automatic confirmation or permission to select a provider.
   Revision changes, missing selection and exact-zero results stop preparation.
   The handoff uses the actual observed URL rather than a reconstructed one.
6. Before ingestion, read all completed relevant lists and every lead page in
   the exact scope. Freeze even an empty baseline. Classify exact identities as
   new/reused/held, preserve unresolved current positions/companies for review,
   and use only eligible new unique rows. Compute sourceSha256 from the frozen
   normalized import artifact bound to scope, audience and baseline. workflowId
   is unrelated to that digest. Preserve artifact/receipts across retries.
7. Import/save uses existing start/append/finish tools; managed photos/logos are
   added from observed bytes. Department is Unspecified unless Function was
   actually observed. Finishing materializes contacts/companies/list memberships
   automatically. No pipeline deal creation, paid fallback, enrichment or outreach
   follows from designing/selecting an audience.
