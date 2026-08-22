# Talkwalker (talkwalker)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Talkwalker is a social media analytics and listening platform that provides REST APIs for tracking brand mentions, analyzing sentiment, measuring campaign performance, and monitoring competitors across 150 million websites and 10+ social networks. The API suite covers search, streaming, histograms, document management, image detection, topic management, and custom metrics.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/talkwalker/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/talkwalker/refs/heads/main/apis.yml)

## Tags

- Social Media Analytics
- Social Listening
- Brand Monitoring
- Sentiment Analysis
- Media Monitoring
- Campaign Analytics

## Timestamps

- **Created:** 2026-06-13
- **Modified:** 2026-06-13

## APIs

### Talkwalker Search API

Search and export a subset of documents from a Talkwalker project, including brand mentions and social data across supported channels. Results are metered at 1 credit per result plus a minimum of 10 credits per call.

- **Human URL:** [https://developer.talkwalker.com/docs/overview/search-api/search-results-project-api](https://developer.talkwalker.com/docs/overview/search-api/search-results-project-api)
- **Base URL:** `https://api.talkwalker.com/api/v2`

#### Tags

- Search
- Social Listening
- Brand Mentions

#### Properties

- [Documentation](https://developer.talkwalker.com/docs/overview)

### Talkwalker Streaming API

Real-time streaming API (v3) for monitoring keyword-based streams and project or topic-level data feeds. Charged at 1 credit per streamed result with no per-call minimum.

- **Human URL:** [https://developer.talkwalker.com/docs/overview/streaming-api-v3](https://developer.talkwalker.com/docs/overview/streaming-api-v3)
- **Base URL:** `https://api.talkwalker.com/api/v2`

#### Tags

- Streaming
- Real-Time
- Social Monitoring

#### Properties

- [Documentation](https://developer.talkwalker.com/docs/overview/streaming-api-v3)

### Talkwalker Histogram API

Reproduce Talkwalker dashboard widgets programmatically by fetching histogram data. Charged at 10 credits per call.

- **Human URL:** [https://developer.talkwalker.com/docs/overview](https://developer.talkwalker.com/docs/overview)
- **Base URL:** `https://api.talkwalker.com/api/v2`

#### Tags

- Analytics
- Dashboards
- Histograms

#### Properties

- [Documentation](https://developer.talkwalker.com/docs/overview)

### Talkwalker Resources API

Manage and retrieve project resources including topics, filters, pages, events, panels, and datasets. Also exposes tag and view (dashboard, report, alert) management endpoints. Free to call — no credit cost.

- **Human URL:** [https://developer.talkwalker.com/docs/overview/resources-api](https://developer.talkwalker.com/docs/overview/resources-api)
- **Base URL:** `https://api.talkwalker.com/api/v2`

#### Tags

- Resources
- Topics
- Tags
- Dashboards

#### Properties

- [Documentation](https://developer.talkwalker.com/docs/overview/resources-api)

### Talkwalker Document API

Import custom documents and modify existing documents within Talkwalker projects. Supports custom metrics creation for imported content. Document imports are free — no credit cost.

- **Human URL:** [https://developer.talkwalker.com/docs/overview](https://developer.talkwalker.com/docs/overview)
- **Base URL:** `https://api.talkwalker.com/api/v2`

#### Tags

- Documents
- Import
- Custom Metrics

#### Properties

- [Documentation](https://developer.talkwalker.com/docs/overview)

### Talkwalker Image API

Detect features and entities within images using Talkwalker's image detection capabilities, enabling logo detection and visual content analytics.

- **Human URL:** [https://developer.talkwalker.com/docs/overview](https://developer.talkwalker.com/docs/overview)
- **Base URL:** `https://api.talkwalker.com/api/v2`

#### Tags

- Image Detection
- AI
- Visual Analytics

#### Properties

- [Documentation](https://developer.talkwalker.com/docs/overview)

## Common Properties

- [Website](https://www.talkwalker.com/)
- [Documentation](https://developer.talkwalker.com/docs/)
- [Git Hub Org](https://github.com/talkwalker)
- [LinkedIn](https://www.linkedin.com/company/talkwalker)
- [Blog](https://www.talkwalker.com/blog)
- [Pricing](https://www.talkwalker.com/pricing)
- [X (Twitter)](https://x.com/Talkwalker)
- [Plans](plans/talkwalker-plans-pricing.yml)
- [Rate Limits](rate-limits/talkwalker-rate-limits.yml)
- [Fin Ops](finops/talkwalker-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
