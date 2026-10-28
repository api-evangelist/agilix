# Agilix

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

Agilix Labs, Inc. is a K-12 education technology company in Orem, Utah that builds the Agilix Learning Suite — a white-label, multi-tenant set of products schools, districts, virtual and online schools and curriculum publishers use to author, deliver and assess online and blended learning. The suite is Buzz (the learning management system), TutorKit (tutoring program management), Publish Anywhere (LTI-based content distribution to any LMS), Dawn (mobile, offline-capable delivery) and BusyBee (an AI teaching assistant built into Buzz). Agilix has built learning technology for more than 25 years and reaches millions of teachers and students in 20+ languages across 200+ countries, running on AWS. Its public developer surface is the Buzz API — also called DLAP or xLi — a command-style HTTP API with 291 documented commands covering domains, users, courses, enrollments, items, questions, submissions, gradebooks, objectives, rights and licensing, plus a configurable Data Stream event feed with roughly 70 event types and five delivery targets including HTTPS webhooks. Buzz is a 1EdTech LTI 1.3 / LTI Advantage platform and exchanges SCORM run-time data. Authentication is OAuth 2.0 client credentials with a JWT client assertion (RFC 6749 + RFC 7523) against an Application Identity account. Agilix publishes an llms.txt for both its corporate site and its API documentation, an ai-plugin.json manifest on two API hosts, weekly dated release notes, and seven first-party sample client libraries on GitHub — but no OpenAPI, no MCP server and no agent card.

**Areas:** Company · Education · EdTech · LMS · K-12 · E-Learning · Tutoring · Assessment · LTI · SCORM · Artificial Intelligence · Event

## Agilix on APIs.io

Everything profiled for Agilix is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/agilix/](https://apis.io/providers/agilix/)** — or go straight to one of the APIs or properties below.

### APIs (1)

- [Agilix Buzz API (DLAP / xLi)](https://apis.io/apis/agilix/buzz-api/) — The Buzz API is the programmable surface of the Agilix Learning Suite. It is a command-style HTTP API rather than a path-and-method REST API: every one of…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Agilix Datastream Webhooks](https://apis.io/asyncapis/agilix/agilix-datastream-webhooks/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [Agilix Buzz API — candidate MCP tool surface](https://apis.io/servers/agilix/agilix-buzz-api-candidate-mcp-tool-surface/)

#### Security (4)

Authentication, trust centre, and vulnerability disclosure.

- [Agilix Authentication](https://apis.io/security/agilix/agilix-authentication/)
- [Agilix Domain Security](https://apis.io/security/agilix/agilix-domain-security/)
- [Agilix Trust Center](https://apis.io/security/agilix/agilix-trust-center/)
- [Agilix Vulnerability Disclosure](https://apis.io/security/agilix/agilix-vulnerability-disclosure/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Agilix Plans Pricing](https://apis.io/plans/agilix/agilix-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Agilix Rate Limits](https://apis.io/rate-limits/agilix/agilix-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.agilix.com/)
- [Documentation](https://api.agilixbuzz.com/docs/entry/Concept/Overview.md)
- [Getting started](https://api.agilixbuzz.com/docs/entry/Concept/Tutorial.md)
- [API reference](https://api.agilixbuzz.com/docs/all.md)
- [Support](https://support.agilix.com/hc/en-us)
- [Blog](https://www.agilix.com/blog)
- [Privacy policy](https://www.agilix.com/privacy)
- [GitHub](https://github.com/AgilixLabs)

## API Evangelist rating

**Kin Score 48.6** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 74.1 |
| Contract quality | 39.0 |
| Contract governance | 0.0 |
| Operational transparency | 76.3 |
| Developer ergonomics | 73.2 |
| Access clarity | 26.3 |

**Agent readiness 26.1** — agent-aware

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | — |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | documented (partial credit) |
| OpenAPI Examples | — |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | yes |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | na |

**Regulatory alignment 29.1** — Education & Research

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/agilix/](https://apis.io/providers/agilix/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Agilix: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/agilix/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
