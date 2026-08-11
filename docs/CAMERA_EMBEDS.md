# FireWatch camera embeds

The camera matrix mixes **link-outs** with an optional **Idaho 511 camera feed wall**.

## Camera matrix (always visible)

Public feed cards open provider pages in a new tab:

| Camera | Link target | Notes |
|--------|-------------|-------|
| **Hoodoo Mountain** | [Avista Wildfire Watch station](https://avista.wildfirewatch.com/station/8923711853cc4598ae76cc8ccae1393c) | Avista / Pano AI · Bonner County · public after ToS |
| **Gold Mountain · Sagle** | [Avista station](https://avista.wildfirewatch.com/station/635b1d09b58342638f7f78b8a404bea9) | Avista / Pano AI · public after ToS |
| **Gold Cup · Laclede** | [Avista station](https://avista.wildfirewatch.com/station/c28354b46e084947aff4330f8d658b72) | Avista / Pano AI · public after ToS |
| **St. Joe Baldy / Black Mountain** | ALERTWest `cam-console/{id}` | Public live PTZ |
| **FAA / CDA lake / 511** | Provider pages | Embed limits documented below |

Optional nearby Avista station (not yet on the matrix): [Blossom Mountain · Post Falls](https://avista.wildfirewatch.com/station/6e35fedea98640ea9790af4c67554974) (Kootenai).

## Hoodoo Mountain (Avista · Pano AI)

| Field | Value |
|-------|-------|
| Summit | Hoodoo Mountain, Bonner County, ID (~5,091 ft; Blanchard USGS) |
| Coordinates | `48.078877, -116.954031` (matches historic summit / Bonner County repeater site) |
| Owner / provider | Avista / **Pano AI** (IDL spells provider `Pano Al`) |
| Public station id | `8923711853cc4598ae76cc8ccae1393c` |
| IDL status | Active; inventory row created **2026-06-17**; `CameraLiveViewURL` = **null** (IDL still has no live URL field) |
| Supervisory area | POL (IDL) · private landowner (PVT) |

### Site history

- Historic fire lookout (cupola 1923 → L-4 1930s) abandoned by mid-1930s; site became an electronics / communications location ([Idaho Fire Lookouts](https://www.idahofirelookouts.com/cda-region/hoodoo-mountain-lookout/)).
- Longstanding Bonner County VHF repeater location (~48.08053, -116.9536).
- Avista contracted **Pano AI** for wildfire detection cameras (WA UTC / Idaho WMP testimony): 360° panoramas ~every 1–2 minutes, Pano Intelligence Center verification, agency onboarding. Avista reported 10 cameras in 2024 (3 Idaho / 7 Washington) and further builds afterward; coordinates placements with IDL and the Idaho Fire Camera Interoperability Committee.

### Where the live stream actually lives

| Surface | URL | Public? |
|---------|-----|---------|
| **Avista Wildfire Watch (public)** | https://avista.wildfirewatch.com/ | **Yes** — view after accepting ToS; no login |
| **Station deep links** | `/station/{id}` (see table above) | **Yes** — GCS may return HTTP 404 with the SPA shell; browsers still boot React Router and show the station |
| **Public stations API** | `https://avista.api.wildfirewatch.com/public/camera-stations?pageSize=1000` | Lists Idaho stations + coords (CORS from Avista origin) |
| **Pano 360 shell** | https://360.pano.ai/ | Agency login (operator product) |
| **Contrast: WA DNR public share** | https://wadnr.wildfirewatch.com/ | Same Pano public-feed family |
| IDL Fire Camera map pin | IDL webappviewer marker at Hoodoo coords | Inventory / viewshed only — **not** the live stream |

**Conclusion:** Hoodoo / Gold Mountain / Gold Cup live imagery is on **Avista’s public Wildfire Watch tenant** (`avista.wildfirewatch.com`). FireWatch matrix cards open the station URLs directly. IDL still omits `CameraLiveViewURL` for these rows. Agency/operator tooling remains on Pano 360 / authenticated tenants; the public SPA is ToS-gated only. Do not iframe Avista (`frame-ancestors 'none'`).


## Camera feed wall (collapsed by default)

| Source | How | Notes |
|--------|-----|--------|
| **Idaho 511** corridor cams | `https://511.idaho.gov/map/Cctv/{imageId}` JPEG snapshots | Loads only when the wall is expanded; auto-refresh ~90s while open. Full map is `https://511.idaho.gov/map` |

Toggle control: `#camWallToggle` / `#cameraWall`.

## Link-only (matrix + providers)

| Source | Why |
|--------|-----|
| **ALERTWest** (St. Joe Baldy, Black Mtn / console 8598) | Cloudflare Stream iframes did not play reliably in the dashboard — open the official `cam-console/{id}` instead |
| **Avista WildfireWatch** | `Content-Security-Policy: frame-ancestors 'none'` — open station links in a new tab |
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

## Maintaining Avista station IDs

Re-check with:

```text
GET https://avista.api.wildfirewatch.com/public/camera-stations?pageSize=1000
Origin: https://avista.wildfirewatch.com
```

Station URLs are `https://avista.wildfirewatch.com/station/{id}`.
