---
title: Importing Content
layout: home
parent: Emby and Jellyfin
---

# Importing VOD and Series Content

This guide covers how to import Movies (VOD) and TV Series from your Emby or Jellyfin media server into M3U Editor playlists.

## Prerequisites

Before you can import content, you must:

1. Complete the [initial setup]({% link docs/integrations/setup.md %}) to configure your Emby/Jellyfin connection
2. Create at least one playlist in M3U Editor to import content into
3. Ensure your media libraries are properly configured in Emby/Jellyfin

{: .note }
After the initial import into your playlists, additional syncs can be performed directly against the playlist to keep content up-to-date.

---

## Importing Movies (VOD)

Follow these steps to import movies from your Emby or Jellyfin library:

### Step 1: Navigate to VOD Channels

1. Go to **Channels & VOD → VOD Channels**
2. Click **Sync from Emby** (or **Sync from Jellyfin** depending on your server)

### Step 2: Select Source Library

1. In the **Emby Library** dropdown, select your **Movies** library
2. This will display all movies available in that library

### Step 3: Choose Target Playlist

1. In the **Playlist** dropdown, select the playlist you want to import the content into
2. This playlist must already exist in M3U Editor

### Step 4: Configure Import Options

Choose your desired VOD import options:

- **Use Direct File Paths**: When enabled, uses direct file paths instead of Emby/Jellyfin streaming URLs
  - Requires file system access from the M3U Editor server
  - Not commonly used unless you have specific requirements
  
- **Auto-enable Channels**: Automatically enables imported channels
  - When disabled, imported content will need to be manually enabled
  
- **Import Groups from Genres**: Creates groups based on Emby/Jellyfin genres
  - Uses the genre handling preference from Settings
  - Allows content to appear in multiple genre-based groups

### Step 5: Start Import

1. Click **Sync Now** to begin the import process
2. Wait for the sync to complete
3. Review the imported content in your selected playlist

---

## Importing TV Series

Follow these steps to import TV series from your Emby or Jellyfin library:

### Step 1: Navigate to Series Channels

1. Go to **Channels & VOD → Series Channels**
2. Click **Sync from Emby** (or **Sync from Jellyfin** depending on your server)

### Step 2: Select Source Library

1. In the **Emby Library** dropdown, select your **TV Shows** or **Series** library
2. This will display all series available in that library

### Step 3: Choose Target Playlist

1. In the **Playlist** dropdown, select the playlist you want to import the series into
2. This playlist must already exist in M3U Editor
3. **Important**: Select a playlist designated for series content

### Step 4: Configure Import Options

Choose your desired series import options:

- **Use Direct File Paths**: When enabled, uses direct file paths instead of Emby/Jellyfin streaming URLs
  - Requires file system access from the M3U Editor server
  - Not commonly used unless you have specific requirements
  
- **Auto-enable Channels**: Automatically enables imported series
  - When disabled, imported content will need to be manually enabled
  
- **Import Groups from Genres**: Creates categories based on Emby/Jellyfin genres
  - Uses the genre handling preference from Settings
  - Allows series to appear in multiple genre-based categories

### Step 5: Start Import

1. Click **Sync Now** to begin the import process
2. Wait for the sync to complete
3. Review the imported series in your selected playlist

---

## Post-Import Management

### Re-syncing Content

To sync updates from your media server:

1. Go to **Channels & VOD → VOD Channels** (or **Series Channels**)
2. Select the same library and playlist combination
3. Click **Sync Now** to update the playlist with any changes

{: .note }
The Emby/Jellyfin sync is a manual operation. To keep content up-to-date, you'll need to periodically run the sync process from **Channels & VOD** as described above.

The sync process will:
- Add new content that has been added to your media server
- Remove content that has been deleted from your media server (only content originally imported from Emby/Jellyfin)
- Update metadata for existing content
- Preserve any manual additions or modifications you've made to the playlist

### Change Detection

The sync system uses batch tracking to detect changes:

- Each sync creates a new batch identifier
- Content from previous syncs that no longer exists in your media server is automatically removed
- Only content originally imported from Emby/Jellyfin is affected
- Manual additions and content from other sources are preserved

---

## Troubleshooting

### No Content Appears After Import

1. Verify your Emby/Jellyfin connection in **Settings → General**
2. Check that the selected library contains content
3. Ensure the API key has appropriate permissions
4. Review the application logs for any error messages

### Content Not Updating

1. Check the **Last Synced** timestamp in your playlist settings
2. Verify automatic syncing is enabled if desired
3. Manually trigger a sync to force an update
4. Ensure your media server is accessible

### Duplicate Content

**Duplicate content will appear if you enable "Multi-Genre Handling" with the "All Genres - Content appears in all applicable genres" option selected.**

1. Check if you've imported the same library multiple times
2. Review your genre handling settings
3. Consider using the cleanup options in playlist settings

---

## Best Practices

1. **Separate Playlists**: Use separate playlists for Movies and TV Series for better organization
2. **Genre Organization**: Enable genre-based grouping for easier content discovery
3. **Regular Syncing**: Set up automatic syncing to keep content current
4. **Test First**: Start with a small library to test the import process before importing large collections
5. **Monitor Logs**: Check application logs after imports to ensure everything processed correctly

---
