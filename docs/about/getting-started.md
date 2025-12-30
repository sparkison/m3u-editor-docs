---
title: Getting Started
layout: home
nav_order: 2
---

# Getting started

**Access via**: [http://localhost:36400](http://localhost:36400) (user = admin, password = admin)

To ensure the data is saved across builds, link an empty volume to: `/var/www/config` within the container. This is where the `env` file will be stored, along with the sqlite database and the application log files.

#### Versions

We inlcude both **linux/amd64** and **linux/arm64** builds in our build process, starting at version `v0.4.5`. We also push regular updates and new beta features to the `experimental` and `dev` branches.

- 💪 Use the latest stable release: `sparkison/m3u-editor:latest`
- #️⃣ Use a specific version: `sparkison/m3u-editor:0.8.0`, `sparkison/m3u-editor:0.7.11`, etc.
- 🔥 Use the dev branch: `sparkison/m3u-editor:dev`
  - stable'ish branch – we try to keep this one clean and fix bugs as quickly as possible
- 🧪 Use the experimental branch: `sparkison/m3u-editor:experimental`
  - new and fun features that can break things; features will be announced on our Discord under the **#releases** channel

#### Table of contents

- [Docker compose (simplest)](#-docker-compose)
- [Docker with m3u-proxy (recommended v0.8.x+)](#-docker-with-m3u-proxy-recommended) <sup>(v0.8.0+)</sup>
- [Docker with internal Postgres](#-docker-compose-with--sql-postgresql) <sup>(v0.6.0+)</sup>
- [Docker with your Postgres (advanced)](#-if-youd-like-to-use-your-own-postgresql-instance) <sup>(v0.6.0+)</sup>
- [Advanced deployment options](#-advanced-deployment-options)
---

## 🐳 Docker compose

Use the following compose example to get up and running.

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - TZ=Etc/UTC
      - APP_URL=http://localhost # or http://192.168.0.123 or https://your-custom-tld.com
    volumes:
      # This will allow you to reuse the data across container recreates
      # Format is: <host_directory_path>:<container_path>
      # More information: https://docs.docker.com/reference/compose-file/volumes/
      - ./data:/var/www/config
    restart: unless-stopped
    ports:
      - 36400:36400
networks: {}
```

Or via Docker CLI:

```bash
 docker run --name m3u-editor -e TZ=Etc/UTC -e APP_URL=http://localhost -v ./data:/var/www/config --restart unless-stopped -p 36400:36400 sparkison/m3u-editor:latest 
```

---

## 🐳 Docker with M3U-Proxy (Recommended)
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

Starting with **v0.8.0**, **m3u editor** includes built-in m3u-proxy support for stream proxying, transcoding, and connection pooling. This is the **recommended setup** for most users.

### Why use M3U-Proxy?

- **Stream Pooling**: Multiple clients can share a single provider connection
- **Connection Management**: Avoid "max connections exceeded" errors
- **Transcoding Support**: Convert streams to HLS format
- **Reduced Provider Load**: Single connection serves multiple viewers

### Quick Setup with External Proxy (Recommended)

Running m3u-proxy as a separate container provides better performance and Redis-based pooling:

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - TZ=Etc/UTC
      - APP_URL=http://localhost
      - APP_PORT=36400
      # Postgres (recommended for performance)
      - ENABLE_POSTGRES=true
      - PG_DATABASE=m3ue
      - PG_USER=m3ue
      - PG_PASSWORD=changeme
      - DB_CONNECTION=pgsql
      - DB_HOST=localhost
      - DB_PORT=5432
      - DB_DATABASE=m3ue
      - DB_USERNAME=m3ue
      - DB_PASSWORD=changeme
      # Redis (external)
      - REDIS_ENABLED=false
      - REDIS_HOST=redis
      - REDIS_SERVER_PORT=6379
      # M3U Proxy (external)
      - M3U_PROXY_ENABLED=false
      - M3U_PROXY_HOST=m3u-proxy
      - M3U_PROXY_PORT=38085
      - M3U_PROXY_TOKEN=your-secure-token
    volumes:
      - ./data:/var/www/config
      - pgdata:/var/lib/postgresql/data
    restart: unless-stopped
    ports:
      - 36400:36400
    networks:
      - m3u-network
    depends_on:
      - m3u-proxy
      - redis

  m3u-proxy:
    image: sparkison/m3u-proxy:latest
    container_name: m3u-proxy
    environment:
      - API_TOKEN=your-secure-token  # Must match M3U_PROXY_TOKEN above
      - PORT=38085
      - REDIS_ENABLED=true
      - REDIS_HOST=redis
      - REDIS_PORT=6379
      - REDIS_DB=6
      - ENABLE_REDIS_POOLING=true
      - LOG_LEVEL=INFO
    restart: unless-stopped
    networks:
      - m3u-network
    depends_on:
      - redis
    # Optional: Hardware acceleration
    # devices:
    #   - /dev/dri:/dev/dri

  redis:
    image: redis:alpine
    container_name: redis
    restart: unless-stopped
    networks:
      - m3u-network
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

networks:
  m3u-network:
    driver: bridge

volumes:
  pgdata:
  redis_data:
```

### Embedded Proxy (Simpler Alternative)

If you prefer a simpler setup, you can use the embedded m3u-proxy (enabled by default):

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - TZ=Etc/UTC
      - APP_URL=http://localhost
      - M3U_PROXY_ENABLED=true  # Uses embedded proxy (default)
    volumes:
      - ./data:/var/www/config
    restart: unless-stopped
    ports:
      - 36400:36400
networks: {}
```

**Note**: The embedded proxy doesn't support Redis-based stream pooling or hardware acceleration.

---

## 🐳 Docker compose with 🐘 SQL (PostgreSQL)
{: .d-inline-block }

New (v0.6.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

By default **m3u editor** uses **SQLite** as the database driver. 

If you'd like something more resilient, you can switch to the **pgsql** driver instead and utilize the internal **PostgreSQL** instance for your database storage. **m3u editor** supports this out of the box!

Update your `docker-compose.yaml` file like this to use **SQL/PostgreSQL**:

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - TZ=Etc/UTC
      - APP_URL=http://localhost # or http://192.168.0.123 or https://your-custom-tld.com
      - ENABLE_POSTGRES=true               # <----- start here
      - PG_DATABASE=${PG_DATABASE:-m3ue}   # <----- DB name
      - PG_USER=${PG_USER:-m3ue}           # <----- DB user
      - PG_PASSWORD=${PG_PASSWORD:-secret} # <----- DB password
      - PG_PORT=${PG_PORT:-5432}           # <----- DB port (optional, defaults to 5432)
      - DB_CONNECTION=pgsql                # <----- set to `pgsql` (default is `sqlite`)
      - DB_HOST=localhost                  # <----- make sure set to `localhost`
      - DB_PORT=${PG_PORT:-5432}           # <----- using default Postgres port
      - DB_DATABASE=${PG_DATABASE:-m3ue}   # <----- should match `PG_DATABASE`
      - DB_USERNAME=${PG_USER:-m3ue}       # <----- should match `PG_USER`
      - DB_PASSWORD=${PG_PASSWORD:-secret} # <----- should match `PG_PASSWORD`
    volumes:
      # This will allow you to reuse the data across container recreates
      # Format is: <host_directory_path>:<container_path>
      # More information: https://docs.docker.com/reference/compose-file/volumes/
      - ./data:/var/www/config
      - pgdata:/var/lib/postgresql/data # <----- link volume `pgsql` data to retain data
    restart: unless-stopped
    ports:
      - 36400:36400
      # - 5432:5432 # <----- (optionally) expose the pgqsl instance
networks: {}
volumes:
  pgdata:           # <----- created named volume for Postgres store
```

then make sure to add `.env` variables in the root of project (where your `docker-compose.yaml` lives) if you want to override the default values, e.g.:

```
PG_DATABASE=database_name
PG_USER=database_user
PG_PASSWORD=a_password
PG_PORT=65432
```

---

## 🔧 If you'd like to use your own PostgreSQL instance
{: .d-inline-block }

New (v0.6.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

Just update the `DB_` variables and exclude the `PG_` variables.

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - TZ=Etc/UTC
      - APP_URL=http://localhost # or http://192.168.0.123 or https://your-custom-tld.com
      # - ENABLE_POSTGRES=false     # <----- disable, or exclude variable, either works
      - DB_CONNECTION=pgsql         # <----- set to `pgsql` (default is `sqlite`)
      - DB_HOST=hostname            # <----- your Postgres instance hostname (localhost, 192.168.0.456, etc.)
      - DB_PORT=5432                # <----- your Postgres instance port
      - DB_DATABASE=database        # <----- your Postgres database name
      - DB_USERNAME=user            # <----- user for your Postgres database
      - DB_PASSWORD=password        # <----- password for your Postgres database
    volumes:
      # This will allow you to reuse the data across container recreates
      # Format is: <host_directory_path>:<container_path>
      # More information: https://docs.docker.com/reference/compose-file/volumes/
      - ./data:/var/www/config
    restart: unless-stopped
    ports:
      - 36400:36400
networks: {}
```

---

## 📕 Tips & Notes

### 🩺 Health check options

**m3u editor** has a built-in health check you can use when needed.

as an example, you can add this to your **m3u-editor** docker compose to utilize it:
```yaml
services:
  m3u-editor:
      healthcheck:
        test: ["CMD", "curl", "-f", "http://localhost:36400/up"] # Make sure to update the port if you've changed it, url can remain localhost as it's an internally run command
        interval: 10s
        timeout: 10s
        retries: 10
        start_period: 60s
```

A more complete example would look something like this:

```yaml
services:
  m3u-editor:
    image: sparkison/m3u-editor:latest
    container_name: m3u-editor
    environment:
      - TZ=Etc/UTC
      - APP_URL=http://192.168.0.123
      - ENABLE_POSTGRES=true
      - DB_CONNECTION=pgsql
      - DB_HOST=localhost
      - PG_PORT=5432
      - DB_PORT=5432
      - PG_DATABASE=m3ue
      - DB_DATABASE=m3ue
      - PG_USER=m3ue
      - DB_USERNAME=m3ue
      - PG_PASSWORD=secret
      - DB_PASSWORD=secret
    volumes:
      - ./data:/var/www/config
      - pgdata:/var/lib/postgresql/data
    restart: unless-stopped
    ports:
      - 36400:36400
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:36400/up"] # Make sure to update the port if you've changed it, url can remain localhost as it's an internally run command
      interval: 10s
      timeout: 10s
      retries: 10
      start_period: 60s

  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    depends_on:
      m3u-editor:
        condition: service_healthy
    ports:
      - 8096:8096
    volumes:
      - ./jellyfin/config:/config
      - ./jellyfin/cache:/cache
      - ./jellyfin/media:/media
    restart: unless-stopped

networks: {}
volumes:
  pgdata:
```

---

## 🚀 Advanced Deployment Options
{: .d-inline-block }

New (v0.8.0)
{: .d-inline-block .v-align-text-bottom .label .label-purple }

**m3u editor** provides several pre-configured docker-compose files for different deployment scenarios:

| File | Description | Use Case |
|:-----|:------------|:---------|
| `docker-compose.proxy.yml` | External m3u-proxy + Redis | **Recommended** for production |
| `docker-compose.proxy-vpn.yml` | External proxy with VPN support | When VPN is required for streams |
| `docker-compose.aio.yml` | All-in-one setup | Simple deployments |
| `docker-compose.external-all.yml` | Fully modular (Nginx) | Advanced custom setups |
| `docker-compose.external-all-caddy.yml` | Fully modular (Caddy) | Automatic HTTPS with Caddy |

### Quick Deployment

```bash
# Download the recommended setup
curl -O https://raw.githubusercontent.com/sparkison/m3u-editor/main/docker-compose.proxy.yml
curl -O https://raw.githubusercontent.com/sparkison/m3u-editor/main/.env.proxy.example

# Setup environment
cp .env.proxy.example .env
# Edit .env and configure your settings

# Deploy
docker-compose -f docker-compose.proxy.yml up -d
```

See the [GitHub repository](https://github.com/sparkison/m3u-editor) for detailed documentation on each deployment option.

---
