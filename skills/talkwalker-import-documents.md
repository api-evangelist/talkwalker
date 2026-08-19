---
name: Import your own documents and custom metrics into a Talkwalker project
description: >-
  Push first-party content (support tickets, survey responses, reviews) into a Talkwalker project so
  it is analysed alongside social data — including the match rule that causes most import failures.
api: openapi/talkwalker-documents-openapi.yml
generated: '2026-08-13'
method: generated
source: >-
  openapi/_original/talkwalker-openapi.yaml +
  https://developer.talkwalker.com/guides/uploading-documents +
  https://developer.talkwalker.com/docs/troubleshooting/error-handling
operations:
  - GET /api/v1/search/info
  - POST /api/v2/talkwalker/p/{project_id}/custom_metrics/add
  - POST /api/v2/docs/p/{project_id}
  - POST /api/v2/docs/p/{project_id}/{action}
  - POST /api/v2/docs/p/{project_id}/d/{dataset_id}
  - POST /api/v2/talkwalker/p/{project_id}/topics/import
---

# Import your own documents and custom metrics into a Talkwalker project

Base URL: `https://api.talkwalker.com` — the documents surface lives under `/api/v2`.

Requires a **`read_write`** token. Insufficient rights come back as error code `13` with
`rights_req` and `rights_got` naming the gap.

**Import is free** — it costs zero credits. Only exporting results costs credits. There is no
economic reason to batch conservatively; batch for throughput and for the 120 calls/min limit.

## The rule that breaks most imports

> A document that does not match a project's queries **cannot be imported into that project**.

The document must satisfy the project's `languages`, `countries`, `source types` and blocked-source
settings **and** match the query of at least one topic. When it does not, the import fails with
"Does not match any xyz" and `details` names the part that did not match.

The documented fix is to give the project a topic that will always match your uploads, e.g. a topic
containing `domainurl:"http://my-site.com/"`, and to create it before the first import:

```
POST /api/v2/talkwalker/p/{project_id}/topics/import?access_token=<token>
```

## Steps

### 1. Confirm the project

```
GET /api/v1/search/info?access_token=<token>
```

Use a `project_id` this token is actually allowed to write to, or expect error `29`.

### 2. Declare custom metrics first, if you need them

```
POST /api/v2/talkwalker/p/{project_id}/custom_metrics/add?access_token=<token>
```

Define the metric before you reference it on imported documents. The rest of the lifecycle:

```
GET    /api/v2/talkwalker/p/{project_id}/custom_metrics/list
POST   /api/v2/talkwalker/p/{project_id}/custom_metrics/update
POST   /api/v2/talkwalker/p/{project_id}/custom_metrics/delete
DELETE /api/v2/talkwalker/p/{project_id}/custom_metrics/m/{metric_name}
```

### 3. Import

Batch of documents into a project:

```
POST /api/v2/docs/p/{project_id}?access_token=<token>
```

Single document, with an explicit action:

```
POST /api/v2/docs/p/{project_id}/{action}?access_token=<token>
```

Reviews, which live in a dataset:

```
POST /api/v2/docs/p/{project_id}/d/{dataset_id}?access_token=<token>          # batch of reviews
POST /api/v2/docs/p/{project_id}/d/{dataset_id}/{action}?access_token=<token> # single review
```

Map your fields against
<https://developer.talkwalker.com/docs/talkwalker-documents/fields> before the first run.

### 4. Handle the failures that matter

| status_code | HTTP | Meaning | Action |
|---|---|---|---|
| `17` | 400 | Could not parse JSON | Fix the payload; do not retry unchanged. |
| `16` | 400 | Invalid operation on document | Read `reason` and `details` — this is where the "does not match" failure lands. |
| `13` | 403 | Insufficient rights | You are using a `read_only` token. |
| `29` | 403 | Project forbidden | Wrong `project_id` for this token. |
| `8` | **401** | Endpoint call limit | A rate limit returned as 401. Back off; stay under 120 calls/min for imports. |

**Retries are not deduplicated.** There is no `Idempotency-Key` on this API, so a POST import that
times out after the server accepted it will double-write if you blindly retry. Track your own
document identifiers and verify before re-sending.

### 5. What you may not import

Social media documents **cannot** be imported into Talkwalker. This is a platform-terms restriction,
not a quota — no batching or retry strategy gets around it.

Source: <https://developer.talkwalker.com/docs/getting-started/api-restrictions>
