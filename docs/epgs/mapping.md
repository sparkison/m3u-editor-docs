---
title: Mapping EPG to Playlist
layout: home
parent: EPG
---

# Mapping EPG to Playlist

## Provider EPG and Playlist

If you're using your provider Playlist and EPG url, the mapping should work without any modifications. Simply head to the **Channels** section and select the **Map EPG to Playlist** option from the **Actions** dropdown. Select your EPG and Playlist to perform the mapping on and press **Map now** to begin. You can configure to run on each EPG sync as well.

- Overwrite (default is false)
    - Will overwrite channel if match is found, even if it already has an EPG channel assigned
- Recurring (default is false)
    - Will re-run mapping each time the EPG is synced. Useful to automatically map EPG for newly added channels.

## Custom EPG(s) and Playlist

If you're using a different EPG source(s), and the mapping isn't working as expected, you can map the EPG to the Playlist by following these steps:
- To solve for auto-mapping not working, you can manually edit each channel you wish to assign an EPG to and select the EPG channel from the dropdown list.
- See notes about fallback mapping below when a direct match isn't found.

## Notes about how it works

The EPG to Channel mapping works by first looking for an exact match (ignoring case) of the channels `tvg-id` to the EPGs `channel` field. This will work in most cases, especially with when both Playlist and EPG are from the same provider.

When a direct match is not found, further matches are looked up based on `tvg-id` and `title` and compared against the EPG `channel` and `title` and use the **Levenshtein Distance** algorithm to find the best match. If no match is found within the defined threshold, canidates are then compared via **Cosine Similarity**.

The EPG **Preferred Locale** field is used to give a larger weight to matches containing the locale string entered.