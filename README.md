# channel-atoms

> Communication channels canonicalized — Slack, email, SMS, webhook, IRC, Matrix, Discord, RSS, iMessage. Typed protocols, endpoints, delivery semantics, transports, and auth methods so any agent or runtime can declare a channel once and reach it everywhere.

`channel-atoms` is a `*-Atoms` catalog in the [Convergent Systems](https://xdao.co) ecosystem. It defines what exists in its domain — typed, versioned, machine-readable, composable, and open — so runtimes (and humans) can stand on shared infrastructure instead of reinventing it.

## Structure

```
channel-atoms/
├── ATOMS.yml              # Catalog manifest
├── atoms/                 # Reusable building blocks
├── channels/              # Compositions assembled from atoms
├── rules/                 # Typed constraint vocabulary
├── schemas/               # Catalog-specific JSON Schemas
├── exports/               # CI-generated machine-readable exports
└── docs/                  # Human-readable documentation
```

### Atom types

- `protocol` — Wire-level protocol (HTTPS webhook, SMTP, Matter, XMPP, MLS, Matrix, AMQP, etc.).
- `endpoint` — Concrete reachable target (URL, mailbox, channel ID, room ID, phone number).
- `delivery-semantic` — At-most-once / at-least-once / exactly-once / best-effort / fire-and-forget.
- `transport` — Push vs pull, sync vs async, batched vs streamed, polling cadence.
- `auth-method` — How the channel authenticates (bearer token, OAuth, mTLS, signed webhooks, app password, none).

### Rule types

- `rate-limit` — Per-endpoint or per-account throughput bounds.
- `auth-required` — Which auth-method an endpoint demands.
- `ordering-guarantee` — Whether the channel preserves message order across publishers.

### Runtime consumers

`aish`, `olympus`

## How to consume

Machine-readable exports are published in [`exports/`](./exports/) on every release:

- `exports/catalog.json` — full catalog dump (every atom, composition, rule)

Exports are deterministic, signed, and versioned. See [`ATOMS.yml`](./ATOMS.yml) for the manifest and the conformance spec.

## How to contribute

1. Read [`ATOMS.yml`](./ATOMS.yml) to understand the catalog's atom types, compositions, and rules.
2. Add a new atom under `atoms/<type>/` or a composition under `channels/<name>/`.
3. Open a PR. CI validates the schema, references, and exports.
4. Larger structural changes go through the [XAIP process](https://github.com/convergent-systems-co/xaips).

## Ecosystem

- **Federation:** [xdao.co](https://xdao.co) · [github.com/convergent-systems-co/xdao](https://github.com/convergent-systems-co/xdao)
- **Spec:** [github.com/convergent-systems-co/atoms-spec](https://github.com/convergent-systems-co/atoms-spec)
- **Tools:** [github.com/convergent-systems-co/atoms-tools](https://github.com/convergent-systems-co/atoms-tools)
- **Umbrella:** [github.com/convergent-systems-co/atoms](https://github.com/convergent-systems-co/atoms) — all catalogs as submodules

## License

Apache-2.0 — see [`LICENSE`](./LICENSE).
