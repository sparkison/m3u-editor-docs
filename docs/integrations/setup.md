---
title: Setup
layout: home
parent: Integrations
nav_order: 0
---

# Integration Setup

## Prerequisites

Before integrating with media servers:

1. Create and configure your playlist in **m3u editor**
2. Note your playlist URLs from **Playlists** ➡️ **Edit** ➡️ **Step 1 (Links)**
3. Ensure **m3u editor** is accessible from your media server

---

## Jellyfin
{: #jellyfin }

### HDHomeRun Tuner Setup

1. In Jellyfin, go to **Dashboard** ➡️ **Live TV**
2. Click **Add** under "Tuner Devices"
3. Select **HD Homerun** as the tuner type
4. Enter your **m3u editor** HDHomeRun URL:
   ```
   http://YOUR_M3U_EDITOR_IP:36400/{playlist-uuid}/discover.json
   ```
5. Click **Save**

### EPG Setup

1. Go to **Dashboard** ➡️ **Live TV** ➡️ **TV Guide Data Providers**
2. Click **Add**
3. Select **XMLTV**
4. Enter your EPG URL:
   ```
   http://YOUR_M3U_EDITOR_IP:36400/{playlist-uuid}/epg.xml
   ```
5. Click **Save**
6. Refresh guide data

### Docker Compose Example

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    # ... your m3u-editor config ...
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:36400/up"]
      interval: 10s
      timeout: 10s
      retries: 10
      start_period: 60s

  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    depends_on:
      m3u-editor:
        condition: service_healthy
    ports:
      - 8096:8096
    volumes:
      - ./jellyfin/config:/config
      - ./jellyfin/cache:/cache
    restart: unless-stopped
```

---

## Plex
{: #plex }

### HDHomeRun Tuner Setup

1. In Plex, go to **Settings** ➡️ **Live TV & DVR**
2. Click **Set Up Plex DVR**
3. Plex should auto-discover your HDHomeRun device, or you can manually enter:
   ```
   http://YOUR_M3U_EDITOR_IP:36400/{playlist-uuid}/discover.json
   ```
4. Follow the setup wizard to configure channels

### EPG Setup

Plex handles EPG through its own guide service or via the HDHomeRun integration. For custom EPG:

1. You may need to use tools like **xteve** as an intermediary
2. Or configure Plex to use the XMLTV guide from m3u editor

**Note**: Plex's Live TV integration works best with Plex Pass subscription.

---

## Emby
{: #emby }

### HDHomeRun Tuner Setup

1. In Emby, go to **Settings** ➡️ **Live TV**
2. Click **Add** under "TV Sources"
3. Select **HD Homerun**
4. Enter your m3u editor HDHomeRun URL:
   ```
   http://YOUR_M3U_EDITOR_IP:36400/{playlist-uuid}/discover.json
   ```
5. Click **Save**

### EPG Setup

1. Go to **Settings** ➡️ **Live TV** ➡️ **Guide Provider**
2. Click **Add Guide Provider**
3. Select **XMLTV**
4. Enter your EPG URL:
   ```
   http://YOUR_M3U_EDITOR_IP:36400/{playlist-uuid}/epg.xml
   ```
5. Save and refresh guide data

---

## IPTV Apps (Xtream API)
{: #iptv-apps }

For apps that support Xtream API (TiviMate, IPTV Smarters, OTT Navigator, etc.):

### Connection Details

| Field | Value |
|:------|:------|
| Server URL | `http://YOUR_M3U_EDITOR_IP:36400` |
| Username | Your playlist username |
| Password | Your playlist password |

**Note**: Username and password are configured in your playlist settings.

### TiviMate Setup

1. Open TiviMate
2. Select **Add Playlist**
3. Choose **Xtream Codes**
4. Enter:
   - **Server URL**: `http://YOUR_M3U_EDITOR_IP:36400`
   - **Username**: Your playlist username
   - **Password**: Your playlist password
5. Click **Next** and complete setup

### IPTV Smarters Setup

1. Open IPTV Smarters
2. Select **Add User** ➡️ **Login with Xtream Codes API**
3. Enter:
   - **Any Name**: Your playlist name
   - **Username**: Your playlist username
   - **Password**: Your playlist password
   - **URL**: `http://YOUR_M3U_EDITOR_IP:36400`
4. Click **Add User**

---

## Direct M3U Playlist

For players that support M3U directly (VLC, Kodi, etc.):

### Playlist URL
```
http://YOUR_M3U_EDITOR_IP:36400/{playlist-uuid}/playlist.m3u
```

### With Proxy Enabled
```
http://YOUR_M3U_EDITOR_IP:36400/{playlist-uuid}/playlist.m3u?proxy=true
```

### EPG URL
```
http://YOUR_M3U_EDITOR_IP:36400/{playlist-uuid}/epg.xml
```

---

## Troubleshooting

### Media server can't find HDHomeRun device

1. Ensure **m3u editor** is accessible from your media server
2. Check firewall rules allow port 36400
3. Try using the IP address instead of hostname
4. Verify the playlist UUID is correct

### Channels not showing EPG data

1. Ensure EPG is synced in **m3u editor**
2. Check EPG mapping in your playlist channels
3. Refresh guide data in your media server
4. Verify channel IDs match between playlist and EPG

### Streams buffering or failing

1. Enable proxy in **m3u editor** to handle connection limits
2. Check provider stream limits
3. Consider using external m3u-proxy with stream pooling