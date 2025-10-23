---
title: Setup
layout: home
parent: Integrations
nav_order: 0
---

# Initial Setup

### Step 1: Configure Media Server Connection

1. Navigate to **Settings → General**
2. Scroll to the **Emby/Jellyfin Integration** section
3. Enter your server details:
   - **Server URL**: Your media server address (e.g., `http://192.168.1.100:8096` or `https://emby.example.com`)
   - **API Key**: Generate from your server's dashboard under **API Keys**

### Step 2: Generate API Key

**For Emby:**
1. Open Emby dashboard
2. Navigate to **Settings → Advanced → API Keys**
3. Click **New API Key**
4. Give it a descriptive name (e.g., "M3U Editor")
5. Copy the generated key

**For Jellyfin:**
1. Open Jellyfin dashboard
2. Navigate to **Dashboard → API Keys**
3. Click the **+** button
4. Enter an app name (e.g., "M3U Editor")
5. Copy the generated key

### Step 3: Test Connection

1. After entering server URL and API key
2. Click **Test Connection** button
3. Verify successful connection message
4. If connection fails, check:
   - Server URL is correct and accessible
   - API key is valid
   - No firewall blocking the connection
   