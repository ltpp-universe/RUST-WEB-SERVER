<center>

## rust-web-server

[![](https://img.shields.io/crates/l/rust-web-server.svg)](./LICENSE)

<center>

[Official Documentation](https://docs.ltpp.vip/rust-web-server/)

> This project started development on May 1, 2024.

## Expected Features

| Feature                   | Supported | Current Status |
| ------------------------- | --------- | -------------- |
| Multi-threading support   | Yes       | Yes            |
| Configurable service      | Yes       | Yes            |
| Hotlink protection        | Yes       | Yes            |
| Gzip support              | Yes       | Yes            |
| Reverse proxy support     | Yes       | Yes            |
| Custom status code pages  | Yes       | Yes            |
| Logging support           | Yes       | Yes            |
| Load balancing support    | Yes       | Yes            |
| Domain binding resolution | Yes       | Yes            |
| Resource parsing          | Yes       | Yes            |
| History mode support      | Yes       | Yes            |
| Custom response headers   | Yes       | Yes            |

## Current Progress

- [x] Custom response headers
- [x] Multi-threading support
- [x] Configurable service
- [x] Logging output
- [x] Resource parsing
- [x] History mode support
- [x] Domain binding support
- [x] Multiple domains on a single port
- [x] Reverse proxy support
- [x] Load balancing support
- [x] Hotlink protection
- [x] Gzip support

## Error Pages

- Error pages are located in the `root_path` with the filename matching the corresponding status code, e.g., `404.html` for a 404 error page.

## JSON Example

> On the first run, a `config.json` configuration file will be generated automatically. Fill it out and restart the service.

> The above configuration sets the current directory as the root directory for file access,  
> listens on ports `80` and `81`, and binds to both `127.0.0.1` and `localhost` for request handling.  
> If a request path is empty, it rewrites the path to `index.html` in the current directory (useful for Vue and other built assets).  
> Logs are saved in the `logs` folder in the current directory.

> The `listen_ip` in the configuration represents the server's IP address. The keys under `bind_server_name` represent domain names or IPs.  
> Typically, `listen_ip` is `127.0.0.1`.  
> If `localhost` is specified in `bind_server_name`, requests can be made to `localhost`,  
> but `127.0.0.1` will not be supported unless explicitly added to `bind_server_name`.  
> If a local mapping is set up, both the mapped domain and `127.0.0.1` must be included in the configuration.

## Reverse Proxy

- The system validates each element in the `proxy` array to check if it is a valid `URL`. If valid, it is added to the reverse proxy list. If the final proxy list is empty, the system loads static resources instead.
- The reverse proxy maps request paths and parameters. If a path is included in the `proxy` element of the configuration file, it will be ignored.
- When using a reverse proxy, a random proxy address `PROXY_URL` is selected from the list. The system extracts the URL without the `path`, while the actual `path` comes from the request. Parameters are appended accordingly.
- If parameters in `PROXY_URL` conflict with the request parameters, the request parameters will be appended.

## Load Balancing

- When using a reverse proxy, a random proxy address `PROXY_URL` is selected from the list.

## GZIP Compression

- The `Accept-Encoding` header must include `gzip`, and the response header must contain `Content-Encoding: gzip`.
- The `gzip_level` setting controls compression efficiency, with values ranging from `1 - 9`.
- Higher values result in better compression but require more processing time. A value of `4` or `5` is recommended.

## Exceptions

- If errors occur while loading pages or requests, try increasing the `buffer_size` value.

## Hotlink Protection

- `hotlink_protection` uses regular expressions for matching (the system also internally handles `../` path traversal issues).

## Logging

> Supports configuration, date-based logging, and complete request records.
