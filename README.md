# Monitoring and Experiment Tracking Setup: Prometheus, Grafana, & W&B (Weights and Biases)

This setup provides a quick way to deploy Prometheus, Grafana, and W&B (Weights and Biases) using Docker Compose. Prometheus and Grafana are used for monitoring services, while W&B is used for experiment tracking in machine learning projects.

## Prerequisites

Ensure that the following are installed:

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Services Included

- **Prometheus**: A time-series database used for monitoring and alerting.
- **Grafana**: A web-based analytics and monitoring dashboard tool.
- **Node Exporter**: Used to expose host system metrics like CPU, memory, and disk usage (via port 9100).
- **Weights and Biases (W&B)**: A machine learning experiment tracking server.

## Docker Compose Configuration

The provided Docker Compose file starts Prometheus, Grafana, and W&B services:

- Prometheus will be available on `http://localhost:9090`
- Grafana will be available on `http://localhost:3000` with default login credentials:
  - Username: `admin`
  - Password: `admin`
- W&B (Weights and Biases) will be available on `http://localhost:8080`

### Prometheus Configuration (`prometheus.yml`)

The Prometheus configuration includes:

- Scraping interval: `5s`
- Scraping the following targets:
  - Prometheus itself on `localhost:9090`
  - Node Exporter metrics on `localhost:9100`
  - Flower Server on `host.docker.internal:8000`

## Steps to Deploy

### 1. Deploy Prometheus & Grafana

1. **Create Prometheus configuration file**:

   Create a `prometheus.yml` file with the following content in the same directory as your Docker Compose file:
   ```yaml
   global:
     scrape_interval: 5s

   scrape_configs:
     - job_name: 'prometheus'
       static_configs:
         - targets: ['localhost:9090']

     - job_name: 'node_exporter'
       static_configs:
         - targets: ['localhost:9100']

     - job_name: 'flower_server'
       static_configs:
         - targets: ['host.docker.internal:8000']

2. **Create Prometheus configuration file**:
   
   Save the following content to a file named `docker-compose.yml`:
   ```yaml
    version: '3.8'

    services:
    prometheus:
        image: prom/prometheus:latest
        container_name: prometheus
        ports:
        - "9090:9090"
        volumes:
        - ./prometheus.yml:/etc/prometheus/prometheus.yml
        networks:
        - monitoring

    networks:
    monitoring:
        driver: bridge

3. **Run Docker Compose**:
   
   In the directory where the `docker-compose.yml` file is located, run the following command:
    ```sh
    docker-compose up -d

4. **Access Prometheus**:
    Visit `http://localhost:9090` in your browser to access the Prometheus dashboard.

### 2. Deploy W&B (Weights and Biases)

1. **Create the Docker Compose File for W&B**:

    Save the following content to a file named `docker-compose.wandb.yml`:

    ```yaml
    services:
    wandb-server:
        image: wandb/local
        container_name: wandb-server-container
        environment:
        - WANDB_API_KEY=<WANDB_API_KEY>
        - WANDB_BASE_URL=http://localhost:8080
        - WANDB_PROJECT_NAME=<PROJECT_NAME>
        ports:
        - "8080:8080"

2. **Run the W&B Docker Compose File**:
    In the directory where the `docker-compose.wandb.yml` file is located, run the following command:

    ```bash
    docker-compose -f docker-compose.wandb.yml up -d
    ```

    This will start the W&B service in detached mode.

3. **Access W&B (Weights and Biases)**:

Visit `http://localhost:8080` in your browser to access the W&B dashboard. You can track and visualize machine learning experiments directly from this interface.
    

## Stopping Services
To stop all services, run the following commands:

For Prometheus:

```bash
docker-compose down
```
For W&B (Weights and Biases):

```bash
docker-compose -f docker-compose.wandb.yml down
```
