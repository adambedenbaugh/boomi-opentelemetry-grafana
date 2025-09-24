# Boomi OpenTelemetry Grafana Observability Stack

![](assets/boomi-otel.png)

## Overview
This repository provides a Docker Compose configuration for Boomi's Integration Runtime monitoring with Opentelemetry. 

### Environment Variables (.env file)

Before starting the containers, you must configure the `.env` file with your Boomi-specific values:

| Variable | Description | Where to Find |
|----------|-------------|---------------|
| `BOOMI_ACCOUNTID` | Your Boomi Account ID | Boomi Platform → Settings → Account Information |
| `BOOMI_ENVIRONMENTID` | Target environment for the runtime | Boomi Platform → Manage → Runtime Management → Environments |
| `INSTALL_TOKEN` | Runtime installation token (starts with "atom-") | Boomi Platform → Manage → Runtime Management → New → Basic Runtime → Setup Preference Local → Security Options → Generate Token |
| `LOCALHOST_ID` | Hostname for the Boomi runtime | Defaults to "atom" |
| `ATOM_NAME` | Display name in Boomi Platform UI | Choose a name that will be visible within the Boomi Platform |
| `OBSERVABILITY_HOSTNAME` | Hostname/IP for observability stack | Use actual IP address for local development or DNS hostname for production |

### Components Within Solution

- **Docker** for container management.
- **Prometheus** for metrics collection. 
- **Loki** for log aggregation.
- **Tempo** for distributed tracing. 
- **Alloy** for data forwarding. 

## Prerequisites

Before getting started, ensure you have:

- **Docker** and **Docker Compose** installed ([Installation Guide](https://docs.docker.com/engine/install/))
- **Boomi Account** with access to install a runtime
- **Boomi Runtime** environment ID and install token
- **Postman** for API call to enable Opentelemetry on Boomi runtime

## Architecture

### Demo Setup (Single VM)
All components run on one virtual machine - suitable for testing and demonstrations only.

### Production Setup (Multi-VM)
- **VM 1**: Boomi Runtime + Grafana Alloy
- **VM 2**: Grafana + Prometheus + Loki + Tempo

---


## Quick Start for Demo

The quick start for demo will install all containers on a single VM.

> [!WARNING]
> This quick start should only be used for demos and is not production-ready. Prometheus, Loki, and Tempo are known to use high CPU resources. In a production environment, Prometheus, Loki, Tempo, and Grafana should be deployed on separate servers. The Boomi runtime and Grafana Alloy are recommended to be deployed on separate servers from the other telemetry agents.

1. Clone the repository:
	```sh
	git clone https://github.com/adambedenbaugh/boomi-opentelemetry-grafana.git
	cd boomi-opentelemetry-grafana
	# Ensure correct permissions for containers
	sudo chmod -R 775 ./
	sudo chown -R 1000:1000 ./
	```
2. Edit the `.env` file with your configuration values. 
3. Ensure that Docker and Docker Compose are installed. Full instructions can be found here: [Docker Doc: Install Docker Engine](https://docs.docker.com/engine/install/)
	
	Add your current user to the docker group:
	```sh
	sudo usermod -aG docker $USER
	```
	Log out of the session and back in for changes to take effect.

4. Start the Boomi runtime and the observability stack:
	```sh
	docker compose -f docker-compose-demo.yml up -d
	```

5. Enable OpenTelemetry on the Boomi runtime using Postman. The attached [Postman collection](postman/Boomi_RuntimeObservabilitySettings.postman_collection.json) contains the required API call. Before executing the request, update the following in the collection's Auth and Variables section:
	- Set the username and password under Auth
	- Set `atomId` under Variables  
	- Set `accountId` under Variables to include the Boomi Account ID

6. Restart Boomi runtime within Boomi's Platform UI.

7. Access Grafana at `http://<host>:3000` (default credentials: admin / admin)

8. In Grafana, navigate to **Connections** → **Data source** → **Add new data source**. If the following sources are not found, click **Connections** → **Add new connection**. Add the following data sources and URLs:
    - **Loki**: URL `http://loki:3100`
    - **Prometheus**: URL `http://prometheus:9090`
    - **Tempo**: URL `http://tempo:3200`

## Quick Start for Production

The quick start for production will install the Boomi runtime and Alloy container on one VM. Grafana, Prometheus, Loki, and Tempo are installed on a second VM.

### VM 1: Boomi Runtime and Alloy

1. Clone the repository:
	```sh
	git clone https://github.com/adambedenbaugh/boomi-opentelemetry-grafana.git
	cd boomi-opentelemetry-grafana
	# Ensure correct permissions for containers
	sudo chmod -R 775 ./
	sudo chown -R 1000:1000 ./
	```
2. Edit the `.env` file with your values. 
3. Ensure that Docker and Docker Compose are installed. Full instructions can be found here: [Docker Doc: Install Docker Engine](https://docs.docker.com/engine/install/)
	
	Add your current user to the docker group:
	```sh
	sudo usermod -aG docker $USER
	```
	Log out of the session and back in for changes to take effect.

4. Start the Boomi runtime and Alloy:
	```sh
	docker compose -f docker-compose-boomi.yml up -d
	```

5. Enable OpenTelemetry on the Boomi runtime using Postman. The attached [Postman collection](postman/Boomi_RuntimeObservabilitySettings.postman_collection.json) contains the required API call. Before executing the request, update the following in the collection's Auth and Variables section:
	- Set the username and password under Auth
	- Set `atomId` under Variables
	- Set `accountId` under Variables to include the Boomi Account ID

6. Restart Boomi runtime within Boomi's Platform UI.

### VM 2: Observability Stack

7. On the second VM, repeat steps 1-3 from above.

8. Start the observability stack:
	```sh
	docker compose -f docker-compose-observability.yml up -d
	```

9. Access Grafana at `http://<host>:3000` (default credentials: admin / admin)

10. In Grafana, navigate to **Connections** → **Data source** → **Add new data source**. If the following sources are not found, click **Connections** → **Add new connection**. Add the following data sources and URLs:
    - **Loki**: URL `http://loki:3100`
    - **Prometheus**: URL `http://prometheus:9090`
    - **Tempo**: URL `http://tempo:3200`

## Configuration

- **Docker**: Edit `.env` for Boomi and Alloy Docker environment variables.
- **Prometheus**: Edit `prometheus/prometheus.yml` to add scrape targets. By default, Prometheus uses port 9090, but in this setup, port 9089 is used externally to avoid a conflict with Boomi’s web service, which also defaults to 9090.
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
