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

| Variable Name| Accepted Values   | Default Value | Description |
|:-------------|:------------------|:--------------|:--------------|
| `PUID`  | `int` | `1000`       | user ID to run under (not currently implemented)       |
| `PGID`  | `int` | `1000`       | group ID to run under (not currently implemented)       |
| `APP_DEBUG`  | `true` or `false` | `false`       | output additional info to the log file       |
| `APP_URL`    | fully qualified domain name | `http://localhost`       | url or IP address where app is being hosted, **including http** |
| `APP_PORT`    | valid port number | `36400` | the port to run the app on |
| `REVERB_SCHEME`    | `http` or `https` | `http`       | how to access websockets |
| `REVERB_HOST`    | valid hostname | `localhost`       | where the websocket server is running (may need to change to host IP) |
| `REVERB_PORT`    | valid port number | `36800`       | port used to access websocket server |
| `REDIS_HOST`    | valid hostname | `localhost`       | default uses container instance
| `REDIS_SERVER_PORT`    | valid port number	 | `36790`       | default uses container instance |
| `FFMPEG_DEBUG`  | `true` or `false` | `false`       | output `ffmpeg` results to `/var/www/storage/logs/ffmpeg.log` for [proxy]({% link docs/playlists/proxy.md %}) debugging       |
| `PROXY_URL_OVERRIDE`    | fully qualified domain name | `null`       | url or IP address where app is being hosted, **including http**. If null or not set, will use `APP_URL` |
| `LOG_VIEWER_API_STATEFUL_DOMAINS`    | fully qualified domain names (comma seperated list) | `null`       | if using a tld (e.g.: https://mysite.com) to run the app, will need to add here as well to access **/logs** view |
| `MAX_CHANNELS`    | `int` | `50000`       | the maximum number of channels to import for m3u playlists (does not apply to Xtream API playlists) |


