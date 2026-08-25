# typeship

Python SDK for typeship. [API reference](./api.md)

Generated from the OpenAPI spec by [typeship](https://typeship.dev). Change the spec or generation settings, then regenerate; generated files are not hand-edited.

- **Zero runtime dependencies** — built on the standard library, nothing to install but Python
- **Typed payloads** — `TypedDict` models and `Literal` enums, with a `py.typed` marker so type checkers see them
- **Typed exceptions** — every documented error response has a class you can catch by name
- **Retries built in** — idempotent requests retry with exponential backoff and `Retry-After` support

## Install

```sh
pip install typeship
```

## Quickstart

```python
import os

from typeship import TypeshipClient

client = TypeshipClient(bearer_token=os.environ["TYPESHIP_TOKEN"])

for item in client.projects.list():
    print(item)
```

## Authentication

- **Bearer token** — `bearer_token=` (a string, or a callable for tokens that expire), sent as `Authorization: Bearer <token>`.

`default_headers=` adds headers to every request (API version headers, tenant ids).

## Async

`AsyncTypeshipClient` has the same methods, awaitable — pages and streams are `async for`. Requests run on the event loop's default executor, so nothing blocks the loop and there is still nothing to install:

```python
from typeship import AsyncTypeshipClient

async with AsyncTypeshipClient(bearer_token=os.environ["TYPESHIP_TOKEN"]) as client:
    async for item in client.projects.list():
        print(item)
```

Because async calls run the synchronous standard-library transport in an executor, cancelling the coroutine stops waiting for its result but cannot interrupt a socket call already running in that worker. `timeout` still bounds each socket attempt; it is not one wall-clock deadline across retries.

## Errors

Methods raise rather than returning a result, which is how Python SDKs read:

```python
from typeship import ApiError, TransportError

try:
    result = client.projects.retrieve("prj_4f8k2m7x9q1v6b3n")
except ApiError as exc:
    exc.status      # the HTTP status
    exc.body        # the parsed error payload
    exc.request_id  # the API's request id, when it sent one
except TransportError:
    ...             # no response at all: network, DNS, timeout
```

## Runtime validation

Types catch mistakes when you compile; they cannot see an API that has drifted from its spec at runtime. `validate=True` checks JSON request and response bodies against the spec's own schemas — with no dependencies, since the schema tables ship as plain data in this package:

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

Configuration also reads from the environment (`TYPESHIP_BASE_URL`, `TYPESHIP_TOKEN`).
