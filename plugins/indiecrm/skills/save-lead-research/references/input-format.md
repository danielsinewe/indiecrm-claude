# Research input format

These fields come from IndieCRM's research schemas and handlers. Prefer the
connected tool's current schema if the server has been updated.

## Start a batch

`start_research_batch` requires every field below.

| Field                 | Value                                                                                       |
| --------------------- | ------------------------------------------------------------------------------------------- |
| `dataScopeId`         | Destination app UUID from `list_workspaces`.                                                |
| `name`                | List name, 1–200 characters.                                                                |
| `sourceSha256`        | SHA-256 of the actual research source, 64 lowercase hexadecimal characters.                 |
| `leadCount`           | Number of unique importable leads, 1–10,000.                                                |
| `companyCount`        | Number of represented companies, 0–10,000.                                                  |
| `unresolvedCompanies` | Array of up to 500 nonempty company names, each at most 500 characters. Use `[]` when none. |
| `reviewRows`          | Number of rows requiring review, 0–100,000.                                                 |
| `partialSearches`     | Number of incomplete searches, 0–100,000.                                                   |

The result is `{ batchId, complete }`. Preserve the exact digest and metadata
when resuming; a conflicting lead count for an existing source is rejected.

## Append leads

`append_research_leads` requires `dataScopeId`, `batchId`, and `leads` (1–50).
Each lead requires the following keys. Supply `null` for unknown values only
where the schema allows null; do not fabricate required nonempty strings.

| Field                                            | Value                                                                                                            |
| ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| `fullName`, `title`, `companyName`, `department` | Nonempty strings, at most 500 characters each.                                                                   |
| `companyId`                                      | Source company identifier, 1–100 characters; not a CRM company UUID unless that is the actual source identifier. |
| `location`                                       | String of at most 500 characters, or `null`.                                                                     |
| `profileUrl`                                     | Actual LinkedIn Sales Navigator lead URL containing `/sales/lead/`, at most 700 characters.                      |
| `sourceQueryUrl`                                 | Source search URL of at most 2,500 characters, or `null`.                                                        |
| `observedAt`                                     | ISO datetime accepted by the tool schema, or `null`.                                                             |
| `sourceCompanyName`                              | Source company name of at most 500 characters, or `null`.                                                        |
| `employerAliasEvidence`                          | Evidence for an employer alias of at most 2,500 characters, or `null`.                                           |
| `researchDetails`                                | Optional object with only the supported detail fields below.                                                     |

Lead identity is derived from `profileUrl`. The same identity must not appear
twice in one append call. The result is `{ saved }` for the submitted chunk,
not the total number of unique leads across all calls.

## Research details

`backfill_research_details` requires `dataScopeId`, `batchId`, and `items`
(1–50). Each item contains `profileUrl` and a `details` object. That object,
and the optional `researchDetails` above, support these optional string fields:

| Field                                      | Maximum length    |
| ------------------------------------------ | ----------------- |
| `industry`, `companyLocation`              | 500 characters    |
| `summary`                                  | 20,000 characters |
| `durationInRole`, `durationInCompany`      | 100 characters    |
| `titleDescription`                         | 5,000 characters  |
| `companyLinkedInUrl`, `linkedinProfileUrl` | 700 characters    |

Keep unsupported keys out: these input objects use strict schemas.

## Images and completion

`save_research_images` requires `dataScopeId`, `batchId`, and `images` (1–8).
The batch must already be complete.
Each image contains `kind` (`photo` or `logo`), `identity` (1–700 characters),
`sourceUrl` (an HTTPS URL on `media.licdn.com` of at most 2,500 characters), and
`dataBase64` (4–7,000,000 characters). The entire group's combined base64 length
must be at most 7,000,000 characters, and each decoded image must be nonempty
and at most 5,000,000 bytes. For a photo, the identity is the normalized lead
profile URL stored by the importer. For a logo, it is the source company ID used
by the saved leads. The importer normalizes Sales Navigator URLs to
`https://www.linkedin.com` plus the source pathname, dropping query strings and
fragments. Read each returned `saved` flag; do not assume every image was stored.

`finish_research_batch` requires only `dataScopeId` and `batchId`, and returns
`{ batchId, count }`. It rejects incomplete lists and batches already finished.
