# Sage X3 GraphQL API

## Description

Sage X3 exposes a GraphQL interface through its **Xtrem** middleware layer, which sits between client applications and the X3 ERP engine. The GraphQL endpoint enables flexible, strongly-typed queries and mutations against the full Sage X3 business object model — including financials, sales, purchasing, inventory, manufacturing, and HR data — with selective field retrieval and structured filtering.

Access is gated by **Connected Apps** (client credentials flow): a connected app is registered in the X3 administration console, receives a client ID and secret, and exchanges them for a short-lived JWT bearer token used on every GraphQL request.

## Endpoint

```
POST http://<host>:<port>/xtrem/api
```

The port is typically `8124` for on-premise deployments. Cloud-hosted tenants receive an HTTPS base URL from Sage.

## Headers

| Header | Value |
|---|---|
| `Content-Type` | `application/json` |
| `x-authorization` | `<JWT bearer token>` |
| `x-xtrem-endpoint` | X3 endpoint/folder name (e.g. `SAGEX3_SEED`) |

## Authentication

1. Register a **Connected App** in the Sage X3 administration console following [Sage X3 GraphQL Quick Start Guide](https://developer.sage.com/x3/graphql/quick-start/create-connected-app).
2. Exchange the client ID, client secret, and X3 user credentials for a JWT via the Xtrem authentication API (`/xtrem/api/auth` or equivalent).
3. Pass the JWT in the `x-authorization` header on every GraphQL POST.
4. Tokens are short-lived (default 300 seconds); refresh before expiry.

## GraphQL Support

- **Introspection** is supported — a `{ __schema { types { name kind } } }` query returns the full type registry for the connected endpoint/folder.
- **Queries** cover all standard Sage X3 business objects (customers, suppliers, items, orders, invoices, ledger entries, etc.).
- **Mutations** support create, update, and delete operations on writable business objects.
- **Filtering and pagination** are available via query arguments.
- The schema is **endpoint-scoped**: different X3 folders (e.g. `SAGEX3_SEED`, `DEMO`) expose the business objects configured for that folder.

## Documentation

- Developer Hub: https://developer.sage.com/x3/graphql/
- Quick Start: https://developer.sage.com/x3/graphql/quick-start/create-connected-app
- Data Integration Overview: https://developer.sage.com/x3/data-integration

## Community / Open Source

- [x3-graphql-starter](https://github.com/littlestmoe/x3-graphql-starter) — minimal TypeScript starter that authenticates and sends GraphQL queries to Sage X3 via the Xtrem API layer.

## Notes

- The `/xtrem/api` endpoint does **not** use the standard REST `api1` base path; it is a separate middleware service.
- On-premise deployments may require disabling TLS certificate verification for self-signed certificates.
- The `x-xtrem-endpoint` header selects the X3 business folder (dataset) targeted by the query, making a single Xtrem instance capable of serving multiple X3 environments.
- Sage X3 GraphQL is a conceptual data model schema derived from the Sage X3 ERP business object model. Native GraphQL introspection is instance-specific; this SDL schema represents the canonical ERP domain types.
