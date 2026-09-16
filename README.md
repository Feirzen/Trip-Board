# Trip Board

A small web page that shows which trips are being watched, what each one should cost, and every price seen so far. Claude writes the data files; the page just reads them.

Live page: enable GitHub Pages on the `main` branch, root folder.

## Files

- `index.html` — the whole page. No build step, no dependencies.
- `data/trips.json` — the trips, their targets, and their price logs.
- `data/alerts.json` — deals that cleared the bar and got emailed.

## trips.json

Each trip:

| Field | What it is |
| --- | --- |
| `id` | Short slug. Alerts point at this. |
| `name` | Internal name, used until a destination is picked. |
| `destination` | City or region once decided, otherwise `null`. |
| `airports` | Departure airports to watch. |
| `window` | Human readable travel window. |
| `flexibility` | How much the dates can move. |
| `status` | `planning`, `watching`, `booked`, or `dead`. Dead trips are hidden. |
| `flightPlan` / `lodgingPlan` | Points or cash, per trip. |
| `targets` | `allInTwo`, `flightCashTwo`, `flightPointsEach`, `lodgingPerNight`. Numbers only, no currency symbols. `null` means not set. |
| `targetSetOn` | Date the current target was set, `YYYY-MM-DD`. |
| `reviewAfterDays` | How long to hold that target before reconsidering it. |
| `floorAllInTwo` | The most that would ever be paid. Targets never drift past this. |
| `trackedRoutes` | Routes set up in Google Flights or seats.aero. |
| `notes` | Anything worth remembering about this trip. |
| `priceLog` | Every price seen. |

A price log entry:

```json
{ "date": "2026-09-16", "route": "SLC-SJD", "value": 412, "unit": "usd", "source": "Google Flights alert" }
```

`unit` is `usd` or `points`. The lowest value in the log is marked as the best seen and dotted on the sparkline.

## How the target moves

A target is held for `reviewAfterDays`. Once that passes, the page shows a "Target review due" chip and the next run reconsiders it against the logged prices: hold if the market moved down, loosen toward `floorAllInTwo` if the logged prices say the original number was never realistic. It never loosens past `floorAllInTwo`, and it does not change inside the hold window.

## alerts.json

```json
{ "date": "2026-09-16", "grade": "S", "tripId": "graduation-may-2027", "title": "...", "summary": "...", "deadline": "2026-09-20", "link": "https://..." }
```

Newest first is handled by the page, so order in the file does not matter.
