## Overview

The M3U Editor provides seamless integration with Emby and Jellyfin media servers, allowing you to automatically sync your media library into playlists. This integration supports both VOD (Video on Demand) content and TV series with full metadata synchronization.

### What This Integration Does

- **Automatic Content Sync**: Import movies and TV series from your media server
- **Metadata Preservation**: Maintains titles, descriptions, ratings, cast, genres, and artwork
- **Smart Organization**: Automatically creates groups/categories based on genres
- **Change Detection**: Keeps playlists synchronized with your media library
- **Flexible Streaming**: Choose between direct file paths or server streaming URLs

---

## Platform Compatibility

Both **Emby Media Server** and **Jellyfin Media Server** are fully supported. These platforms share the same API structure, making them interchangeable for this integration.

### Tested Versions

- Emby Server 4.x and later
- Jellyfin 10.x and later

### Requirements

- Active Emby or Jellyfin server installation
- API key with appropriate permissions
- Network access from M3U Editor to your media server

## Key Features

### 1. Multi-Genre Support

Movies and series can appear in multiple genre groups simultaneously, providing better content organization and discovery.

**How it works:**
- Each item's genre metadata is analyzed during sync
- Content can be placed in one genre (primary) or all genres
- Each genre entry maintains a unique `source_id` to prevent conflicts
- Users can browse the same content through different genre categories

**Example:**
A movie tagged with "Action", "Thriller", and "Sci-Fi" can appear in all three groups when "All Genres" mode is enabled.

### 2. Automatic Genre Organization

The system automatically creates and organizes groups/categories based on genre metadata.

**Features:**
- Automatic group/category creation during sync
- Clean, standardized genre names
- Batch tracking for change detection
- Hierarchical organization (VOD uses groups, series uses categories)

## 3. Change Detection and Cleanup

The sync system intelligently detects changes and keeps playlists up-to-date.

**Capabilities:**
- **Added Content**: New items automatically imported
- **Removed Content**: Deleted media removed from playlists
- **Updated Metadata**: Changes synchronized automatically
- **Batch Tracking**: UUID-based batch numbers identify current vs. outdated content

**Safety Features:**
- Only removes content that originated from Emby/Jellyfin (identified by `source_id` prefix)
- Manual additions and other sources are preserved
- Detailed logging of all cleanup operations

### 4. Flexible Streaming Options

Choose how content is accessed:

- **Server Streaming URLs**: Stream through your media server (default)
- **Direct File Paths**: Access local files directly (requires file system access)
