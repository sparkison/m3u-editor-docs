---
title: Custom Domains
layout: home
nav_order: 4
---

# Custom Domains

## 🔒 Use the app with a custom domain, with, or without HTTPS support
{: .d-inline-block }

New (v0.5.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

Only supported in **m3u editor v0.5.0+**
{: .d-inline-block .v-align-text-bottom .label .label-purple }

**NOTE**: Custom domains are are not officially supported on versions under v0.5.0.
For lower versions it's recommended to use machine IP address, as outlined on the [Getting Started]({% link docs/about/getting-started.md %}) page.

#### Table of contents

- [Docker compose updates](#-docker-compose-updates)
- [NGINX example](#-example-nginx-configuration)
- [Traefik example](#-traefik-example)
- [Caddy example](#-caddy-example)

---

### 🐳 Docker compose updates

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

--- 

### 🏁 Example NGINX configuration

If you're running NGINX on your host machine, the above settings will tell **m3u editor** to run in secure mode under the provided domain. Now it's up to your host machine to handle the SSL.

**NOTE** - It's important to ensure your `REVERB_HOST` is also set correctly, and that `REVERB_SCHEME` is set to `https`, as outlined above to prevent _websocket_ errors.

Example configuration file for your **your-custom-tld.com** website:
```conf
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

---

### 🚦 Traefik Example

If your running [Traefik](https://traefik.io/traefik/) on your server, use something like the below to get **m3u editor** running properly under a custom domain.

Example `docker-compose.yaml` with Traefik labels:

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor-traefik
    environment:
      - TZ=Etc/UTC
      - APP_URL=https://your-custom-tld.com
      # This is used for websockets and in-app notifications
      # Set to your machine/container IP where m3u editor will be accessed, if not localhost
      - REVERB_HOST=your-custom-tld.com
      - REVERB_SCHEME=https
    volumes:
      - ./m3u-editor-config:/var/www/config
    restart: unless-stopped
    labels:
      - "traefik.enable=true"

      # Route all other paths (/) to the app on port 36400
      - "traefik.http.routers.m3u-main.rule=Host(`your-custom-tld.com`)"
      - "traefik.http.routers.m3u-main.entrypoints=websecure"
      - "traefik.http.routers.m3u-main.tls.certresolver=myresolver"
      - "traefik.http.routers.m3u-main.service=m3u-main"
      - "traefik.http.services.m3u-main.loadbalancer.server.port=36400"
      - "traefik.http.services.m3u-main.loadbalancer.server.scheme=http"

      # Route `/app` (websockets) to port 36800
      - "traefik.http.routers.m3u-ws.rule=Host(`your-custom-tld.com`) && PathPrefix(`/app`)"
      - "traefik.http.routers.m3u-ws.entrypoints=websecure"
      - "traefik.http.routers.m3u-ws.tls.certresolver=myresolver"
      - "traefik.http.routers.m3u-ws.service=m3u-ws"
      - "traefik.http.routers.m3u-ws.middlewares=websocket"
      - "traefik.http.services.m3u-ws.loadbalancer.server.port=36800"
      - "traefik.http.services.m3u-ws.loadbalancer.server.scheme=http"

      # Middleware: WebSocket support headers
      - "traefik.http.middlewares.websocket.headers.customrequestheaders.Connection=Upgrade"
      - "traefik.http.middlewares.websocket.headers.customrequestheaders.Upgrade=websocket"
      - "traefik.http.middlewares.websocket.headers.customrequestheaders.Host=your-custom-tld.com"
      - "traefik.http.middlewares.websocket.headers.customrequestheaders.X-Forwarded-Host=your-custom-tld.com"
      - "traefik.http.middlewares.websocket.headers.customrequestheaders.X-Forwarded-Proto=https"
    ports:
      - 36400:36400
      - 36800:36800
```

Make sure your Trafik container has the resolver, `myresolver`, defined, or change the name to match your setup.

E.g., the `myresolver` referenced in the setup above would look something like this in your Traefik container:

```yaml
services:
  traefik:
    image: traefik:v3
    container_name: traefik
    restart: unless-stopped
    ports:
      - 443:443
      - 80:80
    command:
      - --providers.docker=true
      - --providers.docker.exposedbydefault=false
      - --entryPoints.web.address=:80
      - --entryPoints.websecure.address=:443
      - --certificatesresolvers.myresolver.acme.email=${LETSENCRYPT_EMAIL}
      - --certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json
      - --certificatesresolvers.myresolver.acme.httpchallenge=true
      - --certificatesresolvers.myresolver.acme.httpchallenge.entrypoint=web
```

---

### 🔒 Caddy Example

Caddyfile example:

```
m3u-editor {
  reverse_proxy /app* m3u-editor:36800 {
    transport http {
      versions h1
    }
  }
  reverse_proxy /* m3u-editor:36400
}
```