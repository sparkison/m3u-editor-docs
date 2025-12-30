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
| `AUTO_LOGIN`  | `true` or `false` | `false`       | enable auto-login functionality       |
| `LOGIN_PATH`  | string | `login`       | the path used to login to the app       |
| `REDIRECT_GUEST_TO_LOGIN`  | `true` or `false` | `true`       | redirect the base domain to the login page if not logged in       |
| `APP_URL`    | fully qualified domain name | `http://localhost`       | url or IP address where app is being hosted, **including http** |
| `APP_PORT`    | valid port number | `36400` | the port to run the app on |
| `REVERB_PORT`    | valid port number | `36800`       | port used to access websocket server |
| `REVERB_VERIFY`    | `true` or `false` | `true`       | set to `false` to disable SSL verification. Set to `true` (default) to enable |
| `REDIS_HOST`    | valid hostname | `localhost`       | default uses container instance
| `REDIS_SERVER_PORT`    | valid port number	 | `36790`       | default uses container instance |
| `PROXY_URL_OVERRIDE`    | fully qualified domain name | `null`       | url or IP address where app is being hosted, **including http**. If null or not set, will use `APP_URL` |
| `MAX_CHANNELS`    | `int` | `50000`       | the maximum number of channels to import for m3u playlists (does not apply to Xtream API playlists) |
| `DISABLE_SYNC_LOGS`    | `bool` | `false`       | disable the creation of sync logs for playlists. Can be useful with larger lists using SQLite database |
| `INVALIDATE_IMPORT`    | `bool` | `false`       | whether to invalidate Playlist sync if conditon met (see `INVALIDATE_IMPORT_THRESHOLD`) |
| `INVALIDATE_IMPORT_THRESHOLD`    | `int` | `100`       | if the current sync will have less channels than the current channel count (less this value), the sync will be invalidated and canceled (when `INVALIDATE_IMPORT` is `true`) |
| `DISABLE_M3U_XTREAM_FORMAT`    | `true` or `false` | `false`       | by default, all urls will use the Xtream API format so additional stream analysis, check for stream limits, etc. can be performed. Disable to return the provider url (or proxyfied url) instead for M3U playlists. |
| `ENABLE_POSTGRES`    | `true` or `false` | `false`       | set to `true` to enable the internal PostgreSQL instance |
| `PG_DATABASE`    | string | `m3ue`       | enter a valid database name, e.g. "m3ue" |
| `PG_USER`    | string | `root`       | user to create/update for `PG_DATABASE` |
| `PG_PASSWORD`    | string | `root`       | password to set/update for `PG_USER` |
| `PG_PORT`    | `int` | `5432`       | any valid port, does not need to be exposed |
| `DB_CONNECTION`    | `sqlite` or `pgsql` | `sqlite`       | set to `pgsql` to use PostgreSQL  |
| `DB_HOST`    | valid hostname | `null`       | valid hostname for PostgreSQL. If `ENABLE_POSTGRES` is `true` you can use `localhost` |
| `DB_PORT`    | `int` | `null`       | valid port number for PostgreSQL. If `ENABLE_POSTGRES` is `true` you can use `PG_PORT`   |
| `DB_DATABASE`    | string | `null`       | valid database name for PostgreSQL. If `ENABLE_POSTGRES` is `true` you can use `PG_DATABASE`   |
| `DB_USERNAME`    | string | `null`       | valid database user for PostgreSQL. If `ENABLE_POSTGRES` is `true` you can use `PG_USER`   |
| `DB_PASSWORD`    | string | `null`       | valid password for PostgreSQL. If `ENABLE_POSTGRES` is `true` you can use `PG_PASSWORD`   |

## 🔄 M3U Proxy Variables
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

These variables configure the m3u-proxy functionality for stream proxying and transcoding.

| Variable Name| Accepted Values/Types   | Default Value | Description |
|:-------------|:------------------|:--------------|:--------------|
| `M3U_PROXY_ENABLED`    | `true` or `false` | `true`       | `true` = use embedded proxy, `false` = use external m3u-proxy container |
| `M3U_PROXY_HOST`    | valid hostname | `localhost`       | hostname of the m3u-proxy service (use container name for external, e.g., `m3u-proxy`) |
| `M3U_PROXY_PORT`    | valid port number | `8085`       | port the m3u-proxy service runs on (default `38085` for external) |
| `M3U_PROXY_TOKEN`    | string | auto-generated       | API authentication token for m3u-proxy (must match `API_TOKEN` on external proxy) |
| `M3U_PROXY_LOG_LEVEL`    | `DEBUG`, `INFO`, `WARN`, `ERROR` | `null`       | enable logging for m3u-proxy (logs to `/var/www/html/storage/logs/m3u-proxy.log`) |

## 📺 HLS Storage Variables
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

Configure HLS segment storage and garbage collection for proxied streams.

| Variable Name| Accepted Values/Types   | Default Value | Description |
|:-------------|:------------------|:--------------|:--------------|
| `HLS_TEMP_DIR`    | valid path | `/tmp/hls`       | directory for storing HLS segments |
| `HLS_GC_ENABLED`    | `true` or `false` | `true`       | enable garbage collection for old HLS segments |
| `HLS_GC_INTERVAL`    | `int` (seconds) | `60`       | how often to run garbage collection |
| `HLS_GC_AGE_THRESHOLD`    | `int` (seconds) | `300`       | delete HLS segments older than this value |

## 🌐 Web Server Variables
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

Configure the embedded web server.

| Variable Name| Accepted Values/Types   | Default Value | Description |
|:-------------|:------------------|:--------------|:--------------|
| `NGINX_ENABLED`    | `true` or `false` | `true`       | enable/disable embedded Nginx (set to `false` when using external web server) |
| `FPMPORT`    | valid port number | `9000`       | PHP-FPM port for external web server integration |
| `REDIS_ENABLED`    | `true` or `false` | `true`       | enable/disable embedded Redis (set to `false` when using external Redis) |

## 📁 Xtream & STRM Variables
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

Configure Xtream API and STRM file generation.

| Variable Name| Accepted Values/Types   | Default Value | Description |
|:-------------|:------------------|:--------------|:--------------|
| `XTREAM_SERIES_FOLDER`    | string | `Series`       | folder name for series content in STRM generation |
| `XTREAM_MOVIE_FOLDER`    | string | `Movies`       | folder name for movie content in STRM generation |
| `XTREAM_STRM_FOLDER`    | string | `strm`       | output folder for generated STRM files |

## 🖼️ Logo & Cache Variables

| Variable Name| Accepted Values/Types   | Default Value | Description |
|:-------------|:------------------|:--------------|:--------------|
| `LOGO_CACHE_EXPIRY_DAYS`    | `int` | `30`       | number of days to cache channel logos before refreshing |

## 🐛 Debug Variables

| Variable Name| Accepted Values/Types   | Default Value | Description |
|:-------------|:------------------|:--------------|:--------------|
| `SHARED_STREAMING_DEBUG`    | `true` or `false` | `false`       | enable debug logging for shared streaming/pooling |


### Deprecated variables 

The following variables are not longer used in `v0.8.x` and up.

- The `PROXY_FFMPEG_*` variables are not used as m3u-proxy now handles all transcoding. Use an external m3u-proxy instance with hardware acceleration for transcoding needs.
- The `REVERB_*` keys are now automatically set to reflect your `APP_URL` and uses reverse proxy internally so additional ports do not need exposed. Because if this the scheme and host are set automatically.
- The `BROADCAST_CONNECTION` is always `reverb` (the built in websocket service). This now runs via local reverse proxy, so there is no need to expose additional ports (it is __not__ required to espose port `36800` any longer, the default websocket port) or adding reverse proxy, etc. Because of this, it is no longer possible to disable the websockets for in-app notifications and they are instead enabled by default.

| Variable Name| Accepted Values/Types   | Default Value | Description |
|:-------------|:------------------|:--------------|:--------------|
| `REVERB_SCHEME`    | `http` or `https` | `http`       | how to access websockets |
| `REVERB_HOST`    | valid hostname | `localhost`       | where the websocket server is running (may need to change to host IP) |
| `PROXY_FFMPEG_PATH`    | `ffmpeg` or `jellyfin-ffempg`, or valid path to executable | `null`       | valid path to executable if not `ffmpeg` or `jellyfin-ffempg` for local instance |
| `PROXY_FFMPEG_ADDITIONAL_ARGS`    | valid `ffmpeg` input args | `null`       | arguments will be added _before_ the input source |
| `PROXY_FFMPEG_CODEC_VIDEO`    | valid video codec | `null`       | valid ffmpeg video codec, e.g. "h265" |
| `PROXY_FFMPEG_CODEC_AUDIO`    | valid audio codec | `null`       | valid ffmpeg audio codec, e.g. "aac" |
| `PROXY_FFMPEG_CODEC_SUBTITLES`    | valid subtitle codec | `null`       | valid ffmpeg subtitle codec, e.g. "srt" |
| `BROADCAST_CONNECTION`    | `reverb` or `null` | `reverb`       | set to `null` to disable websockets server. Set to `reverb` (default) to enable |
