# Boomi OpenTelemetry Grafana Observability Stack

## Overview
This repository provides a Docker Compose-based observability stack for a Boomi runtimes, including:
- **Prometheus** for metrics collection
- **Loki** for log aggregation
- **Tempo** for distributed tracing
- **Grafana** for visualization and dashboards
- **Alloy** for OpenTelemetry pipeline and data forwarding

## Directory Structure

- `docker-compose.yml` — Compose file for Boomi runtime, Alloy, Grafana, Prometheus, Loki, Tempo, Consul
- `prometheus/` — Prometheus configuration files
- `loki/` — Loki configuration files
- `tempo/` — Tempo configuration files
- `alloy/` — Alloy configuration files
- `grafana/` — Grafana data and provisioning

## Quick Start

1. Clone the repository:
	```sh
	git clone <repo-url>
	cd boomi-opentelemetry-grafana
	```
2. Edit the `.env` file your values. 
3. Ensure that Docker and Docker Compose are installed. Full instructions can be found here: [Docker Doc: Install Docker Engine](https://docs.docker.com/engine/install/)
4. Start the observability stack:
	```sh
	docker compose up -d
	```
5. Enable OpenTelemetry on the Boomi runtime using Postman. The attached [Postman collection](postman/Boomi_RuntimeObservabilitySettings.postman_collection.json) contains the required API call. Before executing the request, update the following in the collection's Auth and Variables section:
	- Set the username and password under Auth.
	- Set `atomId` under Variables.
	- Update `baseUrl` within Variables to include the Boomi Account Id.
6. Access Grafana at [http://localhost:3000](http://localhost:3000) (default user: admin / admin)
7. In Grafana, navigate to Connections -> Add new connection and add the following:
    - loki: URL http://loki:3100
    - prometheus: URL http://prometheus:9090
    - tempo: URL http://tempo:3200

## Configuration

- **Docker**: Edit `.env` for Boomi and Alloy docker environment variables.
- **Docker**: Edit `.env` for Boomi and Alloy Docker environment variables.
- **Prometheus**: Edit `prometheus/prometheus.yml` to add scrape targets.
- **Loki**: Edit `loki/loki.yml` for log ingestion settings.
- **Tempo**: Edit `tempo/tempo.yml` for trace storage and endpoints.
- **Alloy**: Edit `alloy/alloy.yml` for OpenTelemetry pipeline and exporters.

## Troubleshooting

- Ensure all containers are running and not restarting on a loop.
- Check container logs with `docker logs <container_name>` for errors.

## Useful Links

- [Boomi DockerHub](https://hub.docker.com/r/boomi/atom/)
- [Grafana Documentation](https://grafana.com/docs/)
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Loki Documentation](https://grafana.com/docs/loki/latest/)
- [Tempo Documentation](https://grafana.com/docs/tempo/latest/)
- [OpenTelemetry](https://opentelemetry.io/)

---
Feel free to contribute or open issues for improvements!
