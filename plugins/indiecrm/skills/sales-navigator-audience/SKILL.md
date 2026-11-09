---
name: sales-navigator-audience
description: Build and resume a verified Sales Navigator audience in IndieCRM using 1–3 CRM contacts or LinkedIn profile URLs as seeds, or a step-by-step filter interview with live observed result counts. Finalize the real search URL before a separate collection/import request.
---

# Build an IndieCRM Sales Navigator audience

Use this skill for audience design through the connected IndieCRM MCP tools.
This portable workflow
is owned by IndieCRM and needs no local skill path or Mac installation. It stores
planning evidence, not leads. Read [workflow and tool contract](references/workflow.md)
for examples, resume rules and collection boundaries, and [skill boundaries](references/skill-boundaries.md)
when choosing among overlapping workflows.

## Check executable product capabilities before starting

Inspect the host's actually callable tools, including its normal namespace
prefixes. Before creating a draft or changing source filters, require
`request_audience_filter_change`, `observe_audience_search` and
`finalize_audience`. Unverified seeds also require `verify_audience_seed`;
new drafts require `create_sales_navigator_audience` or the original start tool.
The server's `describe_tools` catalog is schema discovery, not evidence that a
tool is callable in this host.

If a required operation is missing, stop before any draft or browser mutation.
Use an available read tool to preserve/report the exact draft ID, revision,
pending request and last observation with its timestamp. Report a product
capability failure, not a finished test. Never substitute a local CLI, SDK,
direct HTTP, generic executor, another connector or a second draft. Do not
finish, reset or replay an existing pending request to hide this failure.
Resume the same draft only after the native product path is actually available.

Resolve the actual workspace app ID and exact product ID with `list_workspaces`
and `list_products`/`get_product`. Follow pagination. Do not confuse a workspace
UUID with its app/data-scope UUID or merge same-name products. Inspect live tool
schemas before calls. Keep source URLs and seed details private.


## Keep product choices grounded in saved records

Keep the chosen workspace/app and ongoing task across digressions, sign-in and
reconnects. Resume them without another workspace question unless the user
changes scope or access is lost. Retain an already selected exact product ID.

Before offering product choices, call `list_products` in that exact workspace
without a search filter and follow every `nextOffset` until null. Combine all
actual rows across pages; a partial page or filtered search is not the complete
inventory. Offer only the exact returned names together with their immutable
product IDs. Use `get_product` to inspect saved context when needed. Keep
same-name products as separate records, even when their names match exactly;
ask only the minimal distinguishing question if a choice remains necessary.

Context inferred from an email, reference person or company must be explicitly
labeled as an inference and kept separate from saved product choices. Never
turn an inferred service description into a product name or ID, present it as a
saved product, or create a product to make the inference appear real. For
example, correspondence about dashboard consulting does not establish a saved
product named Dashboard-/Power-BI-Beratung. Use actual inventory evidence;
example names are never default product choices.

## Choose one starting mode

- **Seeds / lookalike:** `start_sales_navigator_audience` with mode
  `seed_lookalike`, 1–3 distinct same-app `contactId` objects or observed
  `profileUrl` objects, a stable requestId and a short name. Saved CRM context
  is a starting point, not current source verification. Resolve and inspect each
  exact person in the permitted Sales Navigator UI; verify profile links,
  company/location/job context and own signed-in source account. Record each
  through `verify_audience_seed`. Never invent an
  opaque identity or infer a public profile URL from a Sales Navigator ID.
- **Interview:** start with mode `filter_interview` and no seeds. Ask one useful
  filter question at a time, using existing user instructions and reasonable
  explicitly labeled suggestions. Start with the audience's geography or
  business function when neither is known. Seller product features are not
  automatic buyer targeting. When the user delegates choices, make provisional
  evidence-based choices and explain them without demanding another interview.

### Company name from the person profile

Read the company name as an input from the verified LinkedIn **person/contact
profile**. The user need not supply or correct it. Inspect the visible company
field and current Experience roles, their dates/Present labels and actual company
links; preserve parallel current roles rather than choosing the first employer.
A title, historical email signature, seller product or saved CRM company is not
this input. Record the exact observed company in `verify_audience_seed.company`,
plus `companyProfileUrl` and `companyRoleDateLabel` when actually visible; explain
missing links or ambiguous roles in `evidence` without inventing them.

Use the returned `referenceInputs[seedIndex].companyName` for the next company
input. It comes from the verified person observation; `state.seeds[].company`
may still be historical CRM context. Pass the observed name unchanged to a
company-name control when that step is intended; do not substitute the person's
job title or automatically add a Current job title filter. Match a suggested
company against its observed link before selection. A company page may support
the person's employer link; it does not replace reading the contact profile.
Keep inferred company traits and broader audience filters separate from this
source input. If the source is unavailable, preserve the checkpoint and report
that source gap instead of asking the user to type a company the profile exposes.

Both modes use the same persisted draft and filter loop. A named reference such
as Buse is an input to one instance, never a requirement for the product flow.
No demo's geography, size, CXO level, activity or tenure is a default.

## Change, observe, explain

1. Discover available browser capabilities. Use ChatGPT cloud browser first,
   then ChatGPT in-app browser. Reuse a suitable source tab in the background;
   preserve focus and existing tabs. Native Chrome pairing is separate and
   never licenses launching Chrome/extensions. No cookies, tokens, native API
   headers or undocumented source endpoints. Pause only the step requiring a
   real login, consent or security challenge. Stop on source-account changes or
   rate limits; no metered fallback.
2. Call `request_audience_filter_change` with the specific addition/removal. This
   immediately invalidates the previous count, URL and final selection. Apply
   the change in the actual UI. Select the actual Include/Exclude suggestion;
   typing text is not a selected filter. Removing a filter differs from
   excluding its value. Use fresh controls, never recording coordinates.
3. Wait for refreshed results. Read the entire actual filter summary, complete
   page URL, visible result label and own source account URL. Record
   `observe_audience_search`, `observedAt`, `visibleFilterSummary` and count. Use `exact`
   for an actual exact count, `lower_bound` for labels such as `20K+`,
   `approximate` for an approximate source label, or `unknown` with null value
   when unavailable/loading. Never turn a plus-label into an exact total, copy
   an intermediate count, infer count from pagination, or fabricate an opaque
   filter ID. The tool parses the real URL into the saved spec; do not rebuild
   that URL from labels. A stored observation is not live now.
4. After **each actual filter change**, report the actual new label and its
   limitation, then the next useful choice. For example: “Function Legal now
   shows 1K+; exact total unavailable. Next narrow geography?” This is an example,
   not a live result. Keep the current state even for zero or unknown results;
   never silently broaden criteria to avoid zero. You can add and remove
   filters and revisit earlier choices.
5. For seed mode, remove temporary first/last-name lookup filters before the
   audience is finalized. Keep Function, job title and seniority distinct;
   geography and company headquarters distinct; and each tenure field distinct.
   Suggested facets are exploratory, not automatic selections. Derive useful
   common traits from verified seeds and explicit user intent; disagreement
   among seeds is an uncertainty to show, not a reason to invent one persona.
6. Once the intended audience is established, call `finalize_audience` with a
   concrete selectionBasis. This requires all seed observations, current source
   URL/filter spec and an observed count or count bound. Unknown counts and
   an empty global search cannot finalize. Verify the full URL opens the same
   filters in the permitted browser. This selection creates no research batch.

## Resume and collect separately

Use `get_sales_navigator_audience` with the exact appId/draftId; use paginated
`list_sales_navigator_audiences` if the ID was lost. Reuse requestId and identical
operationId/input after uncertain responses. Each mutation needs expectedRevision;
a conflict means reread, not overwrite. The persisted state contains product,
seeds, source account, current spec, pending change, real URL/count/timestamp,
selection and step receipts. A filter change always requires a fresh observation
and selection. Do not recreate a finished draft on restart.

Audience selection alone does not authorize collection. When a separate explicit
collection instruction already exists, quote it to `prepare_audience_collection`
with the selected revision, stable sessionId, actual permitted browser
capabilities and explicit limit (up to 25 for this bounded handoff). Do not invent
a provider from an incomplete request. The tool prepares; it does not collect.
Follow its scope-bound baseline, new/reused/held identity decisions and separate
normalized import digest requirements. Use `save-lead-research` for authorized
persistence and managed images. Batch completion automatically creates/reuses CRM
contacts, companies and list memberships; no pipeline deals or outreach. No new
eligible rows means no new batch. Existing lists are preserved after an audience
mistake. Larger/all-page collection needs its explicit supported route and actual
coverage accounting; a bounded handoff never proves all-page completion.

Report the mode, draft ID/revision, final criteria, observed label and timestamp,
actual search link, selection state, and whether collection was merely prepared
or actually saved. Host observations remain host evidence, not independent
server verification. Close only temporary tabs created for this work when done.
