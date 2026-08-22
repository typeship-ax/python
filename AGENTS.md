# typeship — agent context

This package contains the generated Python SDK for **typeship** (v0.5.0).

Generate production SDKs, CLIs, and MCP servers from an OpenAPI or
GraphQL spec, and keep every selected output current.

Every operation but one requires an API key, created in the console and
sent as `Authorization: Bearer ak_...`. A browser session is not a
credential for this API. The exception is POST /generate, which works
anonymously with the free plan's limits.

## Ground rules
- Generated code: never edit files in this package by hand — changes are lost on regeneration. Wrap the client in your own code instead.
- Zero runtime dependencies: everything is built on the standard library (`urllib`), so `pip install` pulls in nothing else.
- `api.md` in this package is the complete method reference: every operation, parameter, and error class. Read it before guessing.

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
- No docs site is configured for this API; the spec-derived reference above is the source of truth.
