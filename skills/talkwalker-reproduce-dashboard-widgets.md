---
name: Reproduce Talkwalker dashboard widgets with the Histogram API
description: >-
  Rebuild Talkwalker dashboard widgets — sentiment, demographics, influencers, world map, themes —
  outside the product using the Histogram and Summary APIs, at a flat 10 credits per call.
api: openapi/talkwalker-histogram-openapi.yml
generated: '2026-08-13'
method: generated
source: >-
  openapi/_original/talkwalker-openapi.yaml +
  https://developer.talkwalker.com/guides/use-histogram-api
operations:
  - GET /api/v1/search/info
  - GET /api/v1/search/p/{project_id}/histogram/{type}
  - GET /api/v1/search/p/{project_id}/histogram/top_influencers
  - GET /api/v1/search/histogram/{type}
  - GET /api/v1/search/summary
  - GET /api/v1/status/credits
---

# Reproduce Talkwalker dashboard widgets with the Histogram API

Base URL: `https://api.talkwalker.com`. A **`read_only`** token is enough.

## When to reach for Histogram instead of Search

The Histogram API returns **aggregates**, not documents — and that is often the only thing available.
For Facebook, Instagram, Reddit and TikTok, Talkwalker cannot export content at all, but aggregated
metrics *can* be exported through Histogram. If a dashboard covers those sources, Histogram is not a
shortcut, it is the only route.

Cost is a flat **10 credits per call** regardless of how much it aggregates — the inverse of Search,
which charges per result. One wide histogram is far cheaper than paging the same data as documents.

## Steps

### 1. Resolve the project

```
GET /api/v1/search/info?access_token=<token>
```

### 2. Pull the histogram

In-project (the usual case — a widget belongs to a project):

```
GET /api/v1/search/p/{project_id}/histogram/{type}?q=<query>&access_token=<token>
```

Across the global index (quicksearch-style, no project):

```
GET /api/v1/search/histogram/{type}?q=<query>&access_token=<token>
```

Key parameters declared on the operation in `openapi/talkwalker-histogram-openapi.yml`:

| Parameter | Purpose |
|---|---|
| `q` *(required)* | Talkwalker boolean query, e.g. `cats AND dogs` |
| `type` *(required)* | The distribution being requested |
| `min` / `max` | Bin bounds |
| `min_include` / `max_include` | Whether the bounds are inclusive |
| `interval` | Bucket width for time distributions |
| `timezone` | Timezone the buckets are cut in |
| `breakdown` | Secondary dimension to split by |
| `value_type` | Which metric the bins carry |
| `top_n` | Cap on returned buckets |

Influencer widgets have their own endpoint:

```
GET /api/v1/search/p/{project_id}/histogram/top_influencers?access_token=<token>
```

For headline totals rather than a distribution, use the Summary API:

```
GET /api/v1/search/summary?access_token=<token>
GET /api/v1/search/p/<project_id>/summary?access_token=<token>
```

> Note the Summary project path is published with angle brackets (`<project_id>`) rather than the
> `{project_id}` braces used everywhere else in the spec. That is Talkwalker's own inconsistency,
> preserved verbatim in `openapi/_original/talkwalker-openapi.yaml`; substitute the id either way.

### 3. Stay inside the limits

- **30 calls/min** in-project, **60 calls/min** on the global index. Exhaustion returns HTTP **401**
  with `status_code` `8`, not 429, and no `Retry-After` header — back off on a client-side timer.
- Check `GET /api/v1/status/credits` once per run, not per widget; that endpoint is capped at 10
  calls/min and Talkwalker asks you to store its result.

### 4. Accept that not every widget is reproducible

Talkwalker publishes a "non-reproducible widgets" list
(<https://developer.talkwalker.com/guides/use-histogram-api/non-reproducible-widgets>). Check it
before promising a one-to-one dashboard clone — some in-product visualisations have no API
equivalent, and the mapping between widgets and histogram types is documented separately at
<https://developer.talkwalker.com/guides/use-histogram-api/widget-mapping>.
