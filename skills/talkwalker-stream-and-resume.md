---
name: Monitor mentions in real time with a resumable Talkwalker collector
description: >-
  Define a Talkwalker stream, attach a collector so a dropped connection can be resumed without data
  loss, and consume the chunked JSON result stream correctly.
api: openapi/talkwalker-streaming-openapi.yml
generated: '2026-08-13'
method: generated
source: >-
  openapi/_original/talkwalker-openapi.yaml +
  https://developer.talkwalker.com/docs/overview/streaming-api-v3 +
  https://developer.talkwalker.com/docs/troubleshooting/error-handling
operations:
  - GET /api/v3/stream/info
  - PUT /api/v3/stream/s/{stream_id}
  - PUT /api/v3/stream/c/{collector_id}
  - GET /api/v3/stream/c/{collector_id}/results
  - POST /api/v3/stream/c/{collector_id}/pause
  - POST /api/v3/stream/c/{collector_id}/resume
  - DELETE /api/v3/stream/s/{stream_id}
---

# Monitor mentions in real time with a resumable Talkwalker collector

Base URL: `https://api.talkwalker.com` — the streaming surface lives under `/api/v3`, not `/api/v2`.

You need a **`read_write`** access token: creating and deleting streams and setting rules all require
write rights. A `read_only` token fails with error code `13`, and the response tells you exactly what
was missing in `rights_req` vs `rights_got`.

## Why a collector rather than a bare stream

Reading `GET /api/v3/stream/s/{stream_id}/results` gives you the live feed and nothing else — if the
connection drops you lose whatever arrived while you were gone. A **collector** buffers the stream
and hands out a `resume_offset`, so a reconnect picks up exactly where you stopped. Use a collector
for anything you cannot afford to miss.

## Steps

### 1. Name things legally

Stream, rule and collector IDs may contain only lowercase letters, digits, `-` and `_`, and **must
start with a lowercase letter**. Anything else is rejected.

### 2. Create or replace the stream

```
PUT /api/v3/stream/s/{stream_id}?access_token=<token>
```

The operation is documented as "create or replace", so it is safe to re-issue with the same body —
this is the closest thing Talkwalker has to idempotency. There is **no `Idempotency-Key` header**
anywhere in this API (`conventions/talkwalker-conventions.yml`), so no other write is retry-safe.

Watch for:

- `20` — account stream maximum reached (`number_max` tells you the cap).
- `21` — a stream with that `stream_id` already exists (when creating rather than replacing).
- `19` — too many rules; `number_max` / `number_available` / `number_saving` tell you by how much.
- `23` — you defined a stream with no rules; it cannot be streamed.

Confirm with `GET /api/v3/stream/info` (lists streams and collectors) or
`GET /api/v3/stream/s/{stream_id}`.

### 3. Attach a collector

```
PUT /api/v3/stream/c/{collector_id}?access_token=<token>
```

Also create-or-replace. Verify with `GET /api/v3/stream/c/{collector_id}`.

### 4. Consume it

```
GET /api/v3/stream/c/{collector_id}/results?access_token=<token>&pretty=false
```

The response is a **chunked JSON stream**, not a single document. Parse chunk by chunk on
`chunk_type`:

- `CT_RESULT` — a result document. This is what you store.
- `CT_CONTROL` — connection metadata. **Persist `chunk_control.resume_offset` every time you see
  one.** It also carries `connection_id` and `collector_id`.
- `CT_ERROR` — an error delivered *in-band*. A stream can fail without the HTTP status ever changing,
  so a client that only checks response codes will silently believe it is still healthy.

Streaming bills **1 credit per result with no per-call floor** — cheaper per document than Search,
but it runs continuously, so cap it with `max_hits` / `end_behaviour` if you do not want an open
meter.

### 5. Resume after a disconnect

A collector stops for ordinary reasons: `max_hits` reached, `end_behaviour` fired, credits exhausted,
server or network trouble. Resume from the last offset you persisted:

```
GET /api/v3/stream/c/{collector_id}/results?access_token=<token>&resume_offset=<resume_offset>
```

Two disconnects are **not** retryable the same way:

- `24` — "Stream got disconnected because newer stream running". Another connection opened with the
  same `stream_id` and evicted you. Run exactly one connection per stream_id; reconnecting in a loop
  just makes two clients fight.
- `30` — HTTP 429, maximum concurrent streams reached. Close a connection before opening another.

### 6. Pause, resume, tear down

```
POST /api/v3/stream/c/{collector_id}/pause?access_token=<token>
POST /api/v3/stream/c/{collector_id}/resume?access_token=<token>
DELETE /api/v3/stream/s/{stream_id}?access_token=<token>
```

Pause the collector rather than dropping the connection when you want to stop consuming but keep the
buffer. Delete streams you no longer need — the account stream cap (error `20`) is a hard ceiling.

## Exporting past data instead of live data

Streams are forward-looking. For history, create an export task and poll it:

```
POST /api/v3/stream/s/{stream_id}/export?access_token=<token>
GET  /api/v3/tasks/export/{task_id}?access_token=<token>
GET  /api/v3/tasks/export?access_token=<token>
DELETE /api/v3/tasks/export/{task_id}?access_token=<token>
```

The same per-source export restrictions apply as for Search — see
`rate-limits/talkwalker-rate-limits.yml`.
