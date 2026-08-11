# FireWatch camera embeds

The camera matrix mixes **link-outs** with an optional **Idaho 511 camera feed wall**.

## Camera matrix (always visible)

Public feed cards open provider pages in a new tab:

| Camera | Link target | Notes |
|--------|-------------|-------|
| **Hoodoo Mountain** | IDL Fire Camera map pin (`48.078877, -116.954031`) | Avista / Pano AI / POL / PVT — IDL inventory has **no** `CameraLiveViewURL` |
| **Gold Mountain / Gold Cup** | IDL map pins | Former `avista.wildfirewatch.com/station/...` public pages currently 404 |
| **St. Joe Baldy / Black Mountain** | ALERTWest `cam-console/{id}` | Public live PTZ |
| **FAA / CDA lake / 511** | Provider pages | Embed limits documented below |

Avista/Pano AI stations are enterprise-gated; FireWatch deep-links the official IDL inventory pin when no public live URL exists.

## Camera feed wall (collapsed by default)

| Source | How | Notes |
|--------|-----|--------|
| **Idaho 511** corridor cams | `https://511.idaho.gov/map/Cctv/{imageId}` JPEG snapshots | Loads only when the wall is expanded; auto-refresh ~90s while open. Full map is `https://511.idaho.gov/map` |

Toggle control: `#camWallToggle` / `#cameraWall`.

## Link-only (matrix + providers)

| Source | Why |
|--------|-----|
| **ALERTWest** (St. Joe Baldy, Black Mtn / console 8598) | Cloudflare Stream iframes did not play reliably in the dashboard — open the official `cam-console/{id}` instead |
| **Avista WildfireWatch** | `Content-Security-Policy: frame-ancestors 'none'` |
| **FAA WeatherCams** | Page may frame; image API `/api/cameras/.../images/last/` returns **401** without FAA session |
| **CDA Resort live-feed** | Verkada embed domain-locked to `*.cdaresort.com` |
| **IDL camera inventory** | Heavy ArcGIS viewer — also on the **Fire cameras** map tab |

## Analytics

Umami is wired inline via `AV:INJECT id="umami-analytics"` (host `https://stats.aerocoreos.com`, FireWatch website UUID). Relevant events:

| Event | When |
|-------|------|
| `firewatch_camera_wall_toggle` | Wall expand/collapse (`open: "true"` / `"false"`) |
| `firewatch_camera_snapshot` | Click a 511 snapshot tile |
| `firewatch_camera_open` | Click a matrix camera card |

## Maintaining 511 image IDs

Image ids come from Idaho 511 camera list APIs (`images[].id`), not always the map icon `itemId`. Re-verify with a GET that returns `image/jpeg` before changing `SNAP_CAMS`.
