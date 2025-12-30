---
title: CLI Commands
layout: home
nav_order: 6
---

# CLI Commands

## These commands can be run from the container CLI

Shell into your container to run these commands. E.g.: `docker exec -it m3u-editor sh`

Any command can be run with `--help` argument to list command info and arguments.

## Playlist & EPG Commands

- `m3ue refresh-playlist {playlist?} {force?}`
  - **INFO**: Manualy run a Playlist sync
  - Arguments:
    - (optional) `playlist` - Playlist ID. When not passed in will check all playlists and sync where needed.
    - (optional) `force` - Force the playlist to refresh, even if it's not shceduled to sync.
- `m3ue refresh-epg {epg?} {force?}`
  - **INFO**: Manualy run an EPG sync
  - Arguments:
    - (optional) `epg` - EPG ID. When not passed in will check all EPGs and sync where needed.
    - (optional) `force` - Force the EPG to refresh, even if it's not scheduled to sync.
- `m3ue xtream-test`
  - **INFO**: Allows you to test your Xtream API credentials and see the returned results. Good for testing and troubleshooting Xtream API credentials.

## M3U Proxy Commands
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

- `php artisan m3u-proxy:status`
  - **INFO**: Check the status of the m3u-proxy service (embedded or external).
- `php artisan m3u-proxy:update`
  - **INFO**: Update the embedded m3u-proxy to the latest version. Only works with embedded proxy.
  - Arguments:
    - (optional) `--force` - Force update even if already using external proxy.
- `php artisan m3u-proxy:restart`
  - **INFO**: Restart the embedded m3u-proxy service.
- `php artisan m3u-proxy:register-webhook`
  - **INFO**: Register webhooks with the m3u-proxy service for stream events.

## EPG Cache Commands
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

- `php artisan app:epg-cache-generate {id}`
  - **INFO**: Generate EPG cache for a specific EPG source. Improves performance for large EPG files.
  - Arguments:
    - `id` - The UUID of the EPG to generate cache for.
- `php artisan app:epg-cache-health-check`
  - **INFO**: Check the health status of all EPG caches and report any issues.
- `php artisan app:clear-playlist-epg-files`
  - **INFO**: Clear cached EPG files for playlists to force regeneration.

## Xtream & STRM Commands
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

- `php artisan app:xtream-generate {playlist?}`
  - **INFO**: Generate .strm files for series and movies from Xtream playlists.
  - Arguments:
    - (optional) `playlist` - Playlist ID. When not passed, processes all Xtream playlists.

## Schedules Direct Commands
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

- `php artisan app:refresh-sd-data`
  - **INFO**: Refresh EPG data from Schedules Direct. Updates lineup information and programme data.

## User & Authentication Commands

- `m3ue generate-key`
  - **INFO**: Generate an app key, if not currently set. Runs automatically on container start. Should not need to be manually run. Only use if you are missing or having issues with your `APP_KEY` not being set.
- `m3ue test-broadcasting`
  - **INFO**: Send a broadcast notification to the app to confirm websockets are working correctly.
- `m3ue reset-password`
  - **INFO**: Resets user password to the password specified.
- `m3ue disable-mfa`
  - **INFO**: Disables multi-factor authentication for user.

## Maintenance Commands

- `m3ue restart-queue`
  - **INFO**: Clears and restarts the queue. This can cause issues if you have any Playlists or EPGs currently syncing. It is recommended to only run this if you are having issues. This can also be run via the Dashboard using the "Reset Queue" button.
- `php artisan app:reset-sync-process`
  - **INFO**: Reset stuck sync processes. Use when a playlist or EPG sync appears to be stuck.
- `php artisan app:logo-cleanup`
  - **INFO**: Clean up cached channel logos older than the configured expiry period.
- `php artisan app:run-scheduled-backups`
  - **INFO**: Manually trigger scheduled backups.
- `php artisan config:show {keys?*}`
  - **INFO**: Display current configuration values.
  - Arguments:
    - (optional) `keys` - Specific configuration keys to display.

### Automated commands

These commands run at set intervals and do not need to be manually run. However, they are included here for completeness sake.

- `m3ue flush-jobs-table {all?}`
  - **INFO**: Will flush stale jobs, or jobs older than three days. This runs automatically daily and should not need to be run manually.
  - Arguments:
    - (optional) `all` - Flush ALL jobs, not just stale/old jobs.
- `m3ue update-check`
  - **INFO**: Checks for app updates. Runs automatically daily and at container start. Results are also cached. You should not need to run manually.
