---
title: Integrations
layout: home
---

# Integrations

**m3u editor** can integrate with various media servers and IPTV applications.

## 📺 Media Server Integration

### HDHomeRun Emulation

**m3u editor** can emulate an HDHomeRun tuner device, making it compatible with:

- **Plex** (Live TV & DVR)
- **Jellyfin** (Live TV)
- **Emby** (Live TV)

Each playlist provides HDHomeRun-compatible endpoints:
- `/{uuid}/discover.json` - Device discovery
- `/{uuid}/lineup.json` - Channel lineup
- `/{uuid}/lineup_status.json` - Lineup status
- `/{uuid}/device.xml` - Device descriptor

### Xtream API Compatibility

For IPTV applications that support Xtream API:

- TiviMate
- IPTV Smarters
- OTT Navigator
- And many more...

**m3u editor** provides full Xtream API compatibility through:
- `/player_api.php` - Main API endpoint
- `/xmltv.php` - EPG endpoint
- Live, VOD, and Series stream endpoints

## 🔗 Output Formats

| Format | URL | Compatible With |
|:-------|:----|:----------------|
| M3U Playlist | `/{uuid}/playlist.m3u` | VLC, Kodi, most players |
| EPG (XMLTV) | `/{uuid}/epg.xml` | All EPG-enabled players |
| HDHomeRun | `/{uuid}/discover.json` | Plex, Jellyfin, Emby |
| Xtream API | `/player_api.php` | IPTV Smarters, TiviMate |

## 📚 Setup Guides

- [Jellyfin Setup]({% link docs/integrations/setup.md %}#jellyfin)
- [Plex Setup]({% link docs/integrations/setup.md %}#plex)
- [Emby Setup]({% link docs/integrations/setup.md %}#emby)
- [IPTV Apps (Xtream)]({% link docs/integrations/setup.md %}#iptv-apps)