# eclipse-chaser

**Plan. Chase. See totality.**
An open-source web app that overlays eclipse geometry with **weather, aerosols/smoke, wildfire danger, and topography** to **score** vantage points and recommend the **top 5 sites** within a configurable **250 km “cloud-dash” radius**.

* **Reusability first**: ingest **NASA/GSFC/X. Jubier path data** or Besselian-derived KMZ per event, so the same pipeline works for any eclipse. ([eclipse.gsfc.nasa.gov][1], [xjubier.free.fr][2], [xjubier.free.fr][3])
* **Open data** only: AEMET (Spain) & Open-Meteo forecasts, ERA5/ERA5-Land reanalysis, Copernicus **CAMS** aerosols (dust/smoke), **EFFIS** Fire Weather Index, **Copernicus DEM** / national LiDAR, OSM/Overpass for POIs and access. ([aemet.es][4], [Open Meteo][5], [cds.climate.copernicus.eu][6], [cds.climate.copernicus.eu][7], [confluence.ecmwf.int][8], [Datenportal Europa][9], [registry.opendata.aws][10], [copernicus-dem-30m.s3.amazonaws.com][11], [Plan Nacional de Ortofotografía Aérea][12], [Plan Nacional de Ortofotografía Aérea][13], [OpenStreetMap][14])
* **In-browser geospatial**: MapLibre/Leaflet for maps, GeoTIFF.js for rasters, SunCalc/NREL SPA for solar geometry. ([maplibre.org][15], [leafletjs.com][16], [geotiffjs.github.io][17], [GitHub][18], [GitHub][19], [NREL Dokumente][20])

---

## human written input

This project is mostly (probably >99%) Ai generated. (this is one of the few paragraphs i write myself) 

I have some background knowledge in python and web development, but thats minimal and i do that barely as a side-hustle because i would like such a piece of software. it will most likely not be finished and not be whats promised. But maybe Ai gets better fast enough to help me with this idea.

---

## Features

* **Eclipse layers**
  Load NASA/GSFC path (centerline/limits/timing) or Xavier Jubier KMZ for the target eclipse. ([eclipse.gsfc.nasa.gov][1], [xjubier.free.fr][2])
* **West-horizon finder**
  Ranks **west-facing** viewpoints by computing horizon cut-off and viewshed from DEM/LiDAR tiles (browser or server mode). Uses Copernicus GLO-30 globally; can switch to Spain’s PNOA-LiDAR where available. ([registry.opendata.aws][10], [Copernicus Data Space Ecosystem][21], [Plan Nacional de Ortofotografía Aérea][12])
* **Weather & hazards overlays**
  *Cloud forecasts* (AEMET in ES; Open-Meteo globally), *reanalysis climatology* (ERA5/ERA5-Land), *aerosols/smoke* (CAMS AOD & GFAS), and *fire danger* (EFFIS FWI). ([aemet.es][4], [Open Meteo][5], [cds.climate.copernicus.eu][6], [cds.climate.copernicus.eu][7], [confluence.ecmwf.int][8], [ECMWF][22], [Copernicus Atmosphere][23], [Datenportal Europa][9])
* **Satellite context**
  Optional **EUMETSAT** (Meteosat) real-time imagery and NASA **GIBS** True-Color tiles to eyeball actual cloud decks. ([user.eumetsat.int][24], [EUMETSAT][25], [nasa-gibs.github.io][26])
* **MCDA site scoring**
  Weighted multi-criteria analysis (configurable) across **totality geometry**, **weather**, **haze/smoke**, **topography/horizon**, **vegetation & fire risk**, **access & beds** to output a ranked shortlist and **top-5** within **N km**. (See *Scoring Model*.)
* **Cloud-dash planner**
  Draw a user-set **radius (default 250 km)**, visualize reachable alternates, and export a “Plan A/B/C” with turn-by-turn links.
* **Pluggable data providers**
  A common interface wraps regional weather APIs (e.g., AEMET) and global fallbacks (Open-Meteo), so the repo adapts to **other countries and future eclipses**. ([aemet.es][4], [Open Meteo][5])

---

## Live data sources (default adapters)

| Layer                                    | Primary                                                                                                                                                                        | Global fallback / notes                                                                          |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------ |
| Eclipse path (centerline/limits, timing) | NASA/GSFC interactive & tabular path; Xavier Jubier KMZ for convenience. ([eclipse.gsfc.nasa.gov][1], [eclipse.gsfc.nasa.gov][27], [xjubier.free.fr][2], [xjubier.free.fr][3]) | NSO overview pages reference NASA path data. ([NSO - National Solar Observatory][28])            |
| Cloud forecast                           | **AEMET OpenData** (Spain) ([aemet.es][4])                                                                                                                                     | **Open-Meteo** global, no key. ([Open Meteo][5])                                                 |
| Climatology (hourly, twilight)           | **ERA5 / ERA5-Land** (CDS) ([cds.climate.copernicus.eu][6], [cds.climate.copernicus.eu][7])                                                                                    | Same globally via CDS API. ([cds.climate.copernicus.eu][29])                                     |
| Aerosols (dust/smoke)                    | **CAMS** global composition forecasts; **GFAS** fire emissions. ([confluence.ecmwf.int][8], [ECMWF][22])                                                                       | CAMS docs / product notes. ([Copernicus Atmosphere][30])                                         |
| Fire danger                              | **EFFIS** FWI & anomaly. ([forest-fire.emergency.copernicus.eu][31], [Datenportal Europa][9])                                                                                  | GWIS for global context. ([GWIS][32])                                                            |
| DEM / LiDAR                              | **Copernicus DEM GLO-30**; Spain **PNOA-LiDAR**. ([registry.opendata.aws][10], [copernicus-dem-30m.s3.amazonaws.com][11], [Plan Nacional de Ortofotografía Aérea][12])         | DEM license & DOI guidance. ([Copernicus Data Space Ecosystem][21], [docs.sentinel-hub.com][33]) |
| Base map / POIs                          | **OpenStreetMap** via **Overpass API**. ([OpenStreetMap][14], [dev.overpass-api.de][34])                                                                                       | —                                                                                                |
| Satellite imagery (optional)             | **EUMETSAT EUMETView/Data Store**; **NASA GIBS** WMTS. ([user.eumetsat.int][24], [user.eumetsat.int][35], [nasa-gibs.github.io][26])                                           | VIIRS true-color background. ([NASA Earthdata][36])                                              |

> ⚖️ **Licensing notes**: NASA Earth science data is generally open/public domain with attribution guidance; OSM is **ODbL 1.0**; Copernicus DEM is free-to-use with attribution; AEMET OpenData requires API key and attribution per terms. See **Licenses & Attribution** below. ([NASA Earthdata][37], [NASA][38], [OpenStreetMap][39], [opendatacommons.org][40], [docs.sentinel-hub.com][33], [aemet.es][4])

---

## Architecture

**Front-end (default)**

* **React + MapLibre GL JS** (vector tiles) or **Leaflet** (raster/WMS), **Turf.js** for geospatial ops, **Proj4** for projections, **GeoTIFF.js** for client-side rasters (ERA5 grids, DEM tiles), **SunCalc** (fast) with optional **NREL SPA** (high-precision) for low-altitude Sun geometry near sunset. ([maplibre.org][15], [leafletjs.com][41], [geotiffjs.github.io][17], [GitHub][19], [NREL Dokumente][20])

**Back-end (optional, for heavy lifting)**

* Python **FastAPI** micro-services to pre-aggregate ERA5/ERA5-Land, CAMS/GFAS and EFFIS rasters into tiled Cloud-Optimized GeoTIFFs (COGs); cache by bbox+time; queue workers for reanalysis downloads from **CDS**; S3-compatible storage (minio). ([cds.climate.copernicus.eu][6], [cds.climate.copernicus.eu][29])

**Why two modes?**

* **Browser-only** is great for portability and hackability.
* **Server-assist** avoids big downloads on mobile and enables hourly updates for aerosols/smoke & FWI.

---

## Scoring Model (MCDA)

**Goal**: rank candidate sites and output the **Top 5** within a **250 km** configurable radius.

**Criteria (defaults)**

* *Geometry* (duration, distance to centerline, contact times) — 0.25
* *Solar altitude* at totality (°) — 0.10
* *Cloudiness* (19:00–21:00 local) from forecast + ERA5 climatology — 0.20 ([cds.climate.copernicus.eu][6])
* *Aerosols/haze/smoke* (CAMS AOD; GFAS for active fires) — 0.10 ([confluence.ecmwf.int][8], [ECMWF][22])
* *Fire danger* (EFFIS FWI 90-percentile/forecast) — 0.05 ([forest-fire.emergency.copernicus.eu][31])
* *Topography & horizon* (west-facing, skyline cutoff ≤ 1°) — 0.15 (DEM/LiDAR) ([registry.opendata.aws][10], [Plan Nacional de Ortofotografía Aérea][12])
* *Access & beds* (road classes, OSM POI density) — 0.10 (Overpass) ([OpenStreetMap][14])
* *Alternate-site density* within 50 km — 0.05

Each sub-score is normalized to \[0, 1]; **Weighted Sum** ⇒ total score, then filter by **reachability radius**.

**Config example** (`scoring.json`)

```json
{
  "radius_km": 250,
  "weights": {
    "geometry": 0.25, "solar_alt": 0.10, "cloud": 0.20,
    "aerosol": 0.10, "fire": 0.05, "topography": 0.15,
    "access": 0.10, "alternates": 0.05
  },
  "thresholds": { "min_solar_alt_deg": 2.0, "max_horizon_cut_deg": 1.0 }
}
```

---

## Eclipse-Facing Horizon Analysis

* **DEM first**: sample west-azimuth rays (≈260°–300°) from candidate cell; compute skyline and **horizon cutoff**; score higher when cutoff ≤ 1°. Works globally on **Copernicus DEM GLO-30**; Spain users can opt into **PNOA-LiDAR** (0.5–2 m) for micro-relief & tree lines. ([registry.opendata.aws][10], [Plan Nacional de Ortofotografía Aérea][12])
* **Solar geometry**: at totality timestamp, compute Sun azimuth/altitude using **SunCalc** (fast) or **NREL SPA** (precision near low Sun). ([GitHub][19], [NREL Dokumente][20])

---

## Data overlays (visual)

* **Eclipse**: path polygon (limits/centerline), time contours at 10-minute intervals. ([eclipse.gsfc.nasa.gov][1])
* **Cloud forecast**: AEMET tiles/JSON in Spain; global **Open-Meteo** for cloud cover probability. ([aemet.es][4], [Open Meteo][5])
* **Twilight climatology**: ERA5/ERA5-Land hourly means for 19–21 local. ([cds.climate.copernicus.eu][6], [cds.climate.copernicus.eu][7])
* **Aerosols & smoke**: **CAMS** AOD, **GFAS** fire emissions heat maps. ([confluence.ecmwf.int][8], [ECMWF][22])
* **Fire danger**: **EFFIS** FWI (forecast & anomaly). ([forest-fire.emergency.copernicus.eu][31])
* **Satellite**: **EUMETSAT** (Meteosat Rapid Scan) and **NASA GIBS** (VIIRS/MODIS true color) as backdrop. ([user.eumetsat.int][24], [nasa-gibs.github.io][26])
* **Access & amenities**: OSM roads/parking/POIs via **Overpass**. ([OpenStreetMap][14])

---

## Getting started

### Prereqs

* Node 18+, npm/pnpm; optional Python 3.11+ for server mode.
* API keys if you use regional providers (e.g., **AEMET OpenData**). ([aemet.es][4])

### Install

```bash
git clone https://github.com/<you>/eclipse-chaser
cd eclipse-chaser
pnpm i   # or npm i / yarn
cp .env.example .env.local   # add AEMET key if needed
pnpm dev
```

### Load an eclipse

* Drop a **NASA/GSFC path CSV/KML** or **X. Jubier KMZ** into `/data/eclipses/<YYYYMMDD>/`. ([eclipse.gsfc.nasa.gov][1], [xjubier.free.fr][3])
* Or point to a remote URL in `eclipses.config.json` with timing metadata.

---

## Tech stack

* **Map engine**: **MapLibre GL JS** or **Leaflet**. ([maplibre.org][15], [leafletjs.com][16])
* **Rasters**: **GeoTIFF.js** (+ COGs), optional **GIBS** WMTS. ([geotiffjs.github.io][17], [nasa-gibs.github.io][26])
* **Geodesy**: **Turf.js**, **proj4**.
* **Solar**: **SunCalc** (BSD) / **NREL SPA** (reference). ([GitHub][19], [NREL Dokumente][20])
* **Data**: AEMET, Open-Meteo, ERA5/ERA5-Land (CDS), CAMS/GFAS, EFFIS, Copernicus DEM, PNOA-LiDAR, OSM/Overpass, EUMETSAT, NASA GIBS. ([aemet.es][4], [Open Meteo][5], [cds.climate.copernicus.eu][6], [cds.climate.copernicus.eu][7], [confluence.ecmwf.int][8], [ECMWF][22], [forest-fire.emergency.copernicus.eu][31], [registry.opendata.aws][10], [Plan Nacional de Ortofotografía Aérea][12], [OpenStreetMap][14], [user.eumetsat.int][24], [nasa-gibs.github.io][26])

---

## Licenses & attribution

* **Code**: Apache-2.0 (suggested).
* **NASA** (eclipse paths, imagery): public domain/attribution as per NASA guidelines & DAAC citation. ([NASA][38], [modaps.modaps.eosdis.nasa.gov][42])
* **AEMET**: free use with API key; follow AEMET OpenData attribution. ([opendata.aemet.es][43], [aemet.es][44])
* **OpenStreetMap**: **ODbL 1.0**, include © OpenStreetMap contributors. ([OpenStreetMap][39], [opendatacommons.org][40])
* **Copernicus DEM (GLO-30)**: free-to-use with attribution; see license & DOI. ([docs.sentinel-hub.com][33], [Copernicus Data Space Ecosystem][21])
* **CAMS/GFAS**: acknowledge ECMWF/Copernicus Atmosphere Service. ([Copernicus Atmosphere][30])
* **EFFIS/GWIS**: acknowledge Copernicus Emergency Management Service. ([forest-fire.emergency.copernicus.eu][31])
* **EUMETSAT**: follow Data Store/EUMETView terms when redisplaying imagery. ([user.eumetsat.int][35])

---

## Roadmap

* [ ] One-click **Top-5 plan** export (GPX/KML + PDF field sheet).
* [ ] **Server-assist mode** with nightly ERA5 tiles & hourly CAMS/EFFIS refresh. ([cds.climate.copernicus.eu][6], [confluence.ecmwf.int][8], [forest-fire.emergency.copernicus.eu][31])
* [ ] **Local horizon cache** using Copernicus DEM tiles (S3/Azure). ([registry.opendata.aws][10])
* [ ] **Pluggable providers** for other NMHS (e.g., DWD, Météo-France).
* [ ] **Offline** PWA build for field use.
* [ ] Optional **AHP** weighting UI for power users.

---

## Contributing

PRs welcome—especially data adapters for other meteorological agencies, DEM sources, or eclipse providers.

---

## Why this approach?

* Eclipse chasing at **low solar altitude** lives or dies by **twilight cloud** and **horizon obstructions**; modern open datasets let us quantify both. ERA5/ERA5-Land covers the climatology; **AEMET/Open-Meteo** provide short-range forecasts; **CAMS/GFAS** adds dust/smoke risk; **EFFIS** flags fire weather; **Copernicus DEM/PNOA-LiDAR** nails the skyline; **OSM** exposes access & amenities—**all reusable** for any future eclipse. ([cds.climate.copernicus.eu][6], [cds.climate.copernicus.eu][7], [aemet.es][4], [Open Meteo][5], [confluence.ecmwf.int][8], [ECMWF][22], [forest-fire.emergency.copernicus.eu][31], [registry.opendata.aws][10], [Plan Nacional de Ortofotografía Aérea][12], [OpenStreetMap][14])

---

## Disclaimer

⚠️ **Important**: This project and its code are provided **“as-is”**, highly experimental and likely incomplete or incorrect.  
It is labelled **unstable / in-development** and remains so until further notice—no promises of future completion or feature fulfillment.

### AI-generated content
- Over **99 %** of this project (code, text, data pipelines, configurations) was generated using AI tools like ChatGPT / Codex, with *minimal human tweaking*.  
- I have **very limited background in Python or web development**, and this is more a side‑hustle prototype born from personal interest than professional software.

### No warranty or guarantees
- There is **no warranty** regarding correctness, reliability, completeness, or fitness for any particular purpose.  
- Use of this software is **at your own risk**. It may contain bugs, hallucinations, outdated assumptions, security vulnerabilities, or incomplete features.

### User responsibility
- You must **independently verify** any outputs, algorithms, or results before relying on them—especially for planning or critical decisions (e.g. eclipse logistics, weather predictions).
- This is **not legal, scientific, medical, or professional advice** in any domain.

### AI origin disclosure
- I track AI-generated code via this repository’s structure and markings (e.g. file headers incl. “AI-generated”).  
- If you choose to contribute, please only submit code you wrote or fully reviewed—and clearly label any new AI-generated content.

*(If major milestones are reached or human rewriting fundamentally changes the project, this status may be updated—but don’t count on it.)*

---

### Appendix: Key docs & references

* **NASA/GSFC** eclipse path tables & Google map for **2026-08-12**. ([eclipse.gsfc.nasa.gov][27], [eclipse.gsfc.nasa.gov][1])
* **Xavier Jubier** interactive maps & Google Earth files (KMZ). ([xjubier.free.fr][2], [xjubier.free.fr][3])
* **Leaflet / MapLibre GL JS** docs. ([leafletjs.com][16], [maplibre.org][15])
* **GeoTIFF.js** docs. ([geotiffjs.github.io][17])
* **SunCalc** and **NREL SPA** references. ([GitHub][19], [NREL Dokumente][20])
* **Open-Meteo** (forecast & historical archive). ([Open Meteo][5], [Open Meteo][45])
* **AEMET OpenData** entry points & FAQs (ES). ([aemet.es][4], [opendata.aemet.es][46])
* **CAMS** composition forecasts / GFAS fire emissions docs. ([confluence.ecmwf.int][8], [Copernicus Atmosphere][23])
* **EFFIS** FWI & services. ([forest-fire.emergency.copernicus.eu][31])
* **Copernicus DEM** access & license. ([registry.opendata.aws][10], [docs.sentinel-hub.com][33])
* **EUMETSAT** Data Store / EUMETView. ([user.eumetsat.int][35], [EUMETSAT][47])
* **OSM Overpass API** docs. ([OpenStreetMap][14])

---

[1]: https://eclipse.gsfc.nasa.gov/SEgoogle/SEgoogle2001/SE2026Aug12Tgoogle.html?utm_source=chatgpt.com "Total Solar Eclipse of 2026 Aug 12"
[2]: https://xjubier.free.fr/en/site_pages/solar_eclipses/TSE_2026_GoogleMapFull.html?utm_source=chatgpt.com "Spain - 2026 August 12 Total Solar Eclipse - Interactive Google Map"
[3]: https://xjubier.free.fr/en/site_pages/SolarEclipsesGoogleEarth.html?utm_source=chatgpt.com "Google Earth Files (kmz) - Solar Eclipses - Xavier Jubier - Free"
[4]: https://www.aemet.es/en/datos_abiertos/AEMET_OpenData?utm_source=chatgpt.com "AEMET OpenData - State Meteorological Agency"
[5]: https://open-meteo.com/en/docs?utm_source=chatgpt.com "Weather Forecast API - Open-Meteo.com"
[6]: https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels?tab=download&utm_source=chatgpt.com "ERA5 hourly data on single levels from 1940 to present"
[7]: https://cds.climate.copernicus.eu/datasets/reanalysis-era5-land?tab=download&utm_source=chatgpt.com "ERA5-Land hourly data from 1950 to present - Climate Data Store"
[8]: https://confluence.ecmwf.int/display/CKB/CAMS%3A%2BGlobal%2Batmospheric%2Bcomposition%2Bforecast%2Bdata%2Bdocumentation?utm_source=chatgpt.com "CAMS: Global atmospheric composition forecast data documentation"
[9]: https://data.europa.eu/89h/4ad7c541-d39f-441a-81cd-81d9ceae3d2e?utm_source=chatgpt.com "Fire danger (Canadian Fire Weather Index system) i..."
[10]: https://registry.opendata.aws/copernicus-dem/?utm_source=chatgpt.com "Copernicus Digital Elevation Model (DEM)"
[11]: https://copernicus-dem-30m.s3.amazonaws.com/readme.html?utm_source=chatgpt.com "Copernicus Digital Elevation Model datasets - AWS"
[12]: https://pnoa.ign.es/pnoa-lidar/mapa-lidar?utm_source=chatgpt.com "Mapa LiDAR - Plan Nacional de Ortofotografía Aérea"
[13]: https://pnoa.ign.es/pnoa-lidar/productos-a-descarga?utm_source=chatgpt.com "LiDAR, Productos a descarga - Plan Nacional de Ortofotografía Aérea"
[14]: https://wiki.openstreetmap.org/wiki/Overpass_API?utm_source=chatgpt.com "Overpass API - OpenStreetMap Wiki"
[15]: https://maplibre.org/maplibre-gl-js/docs/?utm_source=chatgpt.com "MapLibre GL JS"
[16]: https://leafletjs.com/reference.html?utm_source=chatgpt.com "Documentation - Leaflet - a JavaScript library for interactive maps"
[17]: https://geotiffjs.github.io/?utm_source=chatgpt.com "geotiff.js"
[18]: https://github.com/geotiffjs/geotiff.js/?utm_source=chatgpt.com "geotiff.js is a small library to parse TIFF files for visualization ... - GitHub"
[19]: https://github.com/mourner/suncalc?utm_source=chatgpt.com "mourner/suncalc: A tiny JavaScript library for calculating sun/moon ..."
[20]: https://docs.nrel.gov/docs/fy08osti/34302.pdf?utm_source=chatgpt.com "[PDF] Solar Position Algorithm for Solar Radiation Applications - Publications"
[21]: https://dataspace.copernicus.eu/explore-data/data-collections/copernicus-contributing-missions/collections-description/COP-DEM?utm_source=chatgpt.com "Copernicus DEM - Global and European Digital Elevation Model"
[22]: https://www.ecmwf.int/en/forecasts/dataset/global-fire-assimilation-system?utm_source=chatgpt.com "Global Fire Assimilation System (GFAS) - ECMWF"
[23]: https://atmosphere.copernicus.eu/sites/default/files/2018-05/CAMS%20%20Global%20Fire%20Assimilation%20System%20%28GFAS%29%20data%20documentation.pdf?utm_source=chatgpt.com "[PDF] CAMS Global Fire Assimilation System (GFAS) data documentation"
[24]: https://user.eumetsat.int/data-access?utm_source=chatgpt.com "Data access - EUMETSAT - User Portal"
[25]: https://www.eumetsat.int/data-access-services-onboarding-new-eumetview?utm_source=chatgpt.com "Data access services - Onboarding to the new EUMETView - Eumetsat"
[26]: https://nasa-gibs.github.io/gibs-api-docs/gis-usage/?utm_source=chatgpt.com "Accessing via GIS Applications - GitHub Pages"
[27]: https://eclipse.gsfc.nasa.gov/SEpath/SEpath2001/SE2026Aug12Tpath.html?utm_source=chatgpt.com "Path of Total Solar Eclipse of 2026 Aug 12"
[28]: https://nso.edu/for-public/eclipse-map-2026/?utm_source=chatgpt.com "Eclipse Map - August 12, 2026 - NSO - National Solar Observatory"
[29]: https://cds.climate.copernicus.eu/?utm_source=chatgpt.com "Climate Data Store - Copernicus"
[30]: https://atmosphere.copernicus.eu/documentation?utm_source=chatgpt.com "Documentation - Copernicus Atmosphere Monitoring Service (CAMS)"
[31]: https://forest-fire.emergency.copernicus.eu/applications/data-and-services?utm_source=chatgpt.com "Data and services - EFFIS"
[32]: https://gwis.jrc.ec.europa.eu/?utm_source=chatgpt.com "GWIS - Welcome to GWIS"
[33]: https://docs.sentinel-hub.com/api/latest/static/files/data/dem/resources/license/License-COPDEM-30.pdf?utm_source=chatgpt.com "[PDF] Licence for Copernicus DEM instance COP-DEM-GLO-30-F Global ..."
[34]: https://dev.overpass-api.de/overpass-doc/en/?utm_source=chatgpt.com "Overpass API User's Manual"
[35]: https://user.eumetsat.int/resources/user-guides/data-store-detailed-guide?utm_source=chatgpt.com "Data Store detailed guide - EUMETSAT - User Portal"
[36]: https://www.earthdata.nasa.gov/data/instruments/viirs/land-near-real-time-data?utm_source=chatgpt.com "VIIRS-Land Near Real-Time Data - NASA Earthdata"
[37]: https://www.earthdata.nasa.gov/engage/open-data-services-software-policies/data-information-guidance?utm_source=chatgpt.com "Data and Information Guidance | NASA Earthdata"
[38]: https://www.nasa.gov/nasa-brand-center/images-and-media/?utm_source=chatgpt.com "Guidelines for using NASA Images and Media Guidelines"
[39]: https://www.openstreetmap.org/copyright?utm_source=chatgpt.com "Copyright and License - OpenStreetMap"
[40]: https://opendatacommons.org/licenses/odbl/1-0/?utm_source=chatgpt.com "Open Data Commons Open Database License (ODbL) v1.0"
[41]: https://leafletjs.com/?utm_source=chatgpt.com "Leaflet - a JavaScript library for interactive maps"
[42]: https://modaps.modaps.eosdis.nasa.gov/services/faq/LAADS_Data-Use_Citation_Policies.pdf?utm_source=chatgpt.com "[PDF] LAADS DAAC Data Use Guidelines and Copyright - NASA"
[43]: https://opendata.aemet.es/?utm_source=chatgpt.com "AEMET OpenData"
[44]: https://www.aemet.es/es/datos_abiertos?utm_source=chatgpt.com "Datos abiertos - AEMET. Gobierno de España"
[45]: https://open-meteo.com/en/docs/historical-forecast-api?utm_source=chatgpt.com "Historical Forecast API - Open-Meteo.com"
[46]: https://opendata.aemet.es/centrodedescargas/docs/FAQs170621.pdf?utm_source=chatgpt.com "[PDF] FAQs de AEMET OpenData"
[47]: https://www.eumetsat.int/data-access-services-how-use-new-eumetview?utm_source=chatgpt.com "Data access services - How to use the new EUMETView - Eumetsat"
