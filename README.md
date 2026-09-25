# hello-site

Minimal fixture site used by the site-manager e2e tests (M2; stateful
since M3).

- `docker-compose.yml` — a single `web` service (busybox:1.36) serving:
  - `/` — a static index page (`hello from the dev site`);
  - `/health` — `200 ok` (probed by the platform health-check);
  - `/increment` / `/state` — a counter persisted in the named volume
    `data` (`/data/counter`). The M3 TTL e2e suite bumps the counter,
    lets the reaper dispose the server, restarts it and asserts the
    restored state.
- The router is a small sh script run per connection by `tcpsvd`
  (busybox httpd only executes CGI below `/cgi-bin`, so root-level
  dynamic endpoints need a real router).
- No ports are published to the host; the stack is reachable only on
  the shared `sitemanager_dev` docker network, behind the platform
  reverse-proxy (`GET /site/preview`), which requires platform auth.
- The healthcheck follows the repository contract
  (docs/ARCHITECTURE.md §2): a standard compose `healthcheck` block.
