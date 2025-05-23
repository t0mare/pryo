# Dag 4 - Overvåkning 2

## Vis metrikker i nettleser

### Python skript
Lagre skriptet som generate_metrics_html.py i Minecraft katalogen. lag en katalog som heter html med ```mkdir html```

```python
import requests
from datetime import datetime

PROMETHEUS = "http://localhost:9090"

QUERIES = {
    "CPU Usage": 'avg(process_cpu_load_ratio) * 100',
    "Minecraft JVM Memory Usage": 'sum(jvm_memory_bytes_used) / 1024 / 1024 / 1024',
    "Minecraft TPS": 'rate(minecraft_tick_duration_seconds_count[1m])',
}

def query_prometheus(query):
    r = requests.get(f"{PROMETHEUS}/api/v1/query", params={"query": query})
    result = r.json()["data"]["result"]
    return result

html = f"<html><head><title>Minecraft Server Metrics</title></head><body>"
html += f"<h1>Metrics Snapshot ({datetime.now().strftime('%Y-%m-%d %H:%M:%S')})</h1>"

for name, promql in QUERIES.items():
    data = query_prometheus(promql)
    html += f"<h2>{name}</h2><ul>"
    if not data:
        html += "<li>No data</li>"
    else:
        for item in data:
            #instance = item["metric"].get("instance", "unknown")
            value = item["value"][1]
            html += f"<li>{value}</li>"
    html += "</ul>"

html += "</body></html>"

with open("./html/index.html", "w") as f:
    f.write(html)
```

Prøv å kjør det med ```python generate_metrics_html.py```

## Cron

For at skriptet skal kjøre hvert 5 minutt.
```crontab -e```
```*/5 * * * * /usr/bin/python3 /home/shakan/minecraft/generate_metrics_html.py```

## Web server

### nginx

Legg til dette i ```docker-compose.yml``` for å starte en web-server:

```yaml
  nginx:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
```

---

## Videre visualisering med Grafana

### docker-compose.yml grafana

```yaml
grafana:
  image: grafana/grafana:latest
  container_name: grafana
  user: "1000:1000"
  ports:
    - "3000:3000" # Grafana web interface
  volumes:
    - ./grafana_data:/var/lib/grafana
  environment:
    - GF_SECURITY_ADMIN_USER=admin # Change as needed
    - GF_SECURITY_ADMIN_PASSWORD=your_strong_grafana_password # CHANGE THIS!
    - GF_USERS_ALLOW_SIGN_UP=false
  restart: unless-stopped
  ```

### Grafana add Prometheus source

Connections > Data sources < Add data source > Prometheus
Connection = http://prometheus:9090

### Grafana add Minecraft dashboard

https://grafana.com/grafana/dashboards/14756-unifiedmetrics-0-3-x-prometheus/
Copy ID to clipboard

I Grafana
Dashboard > Import
Sett navn, velg riktig data source.

### Grafana add node exporter dashboard

https://grafana.com/grafana/dashboards/1860-node-exporter-full/

Copy ID to clipboard

I Grafana
Dashboard > Import
Sett navn, velg riktig data source.