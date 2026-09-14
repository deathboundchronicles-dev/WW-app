# Storyteller d10 · Aberrant Room Service

GitHub/Render-ready realtime room service for the Storyteller d10 Aberrant app.

## What this is

- Generic Storyteller d10 room/session server
- WebSocket protocol: **23**
- Certified server implementation: **v0.7.7-alpha**
- Packaged for the Aberrant **v0.7.13-alpha** client line
- No npm runtime dependencies
- HTTP health endpoint at `/health`
- WebSocket upgrade on the same public port
- Server heartbeat / stale-connection cleanup
- In-memory room state with a default 12-hour empty-room TTL

This server owns shared session/Table authority. It deliberately does not contain Aberrant-specific dice, power, damage, or movement formulas.

## Deploy to Render with Blueprint

1. Create a new GitHub repository.
2. Put the contents of this folder at the **root** of the repo.
3. Commit and push.
4. In Render, choose **New → Blueprint**.
5. Connect the GitHub repository.
6. Render reads `render.yaml` and creates the Node web service.
7. When the deploy is live, open:

   `https://YOUR-SERVICE.onrender.com/health`

   You should receive JSON containing `"ok": true` and `"protocol": 23`.

8. Your public WebSocket endpoint is the same hostname using `wss://`:

   `wss://YOUR-SERVICE.onrender.com`

Do not use `ws://` for the public Render endpoint.

## Deploy manually in the Render dashboard

If you do not use the Blueprint:

- Service type: **Web Service**
- Runtime: **Node**
- Build command: `npm ci`
- Start command: `npm start`
- Health check path: `/health`

The server automatically listens on Render's `$PORT` and binds to `0.0.0.0`.

## Connect the Aberrant app

### Fastest method

In the app:

1. Open the Table.
2. Open **Advanced connection**.
3. Set **Room service endpoint** to:

   `wss://YOUR-SERVICE.onrender.com`

4. Save/use that endpoint.
5. Start a room as Storyteller, then join from another browser/device with the room code.

### Make the hosted service the app's default

In the standalone HTML, find:

```html
<meta content="" name="storyteller-room-service"/>
```

and set it to:

```html
<meta content="wss://YOUR-SERVICE.onrender.com" name="storyteller-room-service"/>
```

The Play-Together UX will then use the hosted service automatically unless the device has an explicit custom endpoint override.

## Environment variables

All are optional.

| Variable | Default | Purpose |
|---|---:|---|
| `PORT` | `8787` locally | Render supplies this automatically. |
| `HOST` | `0.0.0.0` | Bind address. Leave this alone on Render. |
| `ROOM_TTL_MS` | `43200000` | Empty-room retention, default 12 hours. |
| `MAX_PAYLOAD` | `5242880` | Maximum WebSocket message bytes. |
| `MAX_ASSET_DATA` | `3700000` | Maximum encoded asset payload. |
| `MAX_ROOM_ASSETS` | `120` | Maximum assets retained by one room. |
| `MAX_LOG` | `300` | Shared room log cap. |
| `ALLOWED_ORIGINS` | empty | Comma-separated exact browser origins. Empty accepts all origins. |

### Origin locking

Leave `ALLOWED_ORIGINS` empty for the first deployment, especially if you are still opening the standalone app locally.

After you host the client at a stable HTTPS origin, you can restrict it, for example:

`https://yourname.github.io,https://your-domain.example`

Use exact origins, without paths.

## Local test

```bash
npm ci
npm start
```

Then open:

`http://localhost:8787/health`

The local WebSocket endpoint is:

`ws://localhost:8787`

## Persistence warning

Room state is currently **in memory**. A process restart, Render redeploy, or free-instance spin-down starts with no active rooms. Character files, local libraries, prep, and backups stored by the app are separate and are not erased by this.

For campaign rooms that must survive server restarts, add a persistent room-state backend in a later server revision.

## Render Free-plan note

Render Free web services can spin down after 15 minutes without inbound HTTP traffic or WebSocket messages. The next request/connection wakes the service again. Because room state is in memory, a spin-down also means active room state is lost.

For live sessions, ongoing WebSocket traffic counts as activity, so an actively used room should remain awake. A paid always-on service avoids the idle spin-down behavior.
