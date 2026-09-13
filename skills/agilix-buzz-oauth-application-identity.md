---
name: agilix-buzz-oauth-application-identity
description: >-
  Stand up a machine identity on the Agilix Buzz (DLAP) API and make an authenticated call —
  create an Application Identity account, register an RSA public key, mint a JWT client
  assertion, exchange it for a Bearer token, and verify with GetUser2/GetDomain2.
generated: '2026-09-12'
method: generated
api: Agilix Buzz API (DLAP / xLi)
grounding: >-
  No OpenAPI exists for this API. Every command name and parameter below was read from the
  provider's own published reference pages, fetched 2026-09-12 and listed in
  https://api.agilixbuzz.com/sitemap.xml. Nothing here is invented.
source:
  - https://api.agilixbuzz.com/docs/entry/Concept/OAuth.md
  - https://api.agilixbuzz.com/docs/entry/Command/CreateUsers2.md
  - https://api.agilixbuzz.com/docs/entry/Command/GetUser2.md
  - https://api.agilixbuzz.com/docs/entry/Command/GetDomain2.md
  - https://api.agilixbuzz.com/docs/entry/Concept/CommandUsage.md
operations:
  - CreateUsers2
  - GetUser2
  - GetDomain2
  - GetStatus
---

# Authenticate to the Agilix Buzz API as an application

Use this when an integration, background service or agent needs to call Buzz without a human
at the keyboard. The password flow (`Login3`) still works but Agilix documents it as not
recommended for new integrations, and it fails outright in domains that require MFA for
administrative accounts.

## Before you start

- You need an existing Buzz domain and an administrator account with the **Update User** right
  in it. Agilix provisions domains through sales — there is no self-serve signup.
- Pick an endpoint host. Use `backgroundapi.agilixbuzz.com` for anything a person is not
  waiting on, `interactiveapi.agilixbuzz.com` only when a user is. They have separate
  processing-time budgets; spending the interactive budget on batch work is the mistake this
  split exists to prevent.
- Set a real `User-Agent` that names your system and its version. Agilix requires it, and
  impersonating a Buzz client in the User-Agent is grounds for immediate account suspension.

Sanity-check connectivity first — `GetStatus` needs no credentials:

```
GET https://backgroundapi.agilixbuzz.com/cmd?cmd=getstatus
```

A healthy response carries `code="OK"` plus the build `version` and `dlapversion`.

## Step 1 — Create the Application Identity account

```
POST https://backgroundapi.agilixbuzz.com/cmd?cmd=createusers2
Authorization: Bearer <admin-token>
Content-Type: application/json

{"requests":{"user":[{"domainid":"//myschool","type":"applicationidentity",
 "username":"sis-sync","firstname":"SIS","lastname":"Sync","email":"sis-sync@example.com"}]}}
```

`type: applicationidentity` is what makes this account OAuth-only: you cannot log into it, and
the API rejects any attempt to set or reset a password on it. Record the returned `userid` —
it is your `client_id`.

Grant it only the rights it actually needs. An Application Identity inherits rights and
enrollment exactly like a person, so an over-privileged one is an over-privileged user.

## Step 2 — Generate a key pair locally

```
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out private_key.pem
openssl pkey -in private_key.pem -pubout -out public_key.pem
```

Choose a Key ID (`kid`) at the same time — ASCII letters, digits, `-`, `_`, `.`, max 128
characters. A date or version slug (`2026-09`, `v2`) makes rotation obvious. The private key
never leaves your system, and never goes in a browser.

## Step 3 — Register the public key

```
PUT https://backgroundapi.agilixbuzz.com/api/users/{userid}/keys/{kid}
Authorization: Bearer <admin-token>
Content-Type: application/x-pem-file

-----BEGIN PUBLIC KEY-----
...
-----END PUBLIC KEY-----
```

`204 No Content` means stored. `400` means the key is malformed, too small, or the target is
not an Application Identity account. `401`/`403` means your admin token lacks Update User.

**Do not PUT over a `kid` that is in use.** It replaces the key in place and breaks every
running process still signing with the old one. Rotate by adding a second `kid`, cutting
traffic over, then deleting the first.

## Step 4 — Exchange a JWT assertion for a Bearer token

Build a short-lived JWT signed RS256 with your private key, carrying your chosen `kid` in the
header, then:

```
POST https://backgroundapi.agilixbuzz.com/api/oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
&client_assertion=<signed-jwt>
```

Success is `200` with `{"access_token": "...", "token_type": "Bearer", "expires_in": 3600}`.
The response carries `Cache-Control: no-store` — hold the token in memory, do not cache the
HTTP response. Re-authenticate every half hour for long-running processes; there is no session
state to carry between token requests.

## Step 5 — Call something and verify

```
GET https://backgroundapi.agilixbuzz.com/cmd?cmd=getuser2&userid=<your-app-identity-userid>
Authorization: Bearer <access-token>
```

`GetUser2` confirms the token works and tells you the home domain; `GetDomain2` then reads
that domain. This is exactly the two-call verification every one of Agilix's seven sample
clients performs, and it is deliberately read-only so you can run it repeatedly.

## Reading the response

Buzz answers `200 OK` even when the command failed, and puts the outcome in the envelope:

```json
{"response": {"code": "OK", ...}}
```

Check **both** the HTTP status and `response.code`. Anything other than `OK` is a failure —
treat unknown codes as errors rather than enumerating them. Do not parse `message`; Agilix
warns the strings change without notice. Batch commands return a parallel
`responses.response[]` array, each member with its own `code`, so a partially-successful batch
is normal and must be inspected member by member.

Codes you must branch on:

| Code | Do this |
|---|---|
| `NoAuthentication` | Token missing or expired — get a new one, then retry. |
| `InvalidCredential` | Credentials are wrong. Do not retry until fixed. |
| `AccessDenied` | The account lacks the Right the command page names. Do not retry. |
| `Argument` / `BadRequest` | Your request is wrong. Do not retry. |
| `RateLimit` | Wait `Retry-After` seconds; read `X-RateLimit-Reset`. |
| `ServerOverwhelmed` | HTTP 503. Back off and ramp up slowly. |

## Retries and limits

Retry only `429` and `503`, with exponential backoff (Agilix's reference clients use 1 s to
64 s, max 5 attempts) and always honor `Retry-After`. Note that **429 has two causes here** —
per-endpoint rate limiting and per-customer processing-time limiting — and the `X-RateLimit-*`
headers are what distinguish them.

Two practical scheduling notes from Agilix's own guidance: interactive load spikes within
5–10 minutes of the top of each hour, so run periodic jobs 10–20 or 40–50 minutes past; and
jitter your intervals so multiple integrations do not synchronize onto the same tick.

## Before you write anything

This API documents **no idempotency mechanism**. There is no `Idempotency-Key` header and no
client request id that de-duplicates a replayed write. If a write times out or a streamed
response is truncated, you cannot tell from the protocol whether it landed — you must read
back the entity before retrying, or accept the risk of a duplicate.

Deletes are soft and most have a matching `Restore*` command (`DeleteCourses`/`RestoreCourse`,
`DeleteUsers`/`RestoreUser`, and so on), but **no retention window is published**, so do not
assume a deletion made today is still reversible tomorrow.
