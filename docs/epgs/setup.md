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