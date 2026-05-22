# channel-atoms — Goals

> Communication channels canonicalized — Slack, email, SMS, webhook, IRC, Matrix, Discord, RSS, iMessage. Typed protocols, endpoints, delivery semantics, transports, and auth methods so any agent or runtime can declare a channel once and reach it everywhere.

*This document is derived from `aish/ARCHITECTURE.md` (now `xdao/xdao/ARCHITECTURE.md` §The *-Atoms Catalogs). Sections marked **Generated** are pattern-based and are intended as a starting point for revision, not as decided plan.*

---

## What this catalog makes civilization-grade

Every agent framework reinvents channel adapters. LangChain, AutoGen, n8n, Zapier, Make — each has its own typing for "send a Slack message" or "POST a webhook." Same five fields (endpoint, auth, payload shape, rate limit, delivery guarantee), encoded ten different ways. There is no shared vocabulary.

By cataloging the primitives, `channel-atoms` turns this domain from opaque-and-ephemeral to typed, versioned, composable, machine-readable, and open — the civilization-grade properties the ecosystem requires.

## What it catalogs

### Atom types

- **`protocol`** — Wire-level protocol (HTTPS webhook, SMTP, MLS / Matrix, XMPP, Matter, AMQP, RSS/Atom, Apple Push, ActivityPub).
- **`endpoint`** — Concrete reachable target (URL, mailbox, channel ID, room ID, phone number, push token).
- **`delivery-semantic`** — At-most-once / at-least-once / exactly-once / best-effort / fire-and-forget.
- **`transport`** — Push vs pull, sync vs async, batched vs streamed, retry shape, polling cadence.
- **`auth-method`** — How the channel authenticates (bearer token, OAuth 2.0, mTLS, HMAC-signed webhook, app password, none, OIDC).

### Compositions: `channels`

A channel composition assembles protocol + endpoint + delivery-semantic + transport + auth into a complete addressable channel — e.g., "post-to-slack-engineering": protocol=slack-webhook, endpoint=https://hooks.slack.com/services/..., delivery=at-least-once, transport=push-sync, auth=signed-secret.

### Rule types

- **`rate-limit`** — Per-endpoint, per-account, or per-protocol throughput bounds (e.g., Slack incoming webhook: 1/sec/channel).
- **`auth-required`** — Which auth-method an endpoint demands (e.g., Slack post requires `signed-secret`; Discord requires `bearer-token`).
- **`ordering-guarantee`** — Whether the channel preserves order across publishers (Slack: yes within channel; SMS: best-effort).

## Runtime consumers

- **aish** — Output sinks for command results, notifications, audit emissions. A user can `aish notify slack-engineering "deploy complete"` and the runtime resolves `slack-engineering` to a channel composition.
- **olympus** — Event sinks for agent emissions, governance attestations, alert routing.

## Status & priority

**Current status:** `proposed`

**Priority tier:** Tier 3 — Build when supporting runtimes mature

**Trigger / activation condition:** First aish / olympus integration that needs more than one channel type. Initially served by hardcoded adapters; cataloged once the duplication starts hurting.

## Roadmap *(Generated — milestone shapes mirror aish's roadmap pattern; revise as actual work begins)*

### v0.1 — Bootstrap & spec acceptance

**Goal:** Schema accepted. 5–10 seed atoms across the common channels (Slack, email, webhook, SMS).

**Success criterion:** aish notify can resolve a channel-atoms composition and successfully deliver to at least Slack + email.

**Kill criterion:** Channels turn out to be too vendor-specific to model in a shared schema without a per-vendor extension explosion — pivot to per-vendor sub-catalogs.

**Work:**

- [ ] XAIP: channel composition schema with delivery-semantic + transport
- [ ] Define 5 atom type schemas
- [ ] Seed atoms: Slack incoming webhook, SMTP email, generic HTTPS webhook, Twilio SMS, RSS
- [ ] aish notify integration

### v0.2 — Adoption & expansion

**Goal:** Coverage of the common-use channels. Rule enforcement (rate limit, auth required).

**Work:**

- [ ] Add Discord, Matrix, iMessage, ActivityPub, Push (FCM/APNs)
- [ ] Rate-limit rule enforcement at runtime
- [ ] Reference 'safe-by-default' channel composition (auth required, idempotency keys, retry budget)

### v1.0 — Operational

**Goal:** Default channel vocabulary across the AI agent ecosystem. New runtimes adopt channel-atoms instead of writing their own.

## Concrete atom example *(Generated — illustrative, not seed content)*

```yaml
channels/post-to-slack-engineering/definition.yml
---
id: post-to-slack-engineering
type: composition
version: 0.1.0
protocol: { ref: atoms/protocol/slack-incoming-webhook }
endpoint: { ref: atoms/endpoint/slack-engineering-hook }
delivery_semantic: { ref: atoms/delivery-semantic/at-least-once }
transport: { ref: atoms/transport/push-sync }
auth: { ref: atoms/auth-method/signed-secret }
```

## Adoption strategy *(Generated)*

aish notifies + olympus emissions pull first. Wider adoption follows reference compositions — once one team's notification routes are captured in channel-atoms, neighboring teams reuse the vocabulary.

## Civilization-grade property checklist

Every catalog must satisfy these before v1.0. Failing any blocks a release.

| Property | Mechanism in this catalog |
|---|---|
| Typed | JSON Schema in `schemas/` validates every atom, composition, rule |
| Versioned | Every atom has a semver `version` field; compositions reference atoms by version-pinned ID |
| Machine-readable | `exports/catalog.json` published on every release |
| Composable | Compositions reference atoms by ID; CI verifies references resolve and no circular dependencies |
| Open | Apache-2.0 licensed; LICENSE file present |
| Durable | No external dependencies for primary content (no remote image URLs, no vendor APIs in the hot path) |

## Related

- **Spec:** [atoms-spec](https://github.com/convergent-systems-co/atoms-spec) — the canonical structure every catalog conforms to
- **Tools:** [atoms-tools](https://github.com/convergent-systems-co/atoms-tools) — CLI for validate / export / bootstrap / resolve
- **Federation:** [xdao](https://github.com/convergent-systems-co/xdao) — ecosystem directory and discovery
- **Umbrella:** [atoms](https://github.com/convergent-systems-co/atoms) — every catalog as a git submodule
- **Manifest:** [`ATOMS.yml`](./ATOMS.yml) — this catalog's machine-readable manifest
- **Standard:** [`README.md`](./README.md) — catalog overview and contribution flow
