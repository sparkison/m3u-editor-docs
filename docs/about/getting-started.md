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
      - APP_URL=http://localhost # or http://192.168.0.123 or https://your-custom-tld.com
      # This is used for websockets and in-app notifications
      # Set to your machine/container IP where m3u editor will be accessed, if not localhost
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
 docker run --name m3u-editor -e TZ=Etc/UTC -e APP_URL=http://localhost -e REVERB_HOST=localhost -e REVERB_SCHEME=http -v ./data:/var/www/config --restart unless-stopped -p 36400:36400 -p 36800:36800 sparkison/m3u-editor:latest 
```

Access via: [http://localhost:36400](http://localhost:36400) (user = admin, password = admin)

To ensure the data is saved across builds, link an empty volume to: `/var/www/config` within the container. This is where the `env` file will be stored, along with the sqlite database and the application log files.

Note about websocket
{: .label .label-purple }

> You will need to set the `REVERB_HOST` variable to the machine IP where **m3u editor** is runing for websockets to work correctly, if not running on `localhost`. 

---

## 🐳 Docker compose with 🐬 MySQL

By default **m3u editor** uses **SQLite** as the database driver. If you'd like something more resilient, you can use **MySQL** instead. m3u editor supports this out of the box!

Update your `docker-compose.yaml` file like this to use **MySQL**:

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - TZ=Etc/UTC
      - APP_URL=http://localhost # or http://192.168.0.123 or https://your-custom-tld.com
      # This is used for websockets and in-app notifications
      # Set to your machine/container IP where m3u editor will be accessed, if not localhost
      - REVERB_HOST=localhost # or 192.168.0.123 or your-custom-tld.com
      - REVERB_SCHEME=http # or https if using custom TLD with https
      - DB_CONNECTION=mysql
      - DB_HOST=m3ue-mysql
      - DB_PORT=3306
      - DB_DATABASE=m3ue
      - DB_USERNAME=m3ue
      - DB_PASSWORD="y@J@U3d0!0n9" # <--- update to a custom password, or use ENV
    volumes:
      # This will allow you to reuse the data across container recreates
      # Format is: <host_directory_path>:<container_path>
      # More information: https://docs.docker.com/reference/compose-file/volumes/
      - ./data:/var/www/config
    restart: unless-stopped
    ports:
      - 36400:36400 # app
      - 36800:36800 # websockets/broadcasting
    depends_on:
      m3ue-mysql:
        condition: service_healthy

  m3ue-mysql:
    image: mysql:8.0
    environment:
      - MYSQL_ROOT_PASSWORD="y@J@U3d0!0n9" # <--- update to a custom password, or use ENV
      - MYSQL_DATABASE=m3ue
      - MYSQL_USER=m3ue
      - MYSQL_PASSWORD="y@J@U3d0!0n9" # <--- update to a custom password, or use ENV
    volumes:
      - ./data/mysql:/var/lib/mysql
    restart: always
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5

networks: {}
```