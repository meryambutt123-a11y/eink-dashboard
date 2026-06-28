# eink·dash

A personal weather + events dashboard styled after a real e-ink display panel. Runs entirely in the browser, installs like an app on your phone, and costs nothing to host or run.

![status](https://img.shields.io/badge/cost-%240-1a1a18) ![status](https://img.shields.io/badge/backend-none-1a1a18) ![status](https://img.shields.io/badge/stack-HTML%20%2F%20CSS%20%2F%20JS-1a1a18)

```
┌──────────────────────────────────────────────────    ┐
│ ● LIVE                                   08:45 AM    │
├───────────────┬────────────────────────────────────  ┤
│               │ TODAY                                │
│     57°       │ Thunderstorm. High 41°, low 31°.     │
│  feels like…  │ ⛈ Thunderstorm                      │
├───────────────┴────────────────────────────────────  ┤
│ 5-DAY   TODAY   MON   TUE   WED   THU                │
├──────────────────┬───────────────────────────────────┤
│ NEARBY            │ UPCOMING                         │
│ Pindi Bhattian 35°│ ○ not connected         [connect]│
│ Tandlianwala  36° │ No upcoming events               │
├──────────────────┴───────────────────────────────────┤
│ CONDITIONS — rain / air / sky                        │
└──────────────────────────────────────────────────────┘
```

## Why this exists

Saw a $40 Seeed Studio e-ink panel running a homemade weather dashboard and wanted the same thing — minus the hardware. This is that dashboard, rebuilt as a website: same monochrome panel-and-grid look, same data density, but it lives on a phone or laptop screen instead of a physical display, and it's free.

## What it shows

- **Current conditions** — temperature, feels-like, sunrise/sunset, your location (auto-detected via GPS)
- **Today's forecast** — plain-language summary and a high/low tag
- **5-day outlook** — condition icon, high, low for each day
- **Nearby areas** — live conditions at a few surrounding points
- **Rain / air / sky stats** — 24h and 7-day rainfall, rain chance, humidity, wind, UV index, moon phase, pressure, visibility
- **Upcoming events** — synced from your real Google Calendar, plus a manual add/remove list for anything else
- A subtle **screen-flicker animation** on refresh, because real e-ink does that too

## Stack

No backend, no database, no build step. One HTML file plus a tiny PWA wrapper:

| File | Purpose |
|---|---|
| `index.html` | The entire dashboard — markup, styles, and logic in one file |
| `manifest.json` | Makes the page installable as a home-screen app (PWA) |
| `sw.js` | Service worker — caches the app shell so it loads instantly and works offline |
| `icon-192.png`, `icon-512.png` | Home-screen app icons |

**Data sources, all free, no API key required:**
- [Open-Meteo](https://open-meteo.com/) — weather and forecast data
- [OpenStreetMap Nominatim](https://nominatim.org/) — reverse geocoding (turns GPS coordinates into a place name)
- [Google Calendar API](https://developers.google.com/calendar) — your real events, via OAuth (requires your own free Google Cloud client — see setup)

## Setup

### 1. Host it for free

Push this folder to a GitHub repo, then:

1. **Settings → Pages**
2. Source: **Deploy from a branch** → Branch: `main`, folder: `/ (root)` → **Save**
3. Your live URL appears after ~30–60 seconds: `https://meryambutt123-a11y.github.io/eink-dashboard/`

### 2. Connect Google Calendar (optional)

Calendar sync needs your own OAuth client — it's free but takes a few minutes on Google's side:

1. [Google Cloud Console](https://console.cloud.google.com/) → new project → enable **Google Calendar API**
2. **OAuth consent screen** → External → add yourself as a **test user**
3. **Credentials → Create OAuth client ID** → Web application
   - Authorized JavaScript origin: `https://meryambutt123-a11y.github.io`
   - Authorized redirect URI: `https://meryambutt123-a11y.github.io/eink-dashboard/`
4. Copy the **Client ID** and paste it into `index.html`, replacing the existing `GOOGLE_CLIENT_ID` value

Since the app stays unpublished (normal for a personal project), Google will show an "unverified app" screen the first time — click **Advanced → Go to eink dashboard** to continue. Calendar sessions last about 7 days; the dashboard shows a countdown and a one-tap **sync** button to reconnect.

### 3. Install it on your phone

Open your GitHub Pages URL in your phone's browser → menu → **Add to Home Screen**. It opens full-screen, with its own icon, no browser bar.

## Customizing

Everything lives in `index.html`:

- **Colors** — edit the CSS variables at the top of the `<style>` block (`--paper`, `--ink`, `--line`, etc.)
- **Default location** — change the fallback coordinates in `initLocation()` if GPS access is denied
- **Refresh rate** — weather auto-refreshes every 15 minutes; change the interval at the bottom of the script
- **Nearby areas** — adjust the lat/lon offsets in `getNearbyCities()`

## Known limitations

- Manually added events are stored per-browser, per-device — they don't sync between your phone and laptop
- Google Calendar sessions expire after ~7 days (unpublished-app limitation) and need a one-tap reconnect
- Nearby-area conditions are approximate points around your location, not official city centers

## Credits

Inspired by [@kyleturman](https://x.com/kyleturman)'s e-ink dashboard build. Weather data from Open-Meteo, geocoding from OpenStreetMap.
