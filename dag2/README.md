# Dag 2 - Minecraft server

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

### Optimaliseringer

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
      TYPE: "PAPER" # PaperMC optimalisert motor
      ENABLE_RCON: "true"
      RCON_PASSWORD: "your_strong_rcon_password" # CHANGE THIS!

    volumes:
      - ./data:/data
    restart: unless-stopped
```

### Plugins

https://www.spigotmc.org/resources/dynmap%C2%AE.274/

Opprett en plugins mappe på serveren:
```mkdir -p ~/minecraft/data/plugins```

Last ned plugin JAR filen og last den opp til serveren:
```scp plugin.jar brukernavn@ip:/home/brukernavn/minecraft/plugins```

Oppdater docker-compose.yml:

```yaml
services:
  minecraft:
    image: itzg/minecraft-server
    container_name: mc-server
    ports:
      - "25565:25565"
      - "8100:8100" #Tilgang til dynmap plugin
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

Start serveren på nytt:
```docker compose restart```

