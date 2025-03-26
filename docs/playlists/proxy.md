---
title: Proxying Playlist
layout: home
parent: Playlist
---

# Proxy Playlists

## 🔄 Using built in proxy

Edit your desired playlist, or create one.

1. Playlists > Create/Edit > Step 5
2. Toggle the "Enable Proxy" option, save changes
3. When your playlist is generated, all channel URLs for the selecte playlist will have URLs pointing to **m3u editor**, which will play and re-stream the content
  - Useful if you are restricted by client as **m3u editor** will act as the client, making it appear as if only one client/ip is accessing your playlist

### 🔎 Debugging

To debug, you can add the `FFMPEG_DEBUG` environment variable to have the `ffmpeg` process pipe output to `/var/www/storage/logs/ffmpeg.log` in the container. This is only recommended to troubleshoot as the log file can grow quickly.

Example docker compose addition:

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - FFMPEG_DEBUG=true
```

Or via cli: `docker run --name m3u-editor -e FFMPEG_DEBUG=true ...`


## 📡 Creating a playlist proxy

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