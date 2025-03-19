---
title: Troubleshooting
layout: home
nav_order: 3
---

# Troubleshooting

## Tips and tricks

- 🔒 Use the app over HTTPS:
    - You will need to update the `env` file within the config directory and update: `OCTANE_HTTPS=true` and then restart your container. You may also need to set the `APP_URL` if `localhost` is not valid for your configuration.
        - Note: you can also issue the following cli command within the container to restart the app: `php artisan octane:reload` so you don't need to restart the entire container.
    - This will reload the app with HTTPS support and remove any mixed contents warnings/errors.
- 🌄 Using local images for playlist or EPG icons:
    - Map a local directory to a directory in the public direcory of m3u editor, e.g.:

  ```yaml
  volumes:
      - ...
      - ./images/logos:/var/www/html/public/logos
  ```

    - Your logos can then be accesses via [http://localhost:36400/logos/filename.jpg](http://localhost:36400/logos/filename.jpg) and updated in your channel or EPG channel within m3u editor.

## Known issues

- 💻 Apple Silicon (M-series) and other non-x86_64 platforms will need to add the `platform` (`platform: linux/x86_64`) parameter to the compose file to support x86_64 architecture - for example:

```yaml
services:
  m3u-editor:
    platform: linux/x86_64
    image: sparkison/m3u-editor:latest
    ...
```
