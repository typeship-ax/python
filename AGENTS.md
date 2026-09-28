# Typeship: agent guide

Instructions for coding agents that call the Typeship API through this Python SDK (API version 1.0.0, package version 0.25.0).

Resolve an OpenAPI or GraphQL Spec, diagnose it, and keep every
selected CLI, MCP, and SDK Target current.

Every operation but one requires a bearer credential: an organization
API key from the console, or an OAuth access token carrying the operation's
read, generate, or write capability and the organization selected during
consent. OAuth grants cannot switch organizations after consent. A browser
session is not a credential for this API. The exception is POST /generate,
which works anonymously with the free plan's limits.

Examples use Parcel, a fictional delivery service. Replace its domains,
repository names, and resource identifiers with your own. The hosted
petstore Spec is a runnable sample.

## Before writing code
- `api.md` is the method reference; `api.json` is the machine-readable contract: every operation's inputs, outputs, errors, `safety` (`read`, `write`, or `destructive`), and an example. Look up exact names there instead of guessing.
- `README.md` covers installation and setup.
- Zero runtime dependencies: everything is built on the standard library (`urllib`), so `pip install` pulls in nothing else.

## Authentication
- Bearer token: `TYPESHIP_TOKEN` env var, or the `bearer_token` client argument.

## Using the SDK
```python
from typeship import TypeshipClient

client = TypeshipClient()  # auth options above
```
- Methods raise rather than returning a result object: catch `ApiError` for any documented failure, `ResponseParseError` for malformed successful JSON, or `TransportError` when no response arrived.
- Payloads are `TypedDict`s, so they are plain dicts at runtime: `item["id"]`, not `item.id`. That is the JSON exactly as the API sent it, with no conversion layer to drift.
- Paginated methods return an iterator that walks every page: `for item in client.x.list():`.
- Every method takes `request_options={"timeout": ..., "max_retries": ..., "headers": {...}}` for per-call overrides.
- The same surface exists awaitable on the `Async...Client` (`await client.x.get()`, `async for` over pages and streams).
- Uploads take `bytes`, an open binary file, or a `(filename, data, content_type)` tuple.

## Safety
- Read credentials from the environment or a secret store. Never hard-code them, print them, or put them in URLs or command arguments.
- Check an operation's `safety` in `api.json` before calling it. Confirm with the user before running a `write` or `destructive` operation they did not ask for.
- The client already retries transient failures, honoring `Retry-After`, and retries a write only when that is safe. Do not wrap calls in another retry loop: a repeated write can apply twice.

## Documentation
- The reference for this exact package: `api.md` (offline, always current with the code).
- Conceptual guides live on the docs site. For questions about how the API's concepts fit together (flows, ordering, environments), fetch `https://typeship.dev/llms-full.txt` and read the relevant sections; `https://typeship.dev/llms.txt` is the page index. Relative links in the spec resolve against `https://typeship.dev`.
