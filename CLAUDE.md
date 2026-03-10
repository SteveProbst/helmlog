# CLAUDE.md — HelmLog

## Project Overview

HelmLog is a single-page, client-side web application for sailing navigation and voyage logging. Built for **True North Sailing** (Jersey City, NJ), it tracks position, weather, speed, and waypoints during sailing trips. The entire application lives in a single `index.html` file (≈2,257 lines) with no build step, no backend, and no package manager.

## Repository Structure

```
helmlog/
├── index.html    # The entire application (HTML + CSS + JS in one file)
├── LICENSE        # MIT License
└── CLAUDE.md      # This file
```

There are no separate source directories, config files, or build artifacts.

## Tech Stack

- **Pure HTML/CSS/JavaScript** — no frameworks, no build tools
- **Leaflet.js v1.9.4** (CDN) — interactive maps
- **Google Fonts** (CDN) — Playfair Display, Source Sans 3, JetBrains Mono
- **Open-Meteo API** — weather forecasts (free, no API key)
- **Browser APIs** — Geolocation, Wake Lock, localStorage, Web Share

## How to Run

Open `index.html` in a browser. No server, install, or build step required. For GPS features, serve over HTTPS or use localhost.

## Architecture

### File Organization

The single `index.html` is organized in three sections:

1. **CSS** (lines ~12–700) — Styles using CSS custom properties defined in `:root`
2. **HTML** (lines ~700–1750) — Tab-based UI with panels for each feature
3. **JavaScript** (lines ~1750–2257) — Vanilla JS organized by feature with `// ── Section ──` comment headers

### JavaScript Sections

| Section | Line | Purpose |
|---|---|---|
| Tabs | ~1751 | Tab switching UI |
| Clock | ~1764 | Real-time clock display |
| GPS | ~1772 | Geolocation watch start/stop |
| Wake Lock | ~1783 | Prevents screen sleep on iOS |
| Resume Detection | ~1811 | Handles app returning from background |
| Weather | ~1836 | Open-Meteo API fetch and display |
| Tracking | ~1897 | Start/stop trip tracking, auto-log intervals |
| Waypoint | ~1970 | Manual waypoint entry modal |
| Log | ~1979 | Render log entries list |
| Map | ~1992 | Leaflet map init, markers, track lines |
| Stats | ~2027 | Distance (Haversine), duration, speed stats |
| Save/Load | ~2031 | JSON/CSV export, localStorage persistence |
| Learn: Accordion | ~2043 | Collapsible reference sections |
| Knot Animation Engine | ~2047 | SVG-based interactive knot tutorials |
| Preset Waypoints | ~2241 | Harbor marks and navigation points |
| Helpers | ~2247 | `degToCompass()`, `showToast()`, utilities |
| Init | ~2252 | App initialization on page load |

### Key Data Structure

```javascript
trackData = {
  tripName: "",
  startTime: null,       // timestamp
  entries: [{
    timestamp: "",       // ISO string
    type: "auto" | "waypoint",
    name: "",
    position: { lat, lng },
    speed: 0,            // knots
    heading: 0,          // degrees
    weather: {
      windSpeed, windDirection, windDirLabel,
      gusts, conditions, temp, humidity, pressure, seaState
    },
    notes: ""
  }]
}
```

Data is persisted to `localStorage` under the key `helmlog_data`.

### CSS Design System

Color variables follow a `--tn-*` naming convention (True North):
- Dark palette: `--tn-navy`, `--tn-dark`, `--tn-blue`, `--tn-steel`
- Light palette: `--tn-ocean`, `--tn-sky`, `--tn-light`, `--tn-pale`, `--tn-white`
- Accents: `--tn-gold`, `--tn-red`, `--tn-green`, `--tn-warm`

Typography: `--font-display` (headings), `--font-body` (text), `--font-mono` (data)

## Development Guidelines

### No Build Tools

There is no linter, formatter, test framework, or CI pipeline. Changes are made directly to `index.html`.

### Code Style

- Vanilla JavaScript using `var` declarations and function expressions
- No ES6 modules — all code shares global scope
- Dense single-line formatting for simple functions
- Multi-line formatting with 2-space indentation for complex functions
- Section dividers use `// ── Section Name ──` pattern

### When Modifying Code

- **CSS changes**: Edit within the `<style>` block; use existing `--tn-*` variables
- **HTML changes**: Add to the appropriate tab panel (`data-panel="..."` attribute)
- **JS changes**: Add functions near the relevant `// ── Section ──` comment
- **New features**: Follow the existing tab/panel pattern for UI; add a new section comment for JS
- Keep everything in `index.html` — do not split into separate files unless explicitly asked

### Key Functions to Know

- `switchTab(name)` — UI navigation between panels
- `startTracking()` / `stopTracking()` — trip session control
- `logEntry(type, name, notes)` — records a position with weather
- `fetchWeather(lat, lng)` — calls Open-Meteo API
- `renderLog()` / `renderMap()` / `updateStats()` — refresh UI displays
- `saveLS()` / `loadLS()` — localStorage read/write
- `hvNM(a,b,c,d)` — Haversine distance in nautical miles
- `showToast(msg)` — user notification

### Export Formats

- **JSON**: Full `trackData` object serialized with `JSON.stringify`
- **CSV**: Flattened entries with headers for spreadsheet import
- Uses Web Share API when available, falls back to download

## Git Workflow

- Primary branch: `main`
- No branch protection, CI checks, or automated testing
- Commit messages are typically brief descriptions of changes
