# FireWatch camera embeds

The camera matrix mixes **in-page snapshots** with **link-outs** based on what each provider allows from `aerovista-us.github.io`.

## Embedded (in dashboard)

| Source | How | Notes |
|--------|-----|--------|
| **Idaho 511** corridor cams | `https://511.idaho.gov/map/Cctv/{imageId}` JPEG snapshots | CORS `*`; auto-refresh ~90s with cache-bust query. Full map is `https://511.idaho.gov/map` (`/cctv` 404s) |

## Link-only

| Source | Why |
|--------|-----|
| **ALERTWest** (St. Joe Baldy, Black Mtn Boundary) | Cloudflare Stream iframes did not play reliably in the dashboard — open the official `cam-console/{id}` instead |
| **Avista WildfireWatch** | `Content-Security-Policy: frame-ancestors 'none'` |
| **FAA WeatherCams** | Page may frame; image API `/api/cameras/.../images/last/` returns **401** without FAA session |
| **CDA Resort live-feed** | Verkada embed domain-locked to `*.cdaresort.com` |
| **IDL camera inventory** | Heavy ArcGIS viewer — also on the **Fire cameras** map tab |

## Analytics

Umami is wired inline via `AV:INJECT id="umami-analytics"` (host `https://stats.aerocoreos.com`). Tracking stays disabled until `websiteId` is a real UUID. See injection-publisher `docs/FIREWATCH_RUNBOOK.md`.

## Maintaining 511 image IDs

Image ids come from Idaho 511 camera list APIs (`images[].id`), not always the map icon `itemId`. Re-verify with a GET that returns `image/jpeg` before changing `SNAP_CAMS`.
