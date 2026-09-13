---
name: agilix-buzz-data-stream-webhook
description: >-
  Subscribe to Agilix Buzz events — validate a Data Stream configuration, set an HTTPS
  (webhook) target on a domain with an event filter, and consume the event envelope safely.
generated: '2026-09-12'
method: generated
api: Agilix Buzz API (DLAP / xLi)
grounding: >-
  No OpenAPI exists for this API. Command names, parameters, timeout/retry defaults and the
  event envelope below were read from the provider's own reference pages, fetched 2026-09-12.
source:
  - https://api.agilixbuzz.com/docs/entry/Concept/DataStream/Overview.md
  - https://api.agilixbuzz.com/docs/entry/Concept/DataStream/Https.md
  - https://api.agilixbuzz.com/docs/entry/Command/SetDataStreamConfiguration.md
  - https://api.agilixbuzz.com/docs/entry/Command/GetDataStreamConfiguration.md
operations:
  - GetDataStreamConfiguration
  - ValidateDataStreamConfiguration
  - SetDataStreamConfiguration
---

# Receive Buzz events on a webhook

Buzz emits roughly 70 event types — entity lifecycle, course content, grades, activity,
inbox, and a full security audit trail — through a per-domain feature called the **Data
Stream**. Five target types are supported; the HTTPS target is the conventional webhook.

Agilix recommends an AWS-hosted target (Kinesis Firehose, Kinesis Data Streams, SQS) over
HTTPS for reliability, and is blunt about why. Read "Know what you are signing up for" below
before you choose the webhook.

## Prerequisite

You need the **ControlDomain** right on the domain, and a Bearer token (see
`agilix-buzz-oauth-application-identity`). All three commands below are POSTs to
`https://backgroundapi.agilixbuzz.com/cmd?cmd=<name>`.

## Step 1 — Read the current configuration first

```
POST https://backgroundapi.agilixbuzz.com/cmd?cmd=getdatastreamconfiguration
Authorization: Bearer <token>
Content-Type: application/json

{"request":{"cmd":"getdatastreamconfiguration","domainid":"//myschool"}}
```

This step is not optional. `SetDataStreamConfiguration` **replaces the domain's entire
configuration** — it is not a partial update. Any target you omit is removed, and an empty
request clears the configuration outright. Targets are matched to the previous configuration
**by position within each type**, not by `title`, so reordering them is read as editing the
targets that used to occupy those positions.

## Step 2 — Validate before you set

```
POST https://backgroundapi.agilixbuzz.com/cmd?cmd=validatedatastreamconfiguration
Authorization: Bearer <token>
Content-Type: application/json

{"request":{"cmd":"validatedatastreamconfiguration","domainid":"//myschool",
 "https":[{"title":"sis-sync","enabled":true,"streamName":"sis-sync",
 "endpoints":"https://hooks.example.edu/buzz","httpMethod":"POST",
 "timeoutSeconds":5,"retries":2,
 "filter":[{"eventType":"EnrollmentEntityCreated","properties":""},
           {"eventType":"EnrollmentEntityDeleted","properties":""}]}]}}
```

This is the only rehearsal affordance in the Buzz API — use it. `SetDataStreamConfiguration`
also tests connectivity before committing, but validating separately lets you fix an endpoint
without touching a live configuration.

## Step 3 — Set it

Same body, `cmd=setdatastreamconfiguration`. On success the newly configured stream (and any
other applicable stream) receives a `DataStreamConfigurationChanged` event.

### HTTPS target attributes

| Attribute | Required | Notes |
|---|---|---|
| `streamName` | yes | Distinguishes this stream from others. |
| `endpoints` | yes | One to five `https://` URLs, semicolon separated. May contain `{PartitionKey}`. |
| `title` | no | Human label only — **not** an identifier for reconfiguration. |
| `enabled` | no | Defaults true. A disabled target is kept but receives nothing. |
| `httpMethod` | no | `POST` (default) or `PUT`. |
| `timeoutSeconds` | no | 1–30, default 2. |
| `retries` | no | Per endpoint before failing over to the next. Default 2. |
| `filter` | no | `eventType` + `properties` pairs. Use it. |

## Step 4 — Handle the envelope

Every record is one line of JSON in a standard wrapper:

```json
{"time":"2022-06-29T16:40:30.9926931Z","guid":"c7a820f6-36ed-48f6-8210-3581238d30a6",
 "domainId":"1176022","type":"DomainEntityChanged","data":{},
 "userId":"93275","agentUserId":"2239","sessionId":"8655695467559064423"}
```

- `guid` uniquely identifies the action across every stream and domain — **this is your
  de-duplication key**, and you need one, because failover and retries can deliver the same
  event more than once.
- `userId` is who the action was taken *as*; `agentUserId` is who actually performed it. They
  differ on a proxied action, which is exactly what an audit trail needs to distinguish.
- `domainId` is the source domain. A descendant domain's events reach both its own streams and
  its ancestors', so a district-level listener sees school-level events with the school's
  `domainId`.

Return a **2xx**, or Buzz retries. Redirects are not followed. HTTPS is mandatory and your
certificate must come from a publicly trusted CA.

## Know what you are signing up for

- **Many events are delivered synchronously**, while the originating API call is still
  running. A slow webhook directly slows the teachers and students who triggered it. Return
  fast; do the work elsewhere.
- **Delivery is at-most-once past 30 seconds.** No notification attempt gets more than 30
  seconds total, whatever your timeout and retry settings say. The first failure after that
  mark ends the attempt and, in Agilix's own words, "that event data will be lost."
- **Ordering is not guaranteed across endpoints.** If the first endpoint times out and the
  second takes over, timestamps can arrive out of order. Use the object version number in the
  payload, not `time`, to decide which state is newer.
- **Filter aggressively.** Restricting both event types and properties is the provider's own
  first recommendation for reliability, and it is what keeps records under the target's
  size limit — exceed it and an "overflow filter" silently replaces your payload with nothing
  but object ids and version numbers.

## Events worth subscribing to first

| Purpose | Event types |
|---|---|
| SIS roster sync | `UserEntityCreated/Changed/Deleted/Restored`, `EnrollmentEntityCreated/Changed/Deleted/Restored` |
| Grade passback | `GradeCreated`, `GradeChanged`, `EnrollmentMetricsChanged` |
| Content pipeline | `CourseItemCreated/Changed/Deleted`, `CourseResourceCreated/Changed/Deleted` |
| Security audit | `AuthLoginFailed`, `AuthMFAFailed`, `AuthAccountLocked`, `AuthProxyLoginStarted`, `DomainPermissionsChanged`, `OAuthClientKeyAdded/Removed` |

The security-audit group is the one most integrations overlook and the one a K-12 district's
security team will ask for.

## Email targets

An email target exists for rare, high-importance events only. It is rate limited to a
configurable number of messages per hour (default 10), and dropping one emits a
`DataStreamEmailDropped` event — subscribe to that too if you rely on email at all.
