# Storyteller d10 · Aberrant Room Service · v0.7.15-alpha

GitHub/Render-ready realtime room service for the Storyteller d10 Aberrant app.

## New in v0.7.15

- Adds a true read-only **Remote Table viewer** role over Protocol 23.
- Viewers receive the shared Table, assets, reveals, shared dice, and session log.
- Viewers do **not** appear in the playable member roster, receive checks, own actors, enter initiative, or mutate room state.
- `room_state.viewerCount` reports connected remote displays separately.
- Normal player/GM room behavior remains Protocol 23 compatible.

## Deploy / update on Render

Put this folder at the **root of the GitHub repository** connected to Render, commit, and push. Render will redeploy automatically when Auto-Deploy is enabled.

The included `render.yaml` uses:

- Runtime: Node
- Build command: `npm ci`
- Start command: `npm start`
- Health check: `/health`

After the deploy, check:

`https://YOUR-SERVICE.onrender.com/health`

The app connects by WebSocket at:

`wss://YOUR-SERVICE.onrender.com`

For the currently configured Aberrant service:

`wss://ww-app-1tke.onrender.com`

## Important

Rooms are in memory. A server restart/redeploy clears active rooms. Character libraries, prep libraries, and local app backups are not stored on this server.

## Local run

```bash
npm ci
npm start
```

Default local endpoint: `ws://localhost:8787`
