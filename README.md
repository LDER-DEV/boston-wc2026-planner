# Boston Stadium Isn't in Boston

An intelligent match-day planner for FIFA World Cup 2026 visitors arriving in Boston. Gillette Stadium sits 30 miles south of downtown Boston in Foxborough, MA — and Route 1 there ranks among the deadliest corridors in the state. This Marimo notebook combines eight live public datasets into a single door-to-stadium itinerary built around the match you picked and the hotel you entered.

Built for the Cursor / PyData Boston 2026 hackathon.

## What it does

After a single match pick and an optional starting address, the notebook auto-generates:

- A **personalized itinerary card** with closest Commuter Rail station (name, real street address, walking + driving time), transfer routing if the station isn't on the Foxboro Line, and a door-to-stadium timing chain working backward from kickoff.
- A **live MBTA map** — 9 routes (Red, Orange, Blue, Green B/C/D/E, Silver Line SL1, Foxboro/Franklin) with vehicle positions polled every 15 s and smoothly interpolated client-side at 50 ms ticks.
- A **match-day weather forecast** — live for matches inside Open-Meteo's 16-day window, historical-average fallback for matches farther out.
- A **MBTA reliability snapshot** ranking lines as Most Reliable vs Plan Extra Time.
- A **places map** of hotels, motels, bars, restaurants, public restrooms, and parks pulled live from OpenStreetMap. Restaurants tagged with the visitor's home cuisine are flagged with their country's flag emoji.
- A **family-friendly cities map** with click-through tourist attractions for 21 curated MA cities.
- A **crash-data overlay** of fatal / serious-injury incident counts for the visitor's chosen city.
- A **personalized conclusion** with concrete safety advice tailored to the visitor's starting point.

## Data sources

| Layer | Source | Auth |
|---|---|---|
| Crash records | MassDOT IMPACT Open Data | none |
| Live transit | MBTA V3 API | none (optional key) |
| Weather | Open-Meteo (forecast + archive) | none |
| Places (hotels, food, restrooms) | OpenStreetMap Overpass | none |
| Geocoding + reverse geocoding | OpenStreetMap Nominatim | none |
| Driving routing | OSRM public demo | none |
| Hero city image + intro | Wikipedia REST | none |
| City safety overlay | FBI UCR 2024 (curated) | n/a |

No paid API keys required. All data sources are free and public.

## Quick start

### Prerequisites

- Python 3.10+
- pip

### Install

```bash
git clone https://github.com/Redeem-Grimm-Satoshi/boston-wc2026-planner.git
cd boston-wc2026-planner
pip install -r requirements.txt
```

### Run

In app mode (code hidden, presentation-ready):

```bash
marimo run submission.py
```

In edit mode (for development):

```bash
marimo edit submission.py
```

Both commands serve the notebook at `http://localhost:2718`.

### Optional MBTA API key

The MBTA V3 API rate-limits anonymous requests to 20 req/min per IP. To raise the limit to 1000 req/min (useful when running for a group or polling more aggressively), grab a free key at <https://api-v3.mbta.com> and set:

```bash
export MBTA_API_KEY=your_key_here  # macOS / Linux
$env:MBTA_API_KEY = "your_key_here"  # Windows PowerShell
```

The notebook picks it up automatically — no code changes needed.

## Tech stack

- **[Marimo](https://marimo.io)** — reactive Python notebook + app runtime
- **[Folium](https://python-visualization.github.io/folium/)** — Leaflet-based interactive maps
- **[Altair](https://altair-viz.github.io)** — declarative charts
- **pandas** + **requests** — data wrangling and HTTP
- Embedded JavaScript for live MBTA vehicle interpolation

## Project structure

```
.
├── submission.py        # Main Marimo notebook (run with marimo run)
├── meta.json            # Submission metadata
├── requirements.txt     # Python dependencies
└── README.md            # This file
```

## How the reactive flow works

The notebook is built on Marimo's reactive cell graph. The user-facing flow:

1. **Pick a match** → resolves `selected_match` (gated behind a sticky Start button).
2. **Enter a starting address** → Nominatim geocodes to lat/lon + Wikipedia hero image.
3. **Click Start** → the entire downstream graph hydrates: weather fetch, closest-station resolution, OSRM driving estimate, MBTA bus connection lookup, places map with cuisine flag highlighting, family-friendly city overlay, and personalized conclusion.

Every downstream cell gates on `mo.stop(selected_match is None)` so the page is empty until the visitor commits to a match.

## License

MIT.

## Acknowledgments

- MassDOT IMPACT for open crash data
- MBTA Developer Portal for the V3 API
- OpenStreetMap contributors for Overpass, Nominatim, OSRM
- Open-Meteo for free weather forecasts and historical archives
- FIFA for the World Cup 2026 publicized match schedule
