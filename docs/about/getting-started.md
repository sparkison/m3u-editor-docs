---
title: Getting Started
layout: home
nav_order: 2
---

# Getting started

## 🐳 Docker compose

Use the following compose example to get up and running.

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      # This will allow you to reuse the data across container recreates.
      # Format is: <host_directory_path>:<container_path>
      # More information: https://docs.docker.com/reference/compose-file/volumes/
      - ./data:/var/www/config
    restart: unless-stopped
    ports:
      - 36400:36400 # app
      - 36800:36800 # websockets/broadcasting
networks: {}
```

Or via Docker CLI:

```bash
 docker run --name m3u-editor -e PUID=1000 -e PGID=1000 -e TZ=Etc/UTC -v ./data:/var/www/config --restart unless-stopped -p 36400:36400 -p 36800:36800 sparkison/m3u-editor:latest 
```

Access via: [http://localhost:36400](http://localhost:36400) (user = admin, password = admin)

To ensure the data is saved across builds, link an empty volume to: `/var/www/config` within the container. This is where the `env` file will be stored, along with the sqlite database and the application log files.

### Note about websocket
Once built, head to the `env` file with your linked config directory and make sure the `REVERB_HOST` is properly set to the machine IP (if not `localhost`) for websockets and broadcasting to work correctly. This is not required for the app to function, but you will not receive real-time in-app notifications without it. You will need to restart the container after changing this.
