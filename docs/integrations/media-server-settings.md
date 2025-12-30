---
title: Settings
layout: home
parent: Media Server
nav_order: 1
---

# Settings
The section describes several configuration options that exist for media server integrations.

---

## Media Server Action Menu
The media server integration contains various actionable items depicted below

<img width="1350" height="572" alt="image" src="https://github.com/user-attachments/assets/73a98add-6cc2-4dee-b579-b9be04475a0f"/>


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

---
## Media Server Integration (Import Settings)
Within a configured media server integration you can control how Groups (VOD) and Categories (Series) are processed.

There are two options when it comes to genre handling:
- Primary **Use only the first genre. Prevents duplication by placing an item in a single group/category. Recommended for**
- All **Use all genres. Items store all genres and may appear in multiple groups/categories, which increases duplicates, storage, and sync time**

  <img width="988" height="318" alt="image" src="https://github.com/user-attachments/assets/953af053-55bd-407d-8eec-e76a0a8cc8f7"/>

>[!TIP]
>Use Primary unless you specifically need every item to appear in all of its genres and accept the extra duplicates and sync overhead
---
## Sync Schedule
Configures synchronization schedule with the integrated media server. The following are the support sync intervals

- 1 hour
- 3 hours
- 6 hours
- 12 hours
- Daily (Midnight)
- Weekly Sunday

<img width="1007" height="174" alt="image" src="https://github.com/user-attachments/assets/020edffe-1beb-4816-8450-2e76d17d3b8d" />



