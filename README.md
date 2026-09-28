# typeship

Python SDK for the Typeship API. [API reference](./api.md)

Resolve an OpenAPI or GraphQL Spec, diagnose it, and keep every selected CLI, MCP, and SDK Target current.

## Installation

```sh
python -m pip install typeship==0.25.0
```

Requires Python 3.11+. The package has no runtime dependencies.

## Quickstart

```python
import os

from typeship import TypeshipClient

client = TypeshipClient(bearer_token=os.environ["TYPESHIP_API_KEY"])

result = client.organization.get()
print(result)
```

## Authentication

- **Bearer token**: `bearer_token=` (a string, or a callable for tokens that expire; after a 401 a callable with a `rejected` parameter is called once with `rejected=True`), sent as `Authorization: Bearer <token>`.

`client.with_credentials(...)` takes the same credential arguments and returns a client that sends only those: nothing is inherited and no environment variable is read. It shares the original client's connections and other settings.

The async client also accepts `async def` token callbacks. When a request sent with a callback token gets a 401, the client resolves the credential again and resends once; a static credential is never resent.

`default_headers=` adds headers to every request (API version headers, tenant ids).

## Async

`AsyncTypeshipClient` has the same methods, awaitable; pages and streams are `async for`. Requests run on the event loop's default executor, so nothing blocks the loop and there is still nothing to install:

```python
import asyncio
import os

from typeship import AsyncTypeshipClient


async def main() -> None:
    async with AsyncTypeshipClient(bearer_token=os.environ["TYPESHIP_API_KEY"]) as client:
        result = await client.organization.get()
        print(result)


asyncio.run(main())
```

Because async calls run the synchronous standard-library transport in an executor, cancelling the coroutine stops waiting for its result but cannot interrupt a socket call already running in that worker. `timeout` still bounds each socket attempt; it is not one wall-clock deadline across retries.

## Errors

Methods raise rather than returning a result, which is how Python SDKs read:

```python
from typeship import ApiError, NotFoundError, RateLimitError, ResponseParseError, TransportError

try:
    result = client.organization.get()
except NotFoundError:
    ...             # every 404, documented or not
except RateLimitError as exc:
    exc.rate_limit  # when to retry
except ApiError as exc:
    exc.code        # stable API code, or http_<status> fallback
    exc.status      # the HTTP status
    exc.body        # the parsed error payload
    exc.request_id  # the API's request id, when it sent one
    str(exc)        # API detail and a suggested next step
except ResponseParseError as exc:
    exc.body        # malformed successful JSON, preserved as text
except TransportError:
    ...             # no response at all: network, DNS, timeout
```

Each status family has one class, raised whether or not the operation documents the status: `BadRequestError` (400), `UnauthorizedError` (401), `ForbiddenError` (403), `NotFoundError` (404), `ConflictError` (409), `UnprocessableEntityError` (422), `RateLimitError` (429), and `ServerError` (5xx). All are `ApiError` subclasses.

All SDK exceptions expose `code`, `status`, `request_id`, `body`, and an actionable message. Transport failures have no HTTP status or API body.

## Runtime validation

Types catch mistakes when you compile; they cannot see an API that has drifted from its spec at runtime. `validate=True` checks JSON request and response bodies against the spec's own schemas, with no dependencies, since the schema tables ship as plain data in this package:

```python
client = TypeshipClient(validate=True)      # raises ValidationError on mismatch
client = TypeshipClient(validate="warn")    # logs a warning and proceeds
```

A request body is checked before it reaches the wire, so a call that would have been rejected never leaves the process. `ValidationError.violations` lists each path and what was wrong with it. Off by default: validation costs a walk of every body.

## Configuration

```python
client = TypeshipClient(
    base_url="https://typeship.dev/api/v1",  # default
    timeout=30.0,      # per attempt
    max_retries=2,     # retries after the first attempt
    debug=True,        # one line per attempt on stderr, never headers or bodies
)
```

Configuration also reads from the environment (`TYPESHIP_BASE_URL`, `TYPESHIP_API_KEY`).

Timeouts apply to each attempt. By default, the client makes up to two retries for `408`, `429`, `500`, `502`, `503`, and `504`; non-idempotent calls retry only on `429`, when the operation declares an idempotency key, or when explicitly enabled. `Retry-After` takes precedence over exponential backoff.

Generated from the OpenAPI spec by [typeship](https://typeship.dev).
