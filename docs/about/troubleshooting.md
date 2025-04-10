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

## Known issues

### 💻 Apple Silicon (M-series) may need to add the `platform` (`platform: linux/x86_64`) parameter to the compose file to support x86_64 architecture - for example:

```yaml
services:
  m3u-editor:
    platform: linux/x86_64 # <--- add this line
    image: sparkison/m3u-editor:latest
    ...
```

## Notes

### 🛠️ Default Docker builds
{: .d-inline-block }

New v0.4.5
{: .d-inline-block .v-align-middle .label .label-purple }

We inlcude both linux/amd64 and linux/arm64 builds in our build process, starting at version v0.4.5
