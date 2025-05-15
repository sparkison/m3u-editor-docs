---
title: Environment Variables
layout: home
nav_order: 5
---

# Environment Variables

Environment variables can be used in your `docker-compose.yaml` file under the `environment` key, or via the CLI using the `-e` flag, to alter the functionality and output of the app.

Docker compose example:
```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - VARIABLE_NAME=value
    ...
```

or via CLI: `docker run --name m3u-editor -e VARIABLE_NAME=value ...`

## 🛠️ Variables

| Variable Name| Accepted Values/Types   | Default Value | Description |
|:-------------|:------------------|:--------------|:--------------|
| `PUID`  | `int` | `1000`       | user ID to run under (not currently implemented)       |
| `PGID`  | `int` | `1000`       | group ID to run under (not currently implemented)       |
| `APP_DEBUG`  | `true` or `false` | `false`       | output additional info to the log file       |
| `APP_URL`    | fully qualified domain name | `http://localhost`       | url or IP address where app is being hosted, **including http** |
| `APP_PORT`    | valid port number | `36400` | the port to run the app on |
| `REVERB_SCHEME`    | `http` or `https` | `http`       | how to access websockets |
| `REVERB_HOST`    | valid hostname | `localhost`       | where the websocket server is running (may need to change to host IP) |
| `REVERB_PORT`    | valid port number | `36800`       | port used to access websocket server |
| `REVERB_VERIFY`    | `true` or `false` | `true`       | set to `false` to disable SSL verification. Set to `true` (default) to enable |
| `REDIS_HOST`    | valid hostname | `localhost`       | default uses container instance
| `REDIS_SERVER_PORT`    | valid port number	 | `36790`       | default uses container instance |
| `PROXY_URL_OVERRIDE`    | fully qualified domain name | `null`       | url or IP address where app is being hosted, **including http**. If null or not set, will use `APP_URL` |
| `MAX_CHANNELS`    | `int` | `50000`       | the maximum number of channels to import for m3u playlists (does not apply to Xtream API playlists) |
| `BROADCAST_CONNECTION`    | `reverb` or `null` | `reverb`       | set to `null` to disable websockets server. Set to `reverb` (default) to enable |
| `ENABLE_POSTGRES`    | `true` or `false` | `false`       | set to `true` to enable the internal PostgreSQL instance |
| `PG_DATABASE`    | string | `m3ue`       | enter a valid database name, e.g. "m3ue" |
| `PG_USER`    | string | `root`       | user to create/update for `PG_DATABASE` |
| `PG_PASSWORD`    | string | `root`       | password to set/udpate for `PG_USER` |
| `PG_PORT`    | `int` | `5432`       | any valid port, does not need to be exposed |
| `DB_CONNECTION`    | `sqlite` or `pgsql` | `sqlite`       | set to `pgsql` to use PostgreSQL  |
| `DB_HOST`    | valid hostname | `null`       | valid hostname for PostgreSQL. If `ENABLE_POSTGRES` is `true` you can use `localhost` |
| `DB_PORT`    | `int` | `null`       | valid port number for PostgreSQL. If `ENABLE_POSTGRES` is `true` you can use `PG_PORT`   |
| `DB_DATABASE`    | string | `null`       | valid database name for PostgreSQL. If `ENABLE_POSTGRES` is `true` you can use `PG_DATABASE`   |
| `DB_USERNAME`    | string | `null`       | valid database user for PostgreSQL. If `ENABLE_POSTGRES` is `true` you can use `PG_USER`   |
| `DB_PASSWORD`    | string | `null`       | valid password for PostgreSQL. If `ENABLE_POSTGRES` is `true` you can use `PG_PASSWORD`   |
