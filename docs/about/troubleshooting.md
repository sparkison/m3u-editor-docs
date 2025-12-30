---
title: Troubleshooting
layout: home
nav_order: 3
---

# Troubleshooting

## Tips and tricks

### 🔒 Use the app over HTTPS or via reverse proxy/NGINX

See the [Custom Domains]({% link docs/about/custom-domains.md %}) page for more information.

### 🌄 Using local images for playlist or EPG icons

 - Map a local directory to a directory in the public directory of m3u editor, e.g.:

  ```yaml
  volumes:
      - ...
      - ./images/logos:/var/www/html/public/logos
  ```

 - Your logos can then be accesses via [http://localhost:36400/logos/filename.jpg](http://localhost:36400/logos/filename.jpg) and updated in your channel or EPG channel within m3u editor.

---

## 🔄 M3U Proxy Issues
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

### Streams not working through proxy

1. **Check proxy status:**
   ```bash
   docker exec -it m3u-editor php artisan m3u-proxy:status
   ```

2. **Enable debug logging:**
   Add `M3U_PROXY_LOG_LEVEL=DEBUG` to your environment variables, then check logs:
   ```bash
   docker exec -it m3u-editor cat /var/www/html/storage/logs/m3u-proxy.log
   ```

3. **Verify token matches (external proxy):**
   Ensure `M3U_PROXY_TOKEN` in m3u-editor matches `API_TOKEN` in m3u-proxy container.

### Stream pooling not working

Stream pooling requires:
- External m3u-proxy setup (`M3U_PROXY_ENABLED=false`)
- Redis configured and healthy
- `ENABLE_REDIS_POOLING=true` on m3u-proxy

Check Redis connection:
```bash
docker exec -it redis redis-cli ping
# Should return: PONG
```

### Proxy connection timeouts

1. Increase timeout values in your reverse proxy configuration
2. Check if provider streams are accessible directly
3. Verify network connectivity between containers

---

## 📺 EPG Cache Issues
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

### EPG data not showing or outdated

1. **Check cache health:**
   ```bash
   docker exec -it m3u-editor php artisan app:epg-cache-health-check
   ```

2. **Regenerate cache:**
   ```bash
   docker exec -it m3u-editor php artisan app:epg-cache-generate {uuid}
   ```

3. **Clear and resync:**
   ```bash
   docker exec -it m3u-editor php artisan app:clear-playlist-epg-files
   docker exec -it m3u-editor m3ue refresh-epg {epg_id} force
   ```

### Large EPG files causing memory issues

- Increase PHP memory limit in container
- Use PostgreSQL instead of SQLite for better performance
- Enable EPG caching to reduce memory usage during API calls

---

## 🔄 Sync Issues

### Playlist sync stuck or failing

1. **Reset sync process:**
   ```bash
   docker exec -it m3u-editor php artisan app:reset-sync-process
   ```

2. **Restart queue:**
   ```bash
   docker exec -it m3u-editor m3ue restart-queue
   ```

3. **Check queue status** in the Dashboard

### Provider rate limiting

If your provider is rate limiting:
- Increase sync intervals
- Disable "Prioritize by resolution" in auto-merge settings
- Use the embedded proxy to mask multiple client connections

---

## Known issues

### 💻 Apple Silicon (M-series) may need to add the `platform` (`platform: linux/x86_64`) parameter to the compose file to support x86_64 architecture - for example:

```yaml
services:
  m3u-editor:
    platform: linux/x86_64 # <--- add this line
    image: sparkison/m3u-editor:latest
    ...
```

**Note**: Starting with v0.4.5, native ARM64 builds are available. Try without the platform parameter first.

---

## 🐛 Debug Commands

Useful commands for troubleshooting:

```bash
# Check application logs
docker exec -it m3u-editor tail -f /var/www/html/storage/logs/laravel.log

# Check m3u-proxy logs (if enabled)
docker exec -it m3u-editor tail -f /var/www/html/storage/logs/m3u-proxy.log

# Test websocket connection
docker exec -it m3u-editor m3ue test-broadcasting

# Show current configuration
docker exec -it m3u-editor php artisan config:show

# Check queue status
docker exec -it m3u-editor php artisan queue:monitor
```

---

## Notes

### 🛠️ Default Docker builds
{: .d-inline-block }

New v0.4.5
{: .d-inline-block .v-align-middle .label .label-purple }

We inlcude both linux/amd64 and linux/arm64 builds in our build process, starting at version v0.4.5
