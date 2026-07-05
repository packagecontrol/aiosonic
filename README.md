![github status](https://github.com/sonic182/aiosonic/actions/workflows/python.yml/badge.svg)
[![PyPI version](https://badge.fury.io/py/aiosonic.svg)](https://badge.fury.io/py/aiosonic)
[![Documentation Status](https://readthedocs.org/projects/aiosonic/badge/?version=latest)](https://aiosonic.readthedocs.io/en/latest/?badge=latest)

# aiosonic - lightweight Python asyncio HTTP/WebSocket client

A very fast, lightweight Python asyncio HTTP/1.1, HTTP/2, and WebSocket client.

> [!NOTE]
>
> This repository provides backport for Sublime Text's python 3.8 plugin environment.

The original repository is hosted on [GitHub](https://github.com/sonic182/aiosonic).

For full documentation, please see [aiosonic docs](https://aiosonic.readthedocs.io/en/latest/).


## Features

- Keepalive support and smart pool of connections
- Multipart file uploads
- Handling of chunked responses and requests
- Connection timeouts and automatic decompression
- Automatic redirect following
- Fully type-annotated
- WebSocket support
- HTTP proxy support
- Sessions with cookie persistence
- Elegant key/value cookies
- (Nearly) 100% test coverage
- HTTP/2 (enabled with a flag)


## Installation

To include aiosonic into a package, create a `depencencies.json` with

```json
{
	"*": {
		"*": [
			"aiosonic",
			"charset_normalizer",
			"h2",
			"hpack",
			"hyperframe",
			"onecache",
			"sublime_aio"
		]
	}
}
```


## Getting Started

Below is an example demonstrating basic HTTP client usage:

```python
import asyncio
import aiosonic
import json

async def run():
    client = aiosonic.HTTPClient()

    # Sample GET request
    response = await client.get('https://www.google.com/')
    assert response.status_code == 200
    assert 'Google' in (await response.text())

    # POST data as multipart form
    url = "https://postman-echo.com/post"
    posted_data = {'foo': 'bar'}
    response = await client.post(url, data=posted_data)
    assert response.status_code == 200
    data = json.loads(await response.content())
    assert data['form'] == posted_data

    # POST data as JSON
    response = await client.post(url, json=posted_data)
    assert response.status_code == 200
    data = json.loads(await response.content())
    assert data['json'] == posted_data

    # GET request with timeouts
    from aiosonic.timeout import Timeouts
    timeouts = Timeouts(sock_read=10, sock_connect=3)
    response = await client.get('https://www.google.com/', timeouts=timeouts)
    assert response.status_code == 200
    assert 'Google' in (await response.text())

    print('HTTP client success')

if __name__ == '__main__':
    asyncio.run(run())
```

## WebSocket Usage

Below is an example demonstrating how to use aiosonic's WebSocket support:

```python
import asyncio
from aiosonic import WebSocketClient

async def main():
    # Replace with your WebSocket server URL
    ws_url = "ws://localhost:8080"
    async with WebSocketClient() as client:
        async with await client.connect(ws_url) as ws:
            # Send a text message
            await ws.send_text("Hello WebSocket")
            
            # Receive an echo response
            response = await ws.receive_text()
            print("Received:", response)
            
            # Send a ping and wait for the pong
            await ws.ping(b"keep-alive")
            pong = await ws.receive_pong()
            print("Pong received:", pong)

            # You can have a "reader" task like this:
            async def ws_reader(conn):
                async for msg in conn:
                    # handle the message...
                    # msg is an instance of aiosonic.web_socket_client.Message dataclass.
                    pass

            asyncio.create_task(ws_reader(ws))
            
            # Gracefully close the connection (optional)
            await ws.close(code=1000, reason="Normal closure")

if __name__ == "__main__":
    asyncio.run(main())
```

## HTTP/2 Usage

HTTP/2 requires HTTPS. Enable it at the client level or per-request.

**Client-level** (all requests use HTTP/2):

```python
import asyncio
import aiosonic

async def run():
    client = aiosonic.HTTPClient(http2=True)
    response = await client.get("https://http2.golang.org/reqinfo")
    assert response.status_code == 200
    print(await response.text())

asyncio.run(run())
```

**Per-request** (opt in for a single call):

```python
import asyncio
import aiosonic

async def run():
    client = aiosonic.HTTPClient()
    response = await client.get("https://http2.golang.org/reqinfo", http2=True)
    assert response.status_code == 200
    print(await response.text())

asyncio.run(run())
```

## Api Wrapping

You can easily wrap APIs with `BaseClient` and override its hooks to customize the response handling.

```python
import asyncio
import json
from aiosonic import BaseClient

class GitHubAPI(BaseClient):
    base_url = "https://api.github.com"
    default_headers = {
        "Accept": "application/vnd.github+json",
        "X-GitHub-Api-Version": "2022-11-28",
        # "Authorization": "Bearer YOUR_GITHUB_TOKEN",
    }

    async def process_response(self, response):
        body = await response.text()
        return json.loads(body)

    async def users(self, username: str, **kwargs):
        return await self.get(f"/users/{username}", **kwargs)
    
    async def update_repo(self, owner: str, repo: str, description: str):
        data = {
            "name": repo,
            "description": description,
        }
        return await self.put(f"/repos/{owner}/{repo}", json=data)


async def main():
    # You can pass an existing aiosonic.HTTPClient() instance in the constructor.
    # If not provided, BaseClient will create a new instance automatically.
    github = GitHubAPI()
    # Call the custom 'users' method to get data for user "sonic182"
    user_data = await github.users("sonic182")
    print(json.dumps(user_data, indent=2))


if __name__ == '__main__':
    asyncio.run(main())
```

Note: You may wanna do a singleton of your clients implementations in order to reuse the internal HTTPClient instance, and it's pool of connections (efficient usage of the client), an example:

```python
class SingletonMixin:
    _instances = {}

    def __new__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__new__(cls)
        return cls._instances[cls]

class GitHubAPI(BaseClient, SingletonMixin):
    base_url = "https://api.github.com"
    # ... the rest of the code

# now, each instance of the class will be the first created
gh = GitHubAPI()
g2 = GitHubAPI()

gh == gh2
```


## Contributing

For general features or fixes, contribute to [original repository](https://github.com/sonic182/aiosonic).
