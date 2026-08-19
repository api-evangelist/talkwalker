---
name: Export brand mentions from a Talkwalker project
description: >-
  Resolve which Talkwalker projects an access token may read, then page mentions out of one of them
  with the Search API — respecting the credit model, the per-source export restrictions and the
  vendor error envelope.
api: openapi/talkwalker-search-openapi.yml
generated: '2026-08-13'
method: generated
source: >-
  openapi/_original/talkwalker-openapi.yaml +
  https://developer.talkwalker.com/docs/overview/search-api/search-results-project-api
operations:
  - GET /api/v1/search/info
  - GET /api/v1/search/p/{project_id}/results
  - GET /api/v1/status/credits
---

# Export brand mentions from a Talkwalker project

Base URL: `https://api.talkwalker.com`

> Talkwalker's published OpenAPI ships **no `operationId`** on any operation, so every step below is
> addressed by METHOD + PATH. `overlays/talkwalker-search-overlay.yaml` adds deterministic
> operationIds if you need named bindings.

## Before you start

- Authentication is an **API key in the query string**: append `access_token=<token>` to every
  request. There is no `Authorization` header. Treat the whole URL as a secret.
- You need a **`read_only`** token at minimum. Request one from Talkwalker
  (`client-api@talkwalker.com`) — there is no self-service issuance.
- To rehearse without a real token use `access_token=demo`, which works only against the
  non-project search/histogram/streaming endpoints and only for the queries `cats`, `dogs` or
  `cats AND dogs`, returning blogs/forums/news only. See `sandbox/talkwalker-sandbox.yml`.
- **This flow costs money.** The Search API bills 1 credit per returned result **plus a floor of 10
  credits per call**. A wide query paged in small pages burns the floor repeatedly — page as large
  as the endpoint allows.

## Steps

### 1. Find out which projects the token can read

```
GET /api/v1/search/info?access_token=<token>
```

Returns the projects this token is allowed to query. Do not hardcode a `project_id` you did not get
from here — a wrong one comes back as error code `29` ("Access to this project is forbidden"), and a
token that is not linked at all as `11`.

### 2. Check the credit balance before a large export

```
GET /api/v1/status/credits?access_token=<token>
```

Read `result_creditinfo.remaining_credits_monthly`. Estimate the run as
`(10 x number_of_calls) + expected_results`. This endpoint is capped at **10 calls per minute** and
Talkwalker asks that you store the result rather than poll it — call it once at the start of a run,
not per page.

### 3. Page the mentions out

```
GET /api/v1/search/p/{project_id}/results?q=<query>&access_token=<token>
```

- `q` uses Talkwalker's own boolean query syntax
  (<https://developer.talkwalker.com/docs/query-syntax>). A malformed query returns error code `4`
  with the parser complaint in `details` — read `details`, do not retry the same query.
- Page with the offset/limit parameters declared on the operation in
  `openapi/talkwalker-search-openapi.yml`.
- Stay under **60 calls per minute** for in-project search (240/min applies only to the non-project
  global-index endpoint). See `rate-limits/talkwalker-rate-limits.yml`.

### 4. Read every response as a Talkwalker envelope, not as an HTTP status

Every response — success or failure — is a flat JSON object:

```json
{"status_code":"0","status_message":"OK","request":"GET /api/v1/search/p/…","request_id":"#…#"}
```

`status_code` is a **string** and is independent of the HTTP status. Branch on it, not only on the
HTTP code. Full catalog: `errors/talkwalker-problem-types.yml`.

Handle these specifically:

| status_code | HTTP | Do this |
|---|---|---|
| `8` | **401** | Per-endpoint call limit — this is a rate limit wearing a 401. Back off and cache; do not re-auth. |
| `9` | 401 | Out of credits. Stop the run; retrying will not help until the monthly reset. |
| `7` | 401 | Token missing/invalid/inactive — this one really is auth. |
| `3` / `5` | 400 | Read `params` / `param` and fix the request. |
| `30` | 429 | Only reachable on the streaming surface; close a connection first. |

There are **no rate-limit response headers** (`X-RateLimit-*`, `RateLimit-*`, `Retry-After` are all
absent), so budget must be tracked client-side. Log `request_id` on every failure — it is the only
handle support can trace.

## Know what you cannot export

Do not build a pipeline that assumes full content. Talkwalker's published export restrictions:

- **Facebook / Instagram** — no content via API at all; aggregated metrics only, via the Histogram API.
- **X (Twitter)** — only tweet ID, author ID and Talkwalker's sentiment score, capped at 1.5M docs
  per month per account, unless the account holds the X data add-on. Rehydrate through X's own API.
- **Online news / TV Eyes** — content is truncated for copyright; use `content_snippet`, where the
  matched keywords are wrapped in `<b>` tags.
- **LinkedIn, Weibo and other Chinese sources, Disqus, MONITOREDSITE, WEB_NLA, raw Reddit, raw
  TikTok** — not exportable. Reddit and TikTok are available as aggregates via the Histogram API.

Source: <https://developer.talkwalker.com/docs/getting-started/api-restrictions>
