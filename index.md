---
title: Home
layout: home
nav_order: 1
---

# m3u editor

![logo](https://github.com/sparkison/m3u-editor-docs/blob/main/assets/images/favicon.png?raw=true)

A simple `m3u` playlist editor, similar to **xteve** or **threadfin**, with `epg` management.

Works with m3u, m3u8, m3u+ and Xtream codes api!

## Prerequisites

- [Docker](https://www.docker.com/) installed on your system.
- Xtream codes API login info or M3U URLs/files containing an M3U playlist of video streams.
- (Optionally) EPG URLs/files containing valid XMLTV data.

> Once built, head to the `env` file with your linked config directory and make sure the `REVERB_HOST` is properly set to the machine IP (if not `localhost`) for websockets and broadcasting to work correctly. This is not required for the app to function, but you will not receive in-app notifications without it. You will need to restart the container after changing this.
