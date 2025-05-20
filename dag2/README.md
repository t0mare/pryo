# Dag 2

## Installer Docker

https://docs.docker.com/engine/install/ubuntu/

## Minecraft Server
`services:
  minecraft:
    image: itzg/minecraft-server
    container_name: mc-pi-server
    ports:
      - "25565:25565"
    environment:
      EULA: "TRUE"
      MEMORY: "2G"          # Adjust for your Pi 5 (e.g., "4G", "6G")
      # TYPE: "PAPER"        # Optional: Uncomment to use PaperMC for better performance
      # VERSION: "LATEST"    # Optional: Specify Minecraft version
      # DIFFICULTY: "normal" # Optional
      # MODE: "survival"     # Optional
    volumes:
      - /home/your_user/minecraft-data:/data  # IMPORTANT: Change 'your_user' to your actual username
                                            # Or use an absolute path like '~/minecraft-data:/data' if running compose from home dir
    restart: unless-stopped`