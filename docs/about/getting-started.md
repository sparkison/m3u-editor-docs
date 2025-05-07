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

## 🐬 Docker compose with 💪 MySQL
{: .d-inline-block }

New (v0.6.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

By default **m3u editor** uses **SQLite** as the database driver. If you'd like something more resilient, you can use **MySQL** instead. **m3u editor** supports this out of the box!

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
      - ENABLE_MYSQL=true                 # <----- start here
      - MYSQL_DATABASE=${MYSQL_DATABASE}  # <----- DB name
      - MYSQL_USER=${MYSQL_USER}          # <----- DB user
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}  # <----- DB password
      - DB_CONNECTION=mysql               # <----- set to `mysql` (default is `sqlite`)
      - DB_HOST=localhost                 # <----- make sure set to `localhost`
      - DB_PORT=3306                      # <----- using default MySQL port
      - DB_DATABASE=${MYSQL_DATABASE}     # <----- should match `MYSQL_DATABASE`
      - DB_USERNAME=${MYSQL_USER}         # <----- should match `MYSQL_USER`
      - DB_PASSWORD=${MYSQL_PASSWORD}     # <----- should match `MYSQL_PASSWORD`
    volumes:
      # This will allow you to reuse the data across container recreates
      # Format is: <host_directory_path>:<container_path>
      # More information: https://docs.docker.com/reference/compose-file/volumes/
      - ./data:/var/www/config
      - ./data/mysql:/var/lib/mysql # <----- link `mysql` directory to retain data
    restart: unless-stopped
    ports:
      - 36400:36400 # app
      - 36800:36800 # websockets/broadcasting
      # - 36306:3306  # <----- (optionally) expose the MySQL instance
networks: {}
```

then make sure to add `.env` variables in the root of project (where your `docker-compose.yaml` lives):

```
MYSQL_DATABASE=m3ue
MYSQL_USER=m3ue
MYSQL_PASSWORD=secret
```