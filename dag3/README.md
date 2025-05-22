# Dag 3 - Overvåkning

## Metrikker i Minecraft server

### Installer plugin

https://github.com/sladkoff/minecraft-prometheus-exporter

### docker-compose.yml plugin

```yaml
services:
  minecraft:
    image: itzg/minecraft-server
    container_name: mc-server
    ports:
      - "25565:25565"
      - "9100:9100" # Port for Prometheus exporter
    environment:
      EULA: "TRUE"
      MEMORY: "4G"
      TYPE: "PAPER" # PaperMC optimalisert motor
      ENABLE_RCON: "true"
      RCON_PASSWORD: "your_strong_rcon_password" # CHANGE THIS!
    volumes:
      - ./data:/data
    restart: unless-stopped
```

## Prometheus

### docker-compose.yml prometheus

```yaml
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - ./prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    restart: unless-stopped
    networks:
```

### prometheus.yml minecraft

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s
  scrape_timeout: 10s

scrape_configs:
  - job_name: 'minecraft'
    static_configs:
      - targets: ['localhost:9100']
        labels:
          server_name: 'minecraft'
```

## Metrikker fra Raspberry pi

### docker-compose.yml node_exporter

```yaml
node_exporter:
  image: prom/node-exporter:latest # Use an ARM-compatible version if needed, though 'latest' often works
  container_name: node_exporter
  ports:
    - "9100:9100" # Expose on the host
  volumes:
    - /proc:/host/proc:ro
    - /sys:/host/sys:ro
    - /:/rootfs:ro
  command:
    - '--path.procfs=/host/proc'
    - '--path.sysfs=/host/sys'
    - '--path.rootfs=/rootfs'
    - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
  pid: host # Important for accessing host process information
  restart: unless-stopped
```

### promethus.yml node_exporter

```yaml
- job_name: 'node_exporter'
   static_configs:
     - targets: ['node_exporter:9100']
       labels:
         instance: 'raspberry-pi-5'
```
