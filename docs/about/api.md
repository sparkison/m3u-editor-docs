---
title: API
layout: home
nav_order: 5
---

# API 🔌

**m3u editor** provides an API endpoint for performaing various actions.

## Enable API front end view (Swagger docs)

Head to **Preferences** -> **API** -> **Allow access to API docs**

You will see a button on the right (**API Docs**) that will take you to the API docs page where you can view and interact with the API directly.

API docs are available at: [http://localhost:36400/docs/api](http://localhost:36400/docs/api)

<small>* Make sure you replace _http://localhost:36400_ with your instance address or IP, if not localhost.</small>

---

**NOTE**: Some endpoints require authentication via an API token.

### 🔑 How to get an API token

1. Login to **m3u editor**
2. Head to **Profile** (click your user avatar in top-right menu to show the dropdown navigation)
3. Scroll to the bottom of your Profile page to the **API Tokens** section
4. Press **Create** on the API tokens table
5. Enter a name for the token (used for your reference, e.g.: "Home server cron task")
6. Select abilities (typically **view** and **update** are required; this can be changed at any time)
7. Set an expiration (or leave empty for no expiration)
8. Press **Submit**
9. 📋 Copy your token!

Save your token somewhere safe, it will not be shown again, and cannot be recovered. 
You can delete and add as many tokens as you need, there is no restriction.

---

### 🧑‍💻 How to use the API

Once you have a token, you can fetch your EPGs or Playlists to get their status and other attributes.

For example, you can retreive the UUID, which is used to call various endpoints to interact directly with your Playlists and EPGs.

User playlists endpoint: `http://localhost:36400/user/playlists`

To get the results via `curl`, you would use something like this:

```sh
curl --request GET \
  --url http://localhost:36400/user/playlists \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer YOUR_API_TOKEN'
```

Which would return something like this:

```json
[
  {
    "name": "Animation",
    "uuid": "9e6a244d-ef20-437c-952c-da98990309cc"
  },
  {
    "name": "Comedy",
    "uuid": "9e6a2464-4e16-4553-8c9c-61200714c725"
  },
  {
    "name": "Entertainement",
    "uuid": "9e6a247f-6c3b-452c-b85c-0e3d15fbc998"
  },
  {
    "name": "General",
    "uuid": "9e6a249f-35ab-4b64-9352-ba3e04e547d2"
  },
  {
    "name": "Sports",
    "uuid": "9e6a24b9-9e5c-47ca-aedc-45c174224dd2"
  },
]
```

You could then call one of the playlist endpoints to manipulate it.

For example, to force a Playlist to sync, you could do this:
```sh
curl --request GET \
  --url 'http://localhost:36400/playlist/9e6a24b9-9e5c-47ca-aedc-45c174224dd2/sync?force=true' \
  --header 'Accept: application/json'
```

Which would force the **Sports** playlist from the results above to be synced immediately.

Be sure to head to [http://localhost:36400/docs/api](http://localhost:36400/docs/api) for the most recent API endpoints and to view and interact with the API directly (make sure you've enabled the API docs: **Preferences** -> **API** -> **Allow access to API docs**, otherwise you will see a `403 Forbidden` error).