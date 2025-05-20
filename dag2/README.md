# Dag 2

## Installer vscode

https://code.visualstudio.com/docs/setup/linux

### Plugins

Installer tillegg Remote - SSH

## Installer Docker

https://docs.docker.com/engine/install/ubuntu/

## Minecraft Server

https://github.com/itzg/docker-minecraft-server

### docker compose

https://docs.docker.com/compose/gettingstarted/

- Start server: ```docker compose up -d```
- Logger: ```docker compose logs -f```
- Stop server: ```docker compose down```

### docker-compose.yml

```yaml
services:
  minecraft:
    image: itzg/minecraft-server
    container_name: mc-server
    ports:
      - "25565:25565"
    environment:
      EULA: "TRUE"
      MEMORY: "4G"

    volumes:
      - ./data/minecraft-data:/data
    restart: unless-stopped
```

### Miljøvariabler
https://docker-minecraft-server.readthedocs.io/en/latest/configuration/server-properties/

