# Roadmap: Eclipse Chaser

This roadmap translates the high-level vision into concrete phases and tasks. Each phase builds on the previous one. Feel free to adjust the order or scope based on experience and priorities.

## Phase 1 – Project setup and baseline map

- [ ] ✅ **Repository scaffolding**: create the directory structure, add `package.json`, `.env.example`, `scoring.json`, `public/`, `src/` and a simple React/Vite application that renders a placeholder. Use React with Vite by default.
- [ ] **Install dependencies**: run `pnpm i` (or `npm i`), which pulls in React, MapLibre GL JS, Leaflet (as an alternative), GeoTIFF.js, @turf/turf, proj4, suncalc and Vite's React plugin.
- [ ] **Configure environment variables**: copy `.env.example` to `.env.local` and provide your AEMET API key (if planning to test Spanish weather data).
- [ ] **Base map**: integrate MapLibre GL JS into the React app. Display a simple map with OSM or another public tile layer so that the UI is ready to overlay eclipse and weather layers.

## Phase 2 – Data ingestion and overlays

- [ ] **Eclipse path ingestion**: write a module that loads NASA/GSFC CSV/KML or Xavier Jubier KMZ from `/data/eclipses/YYYYMMDD/` or remote URLs. Convert the data into GeoJSON for MapLibre.
- [ ] **Display eclipse layers**: overlay the centerline, limits and timing polygons/lines on the base map. Add a control to toggle their visibility.
- [ ] **Cloud forecast overlay**: wrap AEMET (Spain) and Open‑Meteo (global) APIs in a common interface. Fetch current cloud-cover forecasts for the relevant time window and display them as a semi‑transparent raster or vector layer on the map.
- [ ] **Climatology overlay (ERA5)**: implement a service that fetches ERA5/ERA5‑Land climatology for twilight hours (e.g. 19–21 local). Use GeoTIFF.js to read COG tiles or preprocessed grids. Render via raster tiles or aggregate to grid squares on the map.

## Phase 3 – Topography and horizon analysis

- [ ] **DEM integration**: download Copernicus DEM GLO‑30 tiles for the region of interest (S3 bucket) and optionally PNOA‑LiDAR for Spain. Convert to COGs if necessary. Use GeoTIFF.js to sample elevation at candidate sites.
- [ ] **Viewshed & horizon**: implement a west‑facing horizon algorithm. For each candidate cell, cast rays in azimuths ~260–300° and compute skyline cutoff angles. Use these values to score horizon suitability (≤1°).
- [ ] **Solar geometry**: use SunCalc (fast) and optionally NREL SPA (precise) to compute solar azimuth and altitude at eclipse contact times for each candidate site. Ensure that solar altitude meets the `min_solar_alt_deg` threshold.

## Phase 4 – Scoring and MCDA

- [ ] **Scoring engine**: create a modular scoring function that accepts component sub‑scores (geometry, solar altitude, cloudiness, aerosol, fire danger, topography, access and alternates) and weights (from `scoring.json`). Normalize sub‑scores to [0,1] and compute a weighted sum.
- [ ] **Access & beds**: query OSM’s Overpass API for roads, parking areas, accommodations and amenities near candidate sites. Translate POI density and road quality into an accessibility sub‑score.
- [ ] **Alternate‑site density**: compute the number of other candidate sites within 50 km of each candidate. Higher density → lower risk if your first choice is clouded out.
- [ ] **Ranking & filtering**: produce a ranked list of candidate sites within the configurable radius (default 250 km). Filter out sites that fail hard thresholds (e.g., solar altitude < 2° or horizon cutoff > 1°).

## Phase 5 – Planning tools and exports

- [ ] **Interactive cloud‑dash planner**: allow users to draw or enter their own radius. Visualize all candidate sites within that radius and highlight the top 5 by score.
- [ ] **Turn‑by‑turn links**: generate deeplinks to Google Maps, OpenStreetMap or other navigation services for each top site.
- [ ] **Export features**: provide exports in KML/GPX formats and a PDF “field sheet” summarizing the top sites, including coordinates, timing and scoring breakdown.

## Phase 6 – Server‑assist mode

- [ ] **Server component**: implement a FastAPI (Python) backend that pre‑aggregates heavy rasters (ERA5, CAMS/GFAS, EFFIS) into Cloud‑Optimized GeoTIFFs. Use a queue to refresh data nightly (ERA5) or hourly (CAMS/EFFIS).
- [ ] **Caching layer**: store preprocessed tiles in S3/minio. Provide an API for the front‑end to request tiles by bounding box and time.
- [ ] **Deployment**: containerize the server using Docker. Optionally deploy to a cloud provider (e.g., AWS Lightsail or GCP) for near‑real‑time updates.

## Phase 7 – Extensibility and polish

- [ ] **Pluggable providers**: abstract the weather/aerosol/fire data fetchers so that other meteorological agencies (e.g., DWD, Météo‑France) can be added easily. Encourage contributions from the community.
- [ ] **AHP weighting UI**: create an interface for advanced users to adjust MCDA weights using Analytic Hierarchy Process or direct sliders. Persist custom weights per user.
- [ ] **Offline/PWA build**: bundle the application as a Progressive Web App with offline caching so that users in remote areas can still view downloaded layers and their plan.
- [ ] **Accessibility & UX**: improve styling, responsiveness and accessibility. Add dark/light themes and multi‑language support (starting with English and Spanish).

---

This roadmap is intentionally ambitious; feel free to reorder or down‑scope phases according to time and skills. Make incremental commits as you complete each task, and keep the README updated with new capabilities.
