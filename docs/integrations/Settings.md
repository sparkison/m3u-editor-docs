---
title: Settings
layout: home
parent: Integrations
nav_order: 1
---

# Settings
The section describes several configuration options that exist for media server integrations.

---

## Media Server Action Menu
The media server integration contains various actionable items depicted below

<img width="1350" height="572" alt="image" src="https://github.com/user-attachments/assets/73a98add-6cc2-4dee-b579-b9be04475a0f" />

### Test Connection
**Purpose:** Tests the connection to the media server

**Behavior:**
- Calls the MediaServerService to test connectivity
- Shows success notification with server name and version if successful
- Shows error notification with failure message if unsuccessful
---
### Sync Now
**Purpose:** Manually triggers a full sync of content from the media server

**Behavior:**
- Will sync all content from the media server. **For large libraries, this may take several minutes**.
- Dispatches a SyncMediaServer job to the queue
- Shows success notification that sync has started
- User will receive notifications when sync completes/fails
---
### Cleanup Duplicates
**Purpose:** Removes duplicate series entries created during sync format changes

**Behavior:**
- Will find and merge duplicate series entries that were created due to sync format changes. Duplicate series without episodes will be removed, and their seasons will be merged into the series that has episodes.
- Calls cleanupDuplicateSeries() method
- Shows info notification if no duplicates found
- Shows success notification with counts of merged/deleted items if duplicates were found
---
### Edit
**Purpose:** Opens the edit form for the media server integration.
---
### View Playlist
**Purpose:** Navigates to the associated playlist's edit page

**Behavior:**
- Opens the playlist edit page in a new navigation
---
### Delete
**Purpose:** Removes the media server integration

  


