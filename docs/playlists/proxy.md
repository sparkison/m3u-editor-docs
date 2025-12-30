---
title: Proxying Playlist
layout: home
parent: Playlist
---

# Proxy Playlists

## 🔄 Using built in proxy
{: .d-inline-block }

Only available in **m3u editor v0.8.0+**
{: .d-inline-block .v-align-text-bottom .label .label-purple }

**m3u editor** includes powerful stream proxying capabilities through m3u-proxy. This allows you to proxy all stream URLs through your server, which is useful for:

- **Connection Management**: Avoid "max connections exceeded" errors from your provider
- **Stream Pooling**: Multiple users can share a single provider connection
- **IP Masking**: All requests appear to come from your server
- **Transcoding**: Convert streams to HLS format (external proxy only)

### Enabling Proxy for a Playlist

1. Navigate to **Playlists** ➡️ **Create/Edit** ➡️ **Step 5**
2. Toggle the **Enable Proxy** option
3. Save changes

When enabled, all channel URLs in the playlist will point to **m3u editor**, which will proxy the stream content.

### Enable/Disable dynamically

You can also utilize the proxy "on the fly" via URL variables.

Adding the `?proxy=` to the end of your playlist with `true` or `false`, which will enable or disable the proxy functionality dynamically.

To override the Playlist proxy settings, append `?proxy=true`, or `?proxy=false`, to your playlist url, e.g.: _http://localhost:36400/9e6a2337-e48b-404e-bec4-787473b5a4b8/playlist.m3u**?proxy=false**_

---

## 🏗️ Proxy Architecture Options

### Embedded Proxy (Default)

The embedded proxy runs inside the m3u-editor container and is enabled by default (`M3U_PROXY_ENABLED=true`).

**Pros:**
- Simple setup - no additional containers needed
- Good for basic proxying needs

**Cons:**
- No Redis-based stream pooling
- No hardware acceleration support
- Shares resources with the main application

### External Proxy (Recommended)
{: .d-inline-block }

Recommended
{: .d-inline-block .v-align-text-bottom .label .label-green }

Running m3u-proxy as a separate container provides better performance and more features.

**Pros:**
- **Stream Pooling**: Multiple clients share one provider connection via Redis
- **Hardware Acceleration**: GPU transcoding support (VAAPI, NVENC)
- **Better Performance**: Dedicated resources for streaming
- **Independent Scaling**: Scale proxy independently from the main app

**Configuration:**
```yaml
# In m3u-editor environment
- M3U_PROXY_ENABLED=false
- M3U_PROXY_HOST=m3u-proxy  # Container name
- M3U_PROXY_PORT=38085
- M3U_PROXY_TOKEN=your-secure-token
```

See the [Getting Started]({% link docs/about/getting-started.md %}) guide for complete docker-compose examples.

---

## 🔄 Stream Pooling
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

Stream pooling allows multiple clients to share a single transcoded stream from m3u-proxy without consuming additional provider connections.

### How It Works

**Without Pooling:**
```
User 1 → m3u-editor → m3u-proxy → Provider Connection 1
User 2 → m3u-editor → m3u-proxy → Provider Connection 2 ❌ (REJECTED if limit=1)
```

**With Pooling (Enabled):**
```
User 1 → m3u-editor → m3u-proxy → Provider Connection 1
User 2 → m3u-editor → m3u-proxy → Same Connection ✅
User 3 → m3u-editor → m3u-proxy → Same Connection ✅
```

### Requirements for Stream Pooling

- External m3u-proxy setup
- Redis enabled (`REDIS_ENABLED=true` on m3u-proxy)
- `ENABLE_REDIS_POOLING=true` on m3u-proxy

---

## 🎬 HLS Storage Configuration

When using the proxy with transcoding, HLS segments are temporarily stored. Configure storage behavior with these environment variables:

| Variable | Default | Description |
|:---------|:--------|:------------|
| `HLS_TEMP_DIR` | `/tmp/hls` | Directory for HLS segments |
| `HLS_GC_ENABLED` | `true` | Enable garbage collection |
| `HLS_GC_INTERVAL` | `60` | GC interval in seconds |
| `HLS_GC_AGE_THRESHOLD` | `300` | Delete segments older than this (seconds) |

For high-traffic setups, consider mounting `HLS_TEMP_DIR` to a fast storage volume (SSD/RAM disk).

---

## 🖥️ Hardware Acceleration

Hardware acceleration for transcoding is only available with the **external proxy** setup.

### VAAPI (Intel/AMD)

```yaml
m3u-proxy:
  image: sparkison/m3u-proxy:latest
  devices:
    - /dev/dri:/dev/dri
```

### NVIDIA (NVENC)

```yaml
m3u-proxy:
  image: sparkison/m3u-proxy:latest
  runtime: nvidia
  environment:
    - NVIDIA_VISIBLE_DEVICES=all
```

---

## 🐛 Debugging

To debug proxy issues, add the `M3U_PROXY_LOG_LEVEL` environment variable to have the `m3u-proxy` process pipe output to `/var/www/html/storage/logs/m3u-proxy.log` in the container.

**⚠️ Warning**: Only enable for troubleshooting as the log file can grow quickly.

Example docker compose addition:

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - M3U_PROXY_LOG_LEVEL=info 
    ...
```

Log levels: `DEBUG`, `INFO`, `WARN`, `ERROR`

For external proxy, set `LOG_LEVEL` on the m3u-proxy container:

```yaml
m3u-proxy:
  environment:
    - LOG_LEVEL=DEBUG
```

---

## 📡 Using MediaFlow Proxy

While **m3u editor** has a built in proxy option, using MediaFlow Proxy has a few advanatges, such as:

- Convert MPEG-DASH streams (DRM-protected and non-protected) to HLS
- Support for non-DRM protected DASH live and VOD streams
- Proxy and modify HLS (M3U8) streams in real-time
- Proxy HTTP/HTTPS links with custom headers

If you don't need any of the above, the built-in proxy will likely work fine for you.
However, if you have more advanced needs, such as DRM, then MediaFlow Proxy is a great option.

Using the [MediaFlow Proxy](https://github.com/mhdzumair/mediaflow-proxy) setup as an example:

```yaml
services:
  mediaflow-proxy:
    image: mhdzumair/mediaflow-proxy
    environment:
      - API_PASSWORD=YOUR_PROXY_API_PASSWORD
    ports:
      - 8888:8888
networks: {}
```

Your proxied m3u playlist can then be access via: [http://localhost:8888/proxy/hls/manifest.m3u8?d=YOUR_M3U_EDITOR_PLAYLIST_URL&api_password=YOUR_PROXY_API_PASSWORD](http://localhost:8888/proxy/hls/manifest.m3u8?d=YOUR_M3U_EDITOR_PLAYLIST_URL&api_password=YOUR_PROXY_API_PASSWORD)

This is assuming the default **MediaFlow Proxy** setup, which exposes the `http://localhost:8888` URL, make sure to change as needed. 

Replace `YOUR_PROXY_API_PASSWORD` with a secure password, and change the `YOUR_M3U_EDITOR_PLAYLIST_URL` string to your Playlist URL from **m3u editor** (can be found via the URLs in the Playlist edit screen Step 1, e.g.: something like: `http://localhost:36400/9e6a2337-e48b-404e-bec4-787473b5a4b8/playlist.m3u`).

More setup information can be found on the [MediaFlow Proxy](https://github.com/mhdzumair/mediaflow-proxy) page.