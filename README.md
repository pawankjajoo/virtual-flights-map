# Virtual Flights Map

A comprehensive 2D interactive map for real-time flight tracking across **ALL** major flight simulation platforms and virtual ATC networks.

## Overview

Virtual Flights Map provides a unified interface for tracking pilots and air traffic controllers across multiple flight simulation communities simultaneously. Whether you fly on VATSIM, IVAO, POSCON, or other virtual flight simulation networks, this map consolidates all real-time traffic into one beautiful, responsive interface.

## What's New (April 2026)

### CORS Proxy & Cybersecurity Hardening
- **Cloudflare Worker CORS Proxy**: All cross-origin API calls route through a dedicated Cloudflare Worker (`pss-cors-proxy`) ensuring the map works reliably inside Wix iframe embeds and across all deployment contexts
- **Origin Allowlist**: The proxy validates requests against a strict allowlist of approved origins (`pushstartsims.com`, `maps.pushstartsims.com`, and Wix CDN domains) preventing unauthorized third-party usage
- **Referer Verification**: Null-origin requests (common in iframes) are validated by checking the HTTP Referer header against `pushstartsims.com`, blocking abuse from unknown sites
- **Circuit Breaker Pattern**: The resilient fetch engine (`pfetch`) implements a per-domain circuit breaker — after 3 consecutive failures, requests to that domain are paused for 60 seconds to avoid hammering failing endpoints
- **Request Deduplication**: Identical concurrent requests are automatically deduplicated via an in-flight map, preventing redundant API calls

### Day/Night Auto-Theming
- **Automatic Theme Detection**: Map auto-selects dark or light theme based on user's local time (Day: 6:30 AM – 6:30 PM, Night: 6:30 PM – 6:30 AM)
- **Dynamic Map Tiles**: Switches between CartoDB Dark and CartoDB Light tile layers to match the active theme
- **Full UI Theming**: Complete dual color palette with CSS custom properties — every UI element (panels, buttons, text, markers) adapts to the active theme
- **Manual Override**: Users can toggle day/night manually; preference is persisted in localStorage
- **Theme-Aware Markers**: Aircraft labels and text shadows adjust for readability in both themes

### API Call Efficiency for Concurrent Users
- **Batch Endpoint**: Single `/batch` worker endpoint replaces 2–4 separate API requests per poll cycle, dramatically reducing request count
- **Worker-Side Caching**: The Cloudflare Worker caches responses for 12 seconds — all concurrent users share the same cached data, meaning 30+ users generate the same load as 1 user
- **Adaptive Polling Strategy**: Poll intervals adjust based on tab visibility (30s active, 60s idle, paused when hidden) to conserve bandwidth
- **Flight-Phase-Aware Polling**: Ground/parked aircraft (< 10 kts) refresh at 120s intervals; airborne traffic at 30s — reducing unnecessary updates
- **Smooth Aircraft Interpolation**: 30 FPS position interpolation between API polls using heading + groundspeed projection, so aircraft move smoothly without requiring faster polling
- **Exponential Backoff Retries**: Failed requests retry with increasing delay, preventing thundering herd on transient errors
- **Free-Plan Budget Optimized**: Engineered for ~2 requests/min per active user, supporting 30–35 concurrent users on Cloudflare's free tier (100k requests/day)

### Domain Redirect Fix
- **pushstartsim.com → pushstartsims.com**: Configured GoDaddy 301 permanent redirect so the domain without the "s" properly forwards over HTTPS to the main site

## Features

### Real-Time Flight Tracking
- **Multi-Network Support**: Track flights from VATSIM, IVAO, POSCON, and other compatible networks — which means coverage of ALL online players from X-Plane (all editions), Microsoft Flight Simulator (all editions) and P3D Software
- **Live Aircraft Markers**: See all active pilots with callsigns, positions, and headings
- **ATC Coverage**: Interactive ATC stations showing active controllers worldwide
- **Live Statistics**: Real-time pilot and controller counts with connection status

### Interactive Controls
- **Aircraft Search**: Find any flight by callsign, departure, or arrival airport
- **Flight Information Panel**: Click any aircraft for detailed info (altitude, speed, heading, route, flight plan)
- **Layer Toggles**: Show/hide aircraft, ATC stations, routes, weather overlays, navaids, airways, FIRs, SIGMETs
- **Map Navigation**: Pan, zoom, and explore routes interactively
- **Responsive Design**: Works seamlessly on desktop, tablet, and mobile devices

### Visual Features
- **Day/Night Auto-Theme**: Automatically switches between dark cockpit and light daytime themes
- **Aircraft Type Classification**: Color-coded markers across 7 categories (Heavy, Medium, Regional, Light Jet, GA, Helicopter, Military)
- **Altitude-Aware Color Bands**: Marker colors shift based on flight level (GND/LOW/CLB/CRZ/HI/UHI)
- **Dynamic Aircraft Sizing**: Marker size scales by aircraft category (Heavy: 14px, Medium: 11px, GA: 8px)
- **ATC Circle Coverage**: Visual representation of controller coverage areas
- **Smooth Animations**: 30 FPS aircraft interpolation with fluid UI transitions

### Live Weather System
- **RainViewer Radar Overlay**: Real-time precipitation radar tiles with smooth rendering
- **NOAA METAR Integration**: Aviation weather from the Aviation Weather Center covering global airports
- **Flight Category Display**: VFR / MVFR / IFR / LIFR weather classification at airports
- **Zoom-Level Filtering**: Low zoom shows only severe weather; high zoom reveals all categories

### Advanced Capabilities
- **Multi-Network Aggregation**: Phased fetch strategy — VATSIM every 30s, full network sweep (VATSIM + IVAO + POSCON) every 90s
- **Resilient Fetch Engine**: Circuit breaker, retry with backoff, request deduplication, and 15-second abort timeouts
- **ROI Tool Integration**: Region of Interest analysis tools for flight analysis
- **Frequency Search**: Look up ATC frequencies and controller information
- **Flight Plan Parsing**: Detailed routing and flight plan information
- **ATIS Voice Integration**: Text-to-speech for ATIS information

## Technical Stack

### Frontend
- **Leaflet.js v1.9.4**: Powerful open-source mapping library (free, no API key required)
- **Custom Vector Tiles**: CartoDB Dark/Light with automatic theme switching
- **Responsive CSS**: Mobile-first design with Flexbox and CSS Grid
- **Modern JavaScript**: ES6+ with async/await for data fetching

### Styling
- **Day/Night Dual Theme**: CSS custom properties for complete theme switching
- **Inter & SF Mono**: Professional typography for clarity and readability
- **Glassmorphism**: Backdrop blur effects for modern UI components
- **Smooth Animations**: 0.15s–0.3s transitions for fluid interactions

### Data Integration
- **VATSIM Network**: Real-time pilot, ATC, and transceiver data
- **IVAO Network**: Normalized pilot and controller data
- **POSCON Network**: Real-time pilot and ATC data
- **CORS Proxy**: Cloudflare Worker with origin validation, caching, and batch support
- **Real-Time Updates**: Adaptive 30s/60s/120s polling based on context

### Architecture
- **100% Client-Side**: No backend server required
- **Self-Contained**: Single HTML file with all assets
- **No Build Process**: Drop and run — works with any static web server
- **No External Dependencies**: All required libraries loaded via CDN
- **Batch API Design**: Worker aggregates multiple network APIs into single response

## Getting Started

### Installation
1. **Clone the repository**:
   ```bash
   git clone https://github.com/pawankjajoo/Virtual-Flights-Map.git
   cd Virtual-Flights-Map
   ```

2. **Open in Browser**:
   - Simply open `index.html` in any modern web browser
   - No server or build process required
   - Works locally by opening the file directly

### Browser Requirements
- Modern browser with ES6+ support (Chrome 51+, Firefox 55+, Safari 11+, Edge 79+)
- JavaScript enabled
- CORS support for API data fetching
- Sufficient network bandwidth for real-time updates

### No Configuration Needed
The application works out-of-the-box with default settings optimized for performance and clarity. No API keys, authentication, or configuration files required.

## Usage Guide

### Basic Navigation
- **Pan Map**: Click and drag to move around the map
- **Zoom**: Scroll wheel to zoom in/out or use map controls
- **Reset View**: Click the reset button to return to default view

### Searching Flights
1. Click the search box at the top of the screen
2. Type a callsign (e.g., "DAL123"), departure or arrival airport
3. Select from dropdown results (up to 8 matches)
4. Map will automatically navigate to the selected flight

### Viewing Flight Details
1. Click any aircraft marker on the map
2. Info drawer opens showing:
   - **Flight Information**: Callsign, aircraft type, status
   - **Position & Movement**: Latitude, longitude, altitude, heading, ground speed
   - **Flight Plan**: Departure airport, arrival airport, cruise altitude, route
   - **Remarks**: Special notes and flight information

### Viewing ATC Information
1. Click any ATC circle marker
2. Controller details appear showing:
   - **Frequency**: Active ATC frequency
   - **Position**: Airport or airspace coverage
   - **Controller Callsign**: ATC identifier
   - **ATIS Information**: Voice readout of ATIS via text-to-speech

### Layer Control
Toggle visibility of different map elements:
- **Aircraft Toggle**: Show/hide all flight markers
- **ATC Toggle**: Show/hide controller coverage circles
- **Routes Toggle**: Show/hide flight paths and routes
- **Weather Toggle**: Overlay precipitation radar and METAR data
- **Navaids / Airways / FIRs / SIGMETs**: Additional aviation overlays

## Data Sources

| Network | Source | Update Frequency |
|---------|--------|------------------|
| VATSIM | data.vatsim.net/v3/vatsim-data.json | Every 30s (primary) |
| IVAO | api.ivao.aero/v2/tracker/whazzup | Every 90s (full sweep) |
| POSCON | POSCON API endpoints | Every 90s (full sweep) |
| Weather | RainViewer + NOAA AWC | On demand |

**Rate Limiting**: Conservative polling with adaptive intervals to stay within API limits and Cloudflare free-tier budget.

## Performance

- **Optimized for 30–35 concurrent users** on Cloudflare free tier via batch caching
- **Smooth 30 FPS interpolation**: Aircraft positions animate between API polls
- **Real-time Updates**: Adaptive 30s–120s refresh based on flight phase and tab visibility
- **Responsive**: Works smoothly on mobile devices and low-bandwidth connections
- **Bundle Size**: ~234 KB single HTML file with embedded CSS/JS

## Security

- **CORS Proxy Origin Allowlist**: Only approved origins can use the proxy
- **Referer Validation**: Null-origin iframe requests verified via Referer header
- **Circuit Breaker Protection**: Prevents cascading failures from hammering failing APIs
- **No Backend**: All data processing client-side, no sensitive data transmission
- **No Credentials**: Anonymous public API access only
- **Content Security Policy Compatible**: Can be hosted in CSP-restricted environments

## Browser Compatibility

| Browser | Minimum Version | Status |
|---------|-----------------|--------|
| Chrome | 51+ | ✓ Full Support |
| Firefox | 55+ | ✓ Full Support |
| Safari | 11+ | ✓ Full Support |
| Edge | 79+ | ✓ Full Support |
| Opera | 38+ | ✓ Full Support |
| IE 11 | — | ✗ Not Supported |

## Deployment

### Static Web Server
```bash
# Copy files to any static web server
# Apache, Nginx, GitHub Pages, Vercel, Netlify all work

# Example with Python
python -m http.server 8000
# Then open http://localhost:8000/index.html
```

### Direct File Opening
```bash
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
```

### Wix Embed
The map is designed to work inside Wix HTML embed iframes via the CORS proxy. Embed `index.html` on any Wix page and it will function out of the box.

## Customization

### Theme Customization
The application uses CSS custom properties with full day/night palettes. Edit these in the `<style>` section:
```css
:root[data-theme="night"] {
  --bg: #090d18;         /* Dark background */
  --acc: #22d3ee;        /* Cyan accent */
  --grn: #34d399;        /* Active/success */
}
:root[data-theme="day"] {
  --bg: #f0f4f8;         /* Light background */
  --acc: #0891b2;        /* Teal accent */
  --grn: #059669;        /* Active/success */
}
```

### Configuration
Internal configuration values are defined in JavaScript:
- `POLL_ACTIVE`: Data refresh when tab active (default: 30000ms)
- `POLL_IDLE`: Data refresh when idle (default: 60000ms)
- `CORS_PROXY`: CORS proxy URL for API calls
- Networks: VATSIM, IVAO, POSCON all enabled by default

## Contributing

Contributions welcome! Areas for enhancement:
- Additional flight simulation network adapters
- Weather overlay improvements
- Flight trail visualization
- 3D terrain rendering
- Performance optimizations
- Mobile UI improvements

## License

MIT License — Developed by Pawan K Jajoo | Pushstart Simulators

## Support & Community

- For issues and feature requests, open an issue on GitHub
- Join flight simulation communities on VATSIM, IVAO, or POSCON
- Share feedback and suggestions for improvements

## Version History

- **v1.1** (April 2026): CORS proxy cybersecurity hardening, day/night auto-theming, batch API optimization for concurrent users, domain redirect fix, live weather integration, aircraft interpolation engine
- **v1.0** (March 2026): Initial release with Leaflet.js mapping, multi-network tracking, live ATC, flight search, responsive design

---

**Made with ❤️ for the virtual aviation community** | Pushstart Simulators
