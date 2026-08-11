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

## Hoodoo Mountain (Avista · Pano AI)

| Field | Value |
|-------|-------|
| Summit | Hoodoo Mountain, Bonner County, ID (~5,091 ft; Blanchard USGS) |
| Coordinates | `48.078877, -116.954031` (matches historic summit / Bonner County repeater site) |
| Owner / provider | Avista / **Pano AI** (IDL spells provider `Pano Al`) |
| IDL status | Active; inventory row created **2026-06-17**; `CameraLiveViewURL` = **null** |
| Supervisory area | POL (IDL) · private landowner (PVT) |

### Site history

- Historic fire lookout (cupola 1923 → L-4 1930s) abandoned by mid-1930s; site became an electronics / communications location ([Idaho Fire Lookouts](https://www.idahofirelookouts.com/cda-region/hoodoo-mountain-lookout/)).
- Longstanding Bonner County VHF repeater location (~48.08053, -116.9536).
- Avista contracted **Pano AI** for wildfire detection cameras (WA UTC / Idaho WMP testimony): 360° panoramas ~every 1–2 minutes, Pano Intelligence Center verification, agency onboarding. Avista reported 10 cameras in 2024 (3 Idaho / 7 Washington) and further builds afterward; coordinates placements with IDL and the Idaho Fire Camera Interoperability Committee.

### Where the live stream actually lives

| Surface | URL | Public? |
|---------|-----|---------|
| **Avista Wildfire Watch tenant** | https://avista.wildfirewatch.com/ | **No** — SPA login; `robots.txt` Disallow `/`; CSP talks to `*.pano.ai` + `api.wildfirewatch.com` |
| **Pano 360 shell** | https://360.pano.ai/ | Agency login (same product family) |
| **Contrast: WA DNR public share** | https://wadnr.wildfirewatch.com/ | **Yes** — DNR opted into Pano’s public-feed feature (Jul 2025) |
| IDL Fire Camera map pin | IDL webappviewer marker at Hoodoo coords | Inventory / viewshed only — **not** the live stream |

**Conclusion:** Hoodoo’s live imagery is on **Avista’s Pano Wildfire Watch / Pano 360 tenant**, not ALERTWest and not a public page. Former public-looking `/station/{uuid}` links on `avista.wildfirewatch.com` now 404; IDL has not published a live URL for any Avista/Pano Idaho stations (Gold Mountain, Gold Cup, Hoodoo, etc.). Access path for responders: Avista/Pano agency onboarding (Avista reported 340+ camera user accounts by fall 2025).


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
