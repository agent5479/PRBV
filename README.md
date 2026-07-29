# Patons Rock Beach Villas — Arrivals Board

A single-page arrivals dashboard for **Patons Rock Beach Villas** (PRBV). It shows today’s check-ins (and ongoing stays) for four villas, using New Zealand time.

Open [`index.html`](index.html) in a browser, or host it as a static site.

## What it does

- Displays **Villa 1–4** in a card grid
- Highlights **today’s arrivals** with guest name and check-in/out times (2PM–10AM)
- Shows **ongoing stays** when a villa is occupied but has no new check-in today
- Shows a clear message when there are **no new check-ins** for that villa
- Keeps the header date in **Pacific/Auckland (NZT)**, even if the device clock or timezone is wrong
- Cycles a visual highlight across villa cards for TV / kiosk display
- Includes a welcome note with contact phone for guests

## How it works

Booking data comes from public **iCal (.ics)** feeds:

1. **Beds24** (tried first — source of truth for room assignments)
2. **Google Calendar** (fallback if Beds24 fails)

Because browsers block cross-origin calendar requests, the page tries several **CORS proxies** in order. Feed URLs are configured in `index.html` under `villaFeeds`.

A Google-first copy is kept as [`legacy-index.html`](legacy-index.html) for comparison only. Do not use it as the live reception board.

“Today” is resolved preferentially via [timeapi.io](https://timeapi.io) for `Pacific/Auckland`, so a wrong local PC date does not skew arrivals. If that fails, Luxon falls back to the device clock in NZ time.

### Refresh behaviour

| Trigger | Interval / rule |
|---|---|
| Initial load | On page open |
| Auto refresh | Every **60 minutes** |
| Manual Refresh button | **2-minute** cooldown |
| Header date only | Every **15 minutes**, and when the tab becomes visible again |

The footer shows last refresh time (NZT) and per-villa feed status (arrival / ongoing / no booking / error).

## Stack

- Plain HTML + vanilla JavaScript (no build step)
- [Tailwind CSS](https://tailwindcss.com/) (CDN)
- [Luxon](https://moment.github.io/luxon/) for NZ date/time handling
- [Font Awesome](https://fontawesome.com/) icons

## Run locally

No install required:

```bash
# Option A — open the file directly
start index.html   # Windows
# open index.html  # macOS

# Option B — simple local server (recommended if a browser blocks file:// fetches)
npx serve .
# or: python -m http.server 8080
```

Then open the URL shown (e.g. `http://localhost:3000` or `:8080`).

## Deploy

Any static host works (GitHub Pages, Netlify, Cloudflare Pages, S3, etc.). Publish `index.html` as the site root.

Example with GitHub Pages: enable Pages on the `main` branch, root directory.

## Configuration

Edit these values in `index.html` if you need to change behaviour:

- **`villaFeeds`** — Google Calendar and Beds24 iCal URLs per villa
- **`corsProxies`** — proxy list used to fetch ICS feeds
- **`nzTimeApiUrl`** — authoritative NZ wall-clock source
- **`REFRESH_COOLDOWN`** / auto-refresh interval — rate limiting
- Welcome message / phone number in the `.welcome-card` section

> **Note:** Calendar tokens in the page are required for the public ICS links. Treat the repo as containing feed credentials; rotate Beds24/Google tokens if the repo visibility or access changes.

## Status meanings (per villa)

| State | Meaning |
|---|---|
| Arrival | Guest checking in today |
| Ongoing | Stay in progress; no new check-in today |
| No booking | No arrival or occupied stay found for today |
| Error | All feed + proxy attempts failed |

## License

Private project for Patons Rock Beach Villas operations. All rights reserved unless otherwise stated by the owner.
