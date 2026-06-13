# AGENTS.md

Multi-arch Prometheus image: pinned `FROM` over official `prom/prometheus` with `conf/prometheus.yml` + `rules/` baked in, deployed in the `monitor` swarm stack.

## Commands

```bash
make build                                  # build jahrik/arm-prometheus:latest
docker run --rm --entrypoint promtool jahrik/arm-prometheus:latest check config /etc/prometheus/prometheus.yml
make deploy                                 # swarm stack deploy (stack: monitor)
```

## CI

`build.yml`: Test (build + `promtool check config` + `/-/healthy` poll) on PR; Release (buildx amd64/arm64/armv7 push to Docker Hub) on merge to main. Needs `DOCKERHUB_USERNAME`/`DOCKERHUB_TOKEN` secrets.

## Quirks

- Bump Prometheus via the `FROM` tag; validate config changes with promtool before pushing.
- Scrape jobs target consolidated service names (`tasks.node-exporter`, `tasks.cadvisor`) — one job each, not per-arch.
- `rules/` files are swarmprom-era examples, fully commented out.
- External `monitor`/`elk`/`traefik` overlay networks and `/mnt/g1/prometheus` gluster path — keep that wiring. Traefik labels are 1.x syntax, updated when the traefik stack is.
