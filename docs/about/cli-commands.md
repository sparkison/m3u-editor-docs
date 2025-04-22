---
title: CLI Commands
layout: home
nav_order: 6
---

# CLI Commands

## These commands can be run from the container CLI

Shell into your container to run these commands. E.g.: `docker exec -it m3u-editor sh`

Any command can be run with `--help` argument to list command info and arguments.

- `m3ue refresh-playlist {playlist?} {force?}`
  - **INFO**: Manualy run a Playlist sync
  - Arguments:
    - (optional) `playlist` - Playlist ID. When not passed in will check all playlists and sync where needed.
    - (optional) `force` - Force the playlist to refresh, even if it's not shceduled to sync.
- `m3ue refresh-epg {epg?}`
  - **INFO**: Manualy run an EPG sync
  - Arguments:
    - (optional) `epg` - EPG ID. When not passed in will check all EPGs and sync where needed.
- `m3ue generate-key`
  - **INFO**: Generate an app key, if not currently set. Runs automatically on container start. Should not need to be manually run. Only use if you are missing or having issues with your `APP_KEY` not being set.
- `m3ue test-broadcasting`
  - **INFO**: Send a broadcast notification to the app to confirm websockets are working correctly.
- `m3ue reset-password`
  - **INFO**: Resets user password to the password specified.
- `m3ue disable-mfa`
  - **INFO**: Disables multi-factor authentication for user.
- `m3ue restart-queue`
  - **INFO**: Clears and restarts the queue. This can cause issues if you have any Playlists or EPGs currently syncing. It is recommended to only run this if you are having issues. This can also be run via the Dashboard using the "Reset Queue" button.
- `m3ue xtream-test`
  - **INFO**: Allows you to test your Xtream API credentials and see the returned results. Good for testing and troubleshooting Xtream API credentials.

### Automated commands

These commands run at set intervals and do not need to be manually run. However, they are included here for completeness sake.

- `m3ue flush-jobs-table {all?}`
  - **INFO**: Will flush stale jobs, or jobs older than three days. This runs automatically daily and should not need to be run manually.
  - Arguments:
    - (optional) `all` - Flush ALL jobs, not just stale/old jobs.
- `m3ue update-check`
  - **INFO**: Checks for app updates. Runs automatically daily and at container start. Results are also cached. You should not need to run manually.
