---
title: Custom Domains
layout: home
nav_order: 4
---

# Custom Domains

## 🔒 Use the app over HTTPS or via reverse proxy/NGINX
{: .d-inline-block }

New (v0.5.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

Only supported in **m3u editor v0.5.0+**
{: .d-inline-block .v-align-text-bottom .label .label-purple }

Custom domains are are not officially supported on versions under v0.5.0.
For lower versions it's recommended to use machine IP address, as outlined on the [Getting Started]({% link docs/about/getting-started.md %}) page.

Update your `docker-compose.yaml` environment variables, specifically:
 - `APP_URL`
 - `REVERB_HOST`
 - `REVERB_SCHEME`

Example:

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - TZ=Etc/UTC
      - APP_URL=https://your-custom-tld.com
      # This is used for websockets and in-app notifications
      # Set to your machine/container IP where m3u editor will be accessed, if not localhost
      - REVERB_HOST=your-custom-tld.com
      - REVERB_SCHEME=https
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

### ⚡️ Example NGINX configuration

If you're running NGINX on your host machine, the above settings will tell **m3u editor** to run in secure mode under the provided domain. Now it's up to your host machine to handle the SSL.

**NOTE** - It's important to ensure your `REVERB_HOST` is also set correctly, and that `REVERB_SCHEME` is set to `https`, as outlined above to prevent _websocket_ errors.

Example configuration file for your **your-custom-tld.com** website:
```
server {
  listen 80;
  listen [::]:80;
  listen 443 ssl http2;
  listen [::]:443 ssl http2;

  ssl_certificate_key ...
  ssl_certificate ...

  server_name your-custom-tld.com;
  
  root ...

  # Redirect non-https to https
  if ($scheme != "https") {
    rewrite ^ https://$host$uri permanent;
  }
  
  # m3u editor reverse proxy
  location @reverse_proxy {
    proxy_pass http://127.0.0.1:36400;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }

  location / {
    try_files $uri @reverse_proxy;
  }
  
  # m3u editor websockets
  location /app {
    proxy_pass http://127.0.0.1:36800;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_read_timeout 120;
    proxy_send_timeout 120;
  }
}
```