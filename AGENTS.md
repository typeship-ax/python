# typeship — agent context

This package contains the generated Python SDK for **typeship** (API v1.0.0, package v0.20.0).

Resolve an OpenAPI or GraphQL Definition, diagnose it, and keep every
selected CLI, MCP, and SDK Target current.

Every operation but one requires a bearer credential: an organization
API key from the console, or an OAuth access token carrying the operation's
read, generate, or write capability and the organization selected during
consent. OAuth grants cannot switch organizations after consent. A browser
session is not a credential for this API. The exception is POST /generate,
which works anonymously with the free plan's limits.

Examples use Parcel, a fictional delivery service. Replace its domains,
repository names, and resource identifiers with your own. The hosted
petstore Definition is a runnable sample.

## Ground rules
- For a linked repository Delivery, commit package customizations to the rolling Draft. Typeship three-way merges those commits with the next unmodified Generation, preserves exact bytes and file modes, and stops for explicit review when both sides touch the same region or file ownership is ambiguous.
- A preserved file participates in the combined package only when the package manifest, exports, build, and tests include it. Configure Target checks for every custom build or test requirement; do not assume a file is published merely because it survives regeneration.
- Application-only wrappers may still live outside this package. Code intentionally shipped from this package belongs on its rolling Draft and must pass the combined-package checks.
- Zero runtime dependencies: everything is built on the standard library (`urllib`), so `pip install` pulls in nothing else.
- `api.md` is the native method reference; `api.json` is the machine-readable operation, schema, safety, and example contract. Read them before guessing.
- Start with the local build or installation instructions in `README.md`. Generation does not publish a registry package.

## Authentication
- Bearer token: `TYPESHIP_TOKEN` env var, or the `bearer_token` client argument.

## Using the SDK
```python
from typeship import TypeshipClient

client = TypeshipClient()  # auth options above
```
- Methods raise rather than returning a result object: catch `ApiError` for any documented failure, `ResponseParseError` for malformed successful JSON, or `TransportError` when no response arrived.
- Payloads are `TypedDict`s, so they are plain dicts at runtime: `account["id"]`, not `account.id`. That is the JSON exactly as the API sent it, with no conversion layer to drift.
- Paginated methods return an iterator that walks every page: `for item in client.x.list():`.
- Every method takes `request_options={"timeout": ..., "max_retries": ..., "headers": {...}}` for per-call overrides.
- The same surface exists awaitable on the `Async...Client` (`await client.x.get()`, `async for` over pages and streams).
- Uploads take `bytes`, an open binary file, or a `(filename, data, content_type)` tuple.

## Documentation
- The reference for this exact package: `api.md` (offline, always current with the code).
- Conceptual guides live on the docs site. For questions about how the API's concepts fit together (flows, ordering, environments), fetch `https://typeship.dev/llms-full.txt` and read the relevant sections; `https://typeship.dev/llms.txt` is the page index. Relative links in the spec resolve against `https://typeship.dev`.
