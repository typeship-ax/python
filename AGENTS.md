# typeship — agent context

This package contains the generated Python SDK for **typeship** (API v1.0.0, package v0.9.0).

Resolve an OpenAPI or GraphQL Definition, diagnose it, and keep every
selected SDK, CLI, and MCP Target current.

Every operation but one requires a bearer credential: an organization
API key from the console, or an OAuth access token carrying the operation's
read, generate, or write capability and the organization selected during
consent. OAuth grants cannot switch organizations after consent. A browser
session is not a credential for this API. The exception is POST /generate,
which works anonymously with the free plan's limits.

## Ground rules
- Generated code: never edit files in this package by hand — changes are lost on regeneration. Wrap the client in your own code instead.
- Zero runtime dependencies: everything is built on the standard library (`urllib`), so `pip install` pulls in nothing else.
- `api.md` is the native method reference; `api.json` is the machine-readable operation, schema, safety, and example contract. Read them before guessing.

## Authentication
- Bearer token: `TYPESHIP_TOKEN` env var, or the `bearer_token` client argument.

## Using the SDK
```python
from typeship import TypeshipClient

client = TypeshipClient()  # auth options above
```
- Methods raise rather than returning a result object: catch `ApiError` for any documented failure, or a per-status class such as `NotFoundError`; `TransportError` means no response at all.
- Payloads are `TypedDict`s, so they are plain dicts at runtime: `account["id"]`, not `account.id`. That is the JSON exactly as the API sent it, with no conversion layer to drift.
- Paginated methods return an iterator that walks every page: `for item in client.x.list():`.
- Every method takes `request_options={"timeout": ..., "max_retries": ..., "headers": {...}}` for per-call overrides.
- The same surface exists awaitable on the `Async...Client` (`await client.x.get()`, `async for` over pages and streams).
- Uploads take `bytes`, an open binary file, or a `(filename, data, content_type)` tuple.

## Documentation
- The reference for this exact package: `api.md` (offline, always current with the code).
- Conceptual guides live on the docs site. For questions about how the API's concepts fit together (flows, ordering, environments), fetch `https://typeship.dev/llms-full.txt` and read the relevant sections; `https://typeship.dev/llms.txt` is the page index. Relative links in the spec resolve against `https://typeship.dev`.
