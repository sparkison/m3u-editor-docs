---
title: Troubleshooting
layout: home
nav_order: 3
---

# Troubleshooting

## Tips and tricks

### 🔒 Use the app over HTTPS or via reverse proxy/NGINX

Update your `docker-compose.yaml`, or update your docker CLI command, to include additional environment variables `APP_URL` and `OCTANE_HTTPS` to define the app url and enable/disable https support.

Example

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      # This is used for websockets and in-app notifications
      # Set to your machine/container IP where m3u editor will be accessed, if not localhost
      - REVERB_HOST=your-custom-domain.com
      - REVERB_SCHEME=https
      # Uncomment and set to URL or IP if not localhost. Use full url, including http(s)
      - APP_URL=https://your-custom-domain.com
      # Uncomment and set to true if using HTTPS
      - OCTANE_HTTPS=true
    volumes:
      # This will allow you to reuse the data across container recreates.
      # Format is: <host_directory_path>:<container_path>
      # More information: https://docs.docker.com/reference/compose-file/volumes/
      - ./data:/var/www/config
    restart: unless-stopped
    ports:
      - 36400:36400 # app
      - 36800:36800 # websockets/broadcasting
networks: {}
```

Restart the container to see the changes reflected.


### 🌄 Using local images for playlist or EPG icons

 - Map a local directory to a directory in the public directory of m3u editor, e.g.:

  ```yaml
  volumes:
      - ...
      - ./images/logos:/var/www/html/public/logos
  ```

 - Your logos can then be accesses via [http://localhost:36400/logos/filename.jpg](http://localhost:36400/logos/filename.jpg) and updated in your channel or EPG channel within m3u editor.

## Known issues

### 💻 Apple Silicon (M-series) and other non-x86_64 platforms will need to add the `platform` (`platform: linux/x86_64`) parameter to the compose file to support x86_64 architecture - for example:

```yaml
services:
  m3u-editor:
    platform: linux/x86_64
    image: sparkison/m3u-editor:latest
    ...
```
