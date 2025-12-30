---
title: Managing EPG
layout: home
parent: EPG
---

# Managing EPGs

## EPG configuration options

- Name
    - Enter your EPG name, which is used throughout the app to reference.
- Automatically sync EPG
    - Toggle to enable/disable automatic syncing of the EPG
- Sync Every (when **Automatically sync EPG** enabled)
    - The interval at which to run the sync.
- Last Synced (when **Automatically sync EPG** enabled)
    - Autopopulated field that will have the date of the last sync. Can be changed to force a sync at a specific time, e.g.: set **Sync Every** to 8hr, and then update the **Last Synced** field to 6hrs in the past to set the sync 2hr from now.
- XMLTV file or URL/file path
    - You can enter the full EPG (XMLTV) URL from your provider
    - Or if file is stored locally, provide the full path to the file
    - Or if you have a file you would like to upload, upload it here
- User agent
    - You can use the default, or enter a custom user agent (used to fetch and sync your playlist - not used for local or uploaded files)
- Disable SSL verification
    - Not recommended - disable to troubleshoot issues connecting to your provider for downloading/syncing of playlist
- Preferred Locale
    - Used for EPG mapping when no direct match is found for a channel. If multiple matching channels are found, will give priority to entered locale string, e.g. `en`

---

## 📡 EPG Source Types
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

**m3u editor** supports multiple EPG source types:

### XMLTV URL

The most common option. Provide a URL to an XMLTV file from your provider or a third-party EPG service.

### XMLTV File

Upload or specify a local path to an XMLTV file. Useful for:
- Custom EPG files you've created
- EPG files from providers that don't offer direct URLs
- Testing and development

### Schedules Direct
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

[Schedules Direct](https://www.schedulesdirect.org/) is a subscription-based EPG service that provides high-quality programme data for North American TV channels.

**Setup:**
1. Create a [Schedules Direct](https://www.schedulesdirect.org/) account
2. In **m3u editor**, create a new EPG and select **Schedules Direct** as the source type
3. Enter your Schedules Direct credentials
4. Select your lineup(s)
5. Save and sync

**Features:**
- High-quality programme metadata
- Series information with episode details
- Artwork and images (automatically proxied)
- Regular updates throughout the day

---

## ⚡ EPG Caching System
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

**m3u editor** includes a high-performance EPG caching system that dramatically improves performance for large EPG files.

### How It Works

- EPG data is parsed and stored in optimized JSON cache files
- Programme data is chunked by date for efficient access
- Cache is automatically generated after each EPG sync
- API requests use cache instead of parsing XML files each time

### Benefits

- **Instant Response**: Cache lookup instead of XML parsing
- **Reduced Memory**: Date-chunked storage allows partial loading
- **Better Performance**: Especially noticeable with large EPG files (100MB+)

### Manual Cache Management

Generate cache manually:
```bash
docker exec -it m3u-editor php artisan app:epg-cache-generate {uuid}
```

Check cache health:
```bash
docker exec -it m3u-editor php artisan app:epg-cache-health-check
```

Clear playlist EPG files:
```bash
docker exec -it m3u-editor php artisan app:clear-playlist-epg-files
```

### Cache API Endpoints

Access cached EPG data via API:

```sh
# Get EPG data for specific EPG
GET /api/epg/{uuid}/data?page=1&per_page=50&start_date=2025-01-01

# Get EPG data for playlist channels
GET /api/epg/playlist/{uuid}/data?page=1&per_page=50
```

See the [API documentation]({% link docs/about/api.md %}) for more details.