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
      - TZ=Etc/UTC
      # This is used for websockets and in-app notifications
      # Set to your machine/container IP where m3u editor will be accessed, if not localhost
      - APP_URL=http://localhost # or http://192.168.0.123 or https://your-custom-tld.com
      - REVERB_HOST=localhost # or 192.168.0.123 or your-custom-tld.com
      - REVERB_SCHEME=http # or https if using custom TLD with https
    volumes:
      # This will allow you to reuse the data across container recreates
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
 docker run --name m3u-editor -e PUID=1000 -e PGID=1000 -e TZ=Etc/UTC -e REVERB_HOST=localhost -v ./data:/var/www/config --restart unless-stopped -p 36400:36400 -p 36800:36800 sparkison/m3u-editor:latest 
```

Access via: [http://localhost:36400](http://localhost:36400) (user = admin, password = admin)

To ensure the data is saved across builds, link an empty volume to: `/var/www/config` within the container. This is where the `env` file will be stored, along with the sqlite database and the application log files.

Note about websocket
{: .label .label-purple }

> You will need to set the `REVERB_HOST` variable to the machine IP where **m3u editor** is runing for websockets to work correctly, if not running on `localhost`. 
