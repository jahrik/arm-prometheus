# arm-prometheus

[![Build](https://github.com/jahrik/arm-prometheus/actions/workflows/build.yml/badge.svg)](https://github.com/jahrik/arm-prometheus/actions/workflows/build.yml)

Multi-arch [Prometheus](https://prometheus.io/) image for the `monitor` swarm stack. Originally built from source on `arm64v8/golang`; now a pinned layer over the official `prom/prometheus` image with the swarm scrape config baked in.

## Run

```bash
docker run -d -p 9090:9090 jahrik/arm-prometheus:latest
curl http://localhost:9090/-/healthy
```

Scrape jobs use swarm service DNS (`tasks.node-exporter`, `tasks.cadvisor`, `tasks.grafana`, `tasks.traefik`); alerts go to `alertmanager:9093`.

## Deploy (swarm)

```bash
docker network create -d overlay monitor   # once
just deploy                                # stack: monitor, behind traefik
```

## Build

```bash
just build
just push
```

CI: PR builds + `promtool check config` + health check; merge to main pushes multi-arch (amd64/arm64/armv7) to Docker Hub.
