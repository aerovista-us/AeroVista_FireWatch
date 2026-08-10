# FireWatch camera embeds

The camera matrix mixes **in-page feeds** with **link-outs** based on what each provider allows from `aerovista-us.github.io`.

## Embedded (in dashboard)

| Source | How | Notes |
|--------|-----|--------|
| **ALERTWest** St. Joe Baldy / Black Mtn Boundary | Cloudflare Stream `<iframe>` UIDs from each `cam-console/{id}` page | Prefer Stream iframe over embedding the full console SPA |
| **Idaho 511** corridor cams | `https://511.idaho.gov/map/Cctv/{imageId}` JPEG snapshots | CORS `*`; auto-refresh ~90s with cache-bust query. Full map is `https://511.idaho.gov/map` (`/cctv` 404s) |

## Link-only (provider locked)

| Source | Why |
|--------|-----|
| **Avista WildfireWatch** | `Content-Security-Policy: frame-ancestors 'none'` |
| **FAA WeatherCams** | Page may frame; image API `/api/cameras/.../images/last/` returns **401** without FAA session |
| **CDA Resort live-feed** | Verkada embed domain-locked to `*.cdaresort.com` |
| **IDL camera inventory** | Heavy ArcGIS viewer — already available on the **Fire cameras** map tab |

## Maintaining Stream UIDs

If an ALERTWest embed goes dark, open `https://alertwest.live/cam-console/{id}`, inspect page JSON for `camwebrtcoutput` / Cloudflare Stream UID, and update `LIVE_CAMS[].embed` in `index.html`.

## Maintaining 511 image IDs

Image ids come from Idaho 511 camera list APIs (`images[].id`), not always the map icon `itemId`. Re-verify with a GET that returns `image/jpeg` before changing `SNAP_CAMS`.
