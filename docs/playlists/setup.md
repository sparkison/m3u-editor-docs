---
title: Managing Playlist
layout: home
parent: Playlist
---

# Managing Playlists

## Playlist configuration options

### Step 1 (Name)

- Name
    - Enter your playlist name, which is used throughout the app to reference.

- Links
    - Once playlist is created, an additional "Links" section will be displayed, allowing you to copy/view the URLs for your generated **Playlist m3u** file, **EPG xml** and an **HDHR** link if you would like to use the Playlist as an HDHomeRun tuner.

### Step 2 (Type)

Select your provider playlist type, options are:

- m3u8 or local file
    - You can enter the full playlist URL from your provider
    - Or if file is stored locally, provide the full path to the file
    - Or if you have a file you would like to upload, upload it here
- Xtream API
    - Enter your Xtream API credentials
    - Select your desired output (MPEG-TS or HLS)
    - Include VOD, if desired (select the checkbox for "VOD" under the "Additional categories & streams to import" field)
- User agent
    - You can use the default, or enter a custom user agent (used to fetch and sync your playlist - not used for local or uploaded files)
- Disable SSL verification
    - Not recommended - disable to troubleshoot issues connecting to your provider for downloading/syncing of playlist

### Step 3 (Scheduling)

- Automatically sync playlist
    - Toggle to enable/disable automatic syncing of the playlist
- Sync Every (when **Automatically sync playlist** enabled)
    - The interval at which to run the sync.
- Last Synced (when **Automatically sync playlist** enabled)
    - Autopopulated field that will have the date of the last sync. Can be changed to force a sync at a specific time, e.g.: set **Sync Every** to 8hr, and then update the **Last Synced** field to 6hrs in the past to set the sync 2hr from now.

### Step 4 (Processing)

- Preprocess playlist
    - Toggle to enable/disable preprocessing of the playlist
- Use regex for filtering (when **Preprocess playlist** enabled)
    - **Group prefixes to import** will be used as a Regex instead
- Groups to import (when **Preprocess playlist** enabled)
    - 🚨 NOTE: If Playlist has not been processed previously, or is new, you will need to run an initial sync to fetch the groups, otherwise this list will be empty. Please sync your playlist first (by completng its creation) and come back to this step to select the discovered groups.
    - Groups to include in the playlist import/sync
- Group prefixes to import (when **Preprocess playlist** enabled)
    - Custom strings to include in the  import, e.g.: `US -` to include all groups starting with `US -`, and ignoring others
    - Using regex: when **Use regex for filtering** is enabled, you can use regex instead, e.g.: `^(US|UK|CA)` to include all groups starting with US, UK or CA.
- Enable new channels
    - When enabled, all newly imported channels will be enabled by default.
- Ignored file types
    - File types to ignore, for example `.mp4`, `.mkv`, etc. Useful when you have a playlist with a lot of series or VOD content you don't wish to include.

### Step 5 (Output)

- Automatically assign sort number based on playlist order
    - When enabled, the sort order will be assigned to the imported channels based on the order of the original playlist.
- Auto channel number increment
    - If no channel number is set on the provider list, output an automatically incrementing number. Custom channel numbers will still be respected if set.
- Channel start (when **Auto channel number increment** enabled)
    -  The starting channel number, e.g.: `1000`, then any channel without a channel number set will start at 1000 and count up by one for each channel without an assigned channel number.
- Streams
    - Used to define the number of streams available, currently only used for **HDHomeRun** device, if not using HDHR feature, you can set this to any value.
- Enable Proxy
    - Use **m3u editor** built in [proxy functionality]({% link docs/playlists/proxy.md %})

### Step 6 (Advanced Options)
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

#### Auto-Merge Channels

Auto-merge allows you to automatically combine channels with the same stream ID into a single master channel with failover support.

- **Auto-merge channels after sync**
    - When enabled, channels with identical stream IDs are automatically merged after playlist sync
- **Deactivate failover channels**
    - Automatically disable channels that are configured as failovers
- **Prioritize by resolution**
    - Use stream resolution to determine which channel becomes the master
    - ⚠️ **Warning**: This requires analyzing each stream, which may cause rate limiting with some IPTV providers. Only enable when necessary.
- **Force complete re-merge**
    - Reprocess all channels instead of just new ones

**How Master Channel Selection Works:**

Without resolution check (Recommended for IPTV):
1. If a "Preferred Playlist" is selected: First channel from that playlist becomes master
2. Otherwise: First channel based on playlist priority becomes master

With resolution check:
1. Channels are analyzed to determine resolution
2. Channel with highest resolution becomes master
3. Remaining channels become failovers, sorted by resolution

#### Connection Limits

Configure maximum simultaneous connections for your playlist:

- **Stream limit**
    - Maximum number of concurrent connections allowed
    - Useful when your provider restricts simultaneous streams

---

## Output URL Formats

Your playlist can be accessed in multiple formats:

| Format | URL Pattern | Use Case |
|:-------|:------------|:---------|
| M3U Playlist | `/{uuid}/playlist.m3u` | Standard M3U players |
| EPG XML | `/{uuid}/epg.xml` | EPG-enabled players |
| HDHomeRun | `/{uuid}/discover.json` | Plex, Jellyfin, Emby |
| Xtream API | `/player_api.php?username=X&password=Y` | IPTV apps |

All URLs can be found in **Step 1** of the playlist editor after creation.