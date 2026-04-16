# Cloud/Hybrid GIS Architecture — Eastern Mau Environmental Monitoring System

## System Overview

A multi-tier hybrid GIS architecture combining on-premise raster processing, cloud-hosted feature services, and browser-based geospatial analytics to deliver an interactive environmental monitoring dashboard for the Eastern Mau Forest Reserve, Kenya.

**Live deployment**: [papayai.droneverse.pro/dashboard](https://papayai.droneverse.pro/dashboard/)

---

## Architecture Diagram

```
 ┌────────────────────────────────────────────────────────────────────┐
 │                      DATA ACQUISITION TIER                        │
 │                                                                    │
 │  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────────┐ │
 │  │ Landsat 5 TM │  │ Landsat 7/8  │  │ Sentinel-2 MSI (10 m)   │ │
 │  │ (30 m, 1984- │  │ (30 m, 2002- │  │ Copernicus Open Access   │ │
 │  │  1995)        │  │  2014)        │  │ Hub (2024)               │ │
 │  └──────┬───────┘  └──────┬───────┘  └────────────┬─────────────┘ │
 └─────────┼─────────────────┼────────────────────────┼──────────────┘
           │                 │                        │
           ▼                 ▼                        ▼
 ┌────────────────────────────────────────────────────────────────────┐
 │                     PROCESSING TIER (On-Premise)                   │
 │                                                                    │
 │  ArcGIS Pro / QGIS                                                │
 │  ├─ Atmospheric correction & cloud masking                        │
 │  ├─ Supervised maximum-likelihood classification                  │
 │  ├─ Accuracy assessment (>85 % overall)                           │
 │  └─ Export: single-band uint16 GeoTIFF, EPSG:32736               │
 │                                                                    │
 │  Python (rasterio, numpy)                                          │
 │  ├─ Area statistics per LULC class                                │
 │  ├─ Change-detection matrices (epoch-pair comparison)             │
 │  └─ ISO 19115 metadata generation (see /metadata/)               │
 │                                                                    │
 │  Output: 6 classified GeoTIFFs (1984, 1986, 1995, 2002,          │
 │          2014, 2024) + 6 ISO 19115 XML metadata records           │
 └──────────────────────────────┬─────────────────────────────────────┘
                                │
          ┌─────────────────────┼──────────────────────┐
          ▼                     ▼                      ▼
 ┌─────────────────┐  ┌─────────────────┐   ┌─────────────────────┐
 │  WEB SERVER      │  │  ArcGIS ONLINE  │   │  GITHUB REPOS       │
 │  (cPanel/Apache) │  │  (Esri SaaS)    │   │  (Version Control)  │
 │                  │  │                  │   │                     │
 │  /dashboard/     │  │  8 Hosted Items: │   │  - Source code      │
 │   ├ index.html   │  │  ├ Feature Svc   │   │  - Python scripts   │
 │   └ rasters/     │  │  ├ Web Maps      │   │  - ISO metadata     │
 │     ├ 1984.tif   │  │  ├ GeoJSON layers│   │  - Configs & SOPs   │
 │     ├ 1986.tif   │  │  └ Dashboards    │   │                     │
 │     ├ …          │  │                  │   │                     │
 │     └ 2024.tif   │  │  OGC WMS/WFS    │   │                     │
 └────────┬────────┘  └────────┬────────┘   └─────────────────────┘
          │                     │
          └──────────┬──────────┘
                     ▼
 ┌────────────────────────────────────────────────────────────────────┐
 │                     BROWSER RENDERING TIER                         │
 │                                                                    │
 │  ArcGIS Maps SDK for JavaScript v4.29                             │
 │  ├─ MapView with satellite basemap                                │
 │  ├─ MediaLayer renders classified raster overlays                 │
 │  └─ Epoch selector triggers ImageElement swap + fade transition   │
 │                                                                    │
 │  GeoTIFF.js 2.1.3 (loaded via AMD-safe wrapper)                  │
 │  ├─ Fetches raw .tif via HTTP range requests                     │
 │  ├─ Parses uint16 pixel values in browser                        │
 │  └─ Outputs RGBA canvas with class-specific symbology:           │
 │       1 = Dense Forest      → RGB(0, 109, 44)                    │
 │       2 = Barren Land       → RGB(247, 248, 233)                 │
 │       3 = Settlement        → RGB(186, 228, 179)                 │
 │       4 = Grassland         → RGB(49, 163, 84)                   │
 │       5 = Planted Farmland  → RGB(116, 196, 118)                 │
 │                                                                    │
 │  Chart.js 4.4.1 (AMD-safe wrapper)                               │
 │  ├─ Bar chart: area distribution per class                        │
 │  ├─ Pie chart: proportional land cover                            │
 │  └─ Line chart: 40-year temporal trend                            │
 │                                                                    │
 │  Interactive Features                                              │
 │  ├─ Epoch selector (6 epochs, 1984-2024)                          │
 │  ├─ Fade transition on epoch change (200 ms out / 500 ms in)     │
 │  ├─ Dynamic change-detection table (epoch-reactive)               │
 │  └─ Legend with class color swatches                              │
 └────────────────────────────────────────────────────────────────────┘
```

---

## Technology Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Satellite Imagery | Landsat 5/7/8, Sentinel-2 | Multi-temporal source imagery (30 m / 10 m) |
| Desktop GIS | ArcGIS Pro, QGIS | Supervised classification, accuracy assessment |
| Automation | Python (rasterio, numpy, tkinter) | Batch stats, change detection, desktop viewer |
| Metadata | ISO 19115:2003 / ISO 19139 XML | Standards-compliant dataset documentation |
| Cloud GIS | ArcGIS Online (8 hosted items) | Feature services, web maps, hosted GeoJSON |
| Web Server | Apache (cPanel) on shared hosting | Static dashboard + GeoTIFF file hosting |
| Map SDK | ArcGIS Maps SDK for JS v4.29 | MapView, MediaLayer, basemap rendering |
| Raster Parsing | GeoTIFF.js 2.1.3 | Browser-side GeoTIFF decoding + canvas colorization |
| Charts | Chart.js 4.4.1 | Statistical visualization (bar, pie, line) |
| Version Control | GitHub (7 repositories) | Code, configs, metadata, documentation |

---

## Key Design Decisions

### 1. Browser-Side Raster Rendering
GeoTIFF.js parses classified rasters directly in the browser, eliminating the need for a tile server or ArcGIS Image Service. This enables:
- Zero server-side processing for map rendering
- Pixel-level access for on-the-fly statistics
- Deployment on low-cost shared hosting (no GIS server required)

### 2. AMD Conflict Resolution
ArcGIS Maps SDK uses RequireJS (AMD), which conflicts with Chart.js and GeoTIFF.js UMD loaders. Solved with a `loadScriptNoAMD()` wrapper that temporarily removes `window.define` before loading non-AMD libraries.

### 3. MediaLayer for Raster Overlay
Classified rasters are rendered as Canvas images and projected via ArcGIS `MediaLayer` with `ImageElement`, providing proper geographic registration (EPSG:32736 bounds transformed to Web Mercator) without converting to tile pyramids.

### 4. Epoch Switching with Fade Transitions
Switching between classification epochs replaces the `ImageElement` in the `MediaLayer.source.elements` collection (simple property assignment does not trigger re-render). A CSS opacity fade (200 ms out → image swap → 500 ms in) prevents visual jarring.

---

## Deployment Topology

```
User Browser ──HTTPS──▶ Apache (droneverse.pro)
                          ├── /dashboard/index.html    (24 KB SPA)
                          ├── /dashboard/rasters/*.tif  (6 GeoTIFFs)
                          └── /portfolio/index.html     (credentials)
                        │
                        └──HTTPS──▶ ArcGIS Online
                                     ├── Satellite basemap tiles
                                     └── Feature services (GeoJSON)
```

**Hosting**: Shared cPanel hosting (InfinityFree)
**CDN**: ArcGIS CDN (`js.arcgis.com`), jsDelivr (Chart.js, GeoTIFF.js)
**SSL**: Let's Encrypt via cPanel AutoSSL

---

## Data Specifications

| Epoch | Sensor | Resolution | Dimensions | CRS | File Size |
|-------|--------|-----------|------------|-----|-----------|
| 1984 | Landsat 5 TM | 30 m | 1498 × 1455 | EPSG:32736 | ~4 MB |
| 1986 | Landsat 5 TM | 30 m | 1498 × 1455 | EPSG:32736 | ~4 MB |
| 1995 | Landsat 5 TM | 30 m | 1498 × 1455 | EPSG:32736 | ~4 MB |
| 2002 | Landsat 7 ETM+ | 30 m | 1498 × 1455 | EPSG:32736 | ~4 MB |
| 2014 | Landsat 8 OLI | 30 m | 1498 × 1455 | EPSG:32736 | ~4 MB |
| 2024 | Sentinel-2 MSI | 10 m | 4494 × 4364 | EPSG:32736 | ~38 MB |

**Bounding box (WGS 84)**: 35.6945°E, 0.6782°S → 36.098°E, 0.2836°S

---

## Related Repositories

| Repository | Content |
|-----------|---------|
| [arcgis-python-automation](https://github.com/passianymike-tech/arcgis-python-automation) | Python scripts: batch publishing, LULC desktop viewer, area stats |
| [remote-sensing-gis-integration](https://github.com/passianymike-tech/remote-sensing-gis-integration) | RS workflows, ISO 19115 metadata, classification pipelines |
| [webgis-applications-portfolio](https://github.com/passianymike-tech/webgis-applications-portfolio) | Web mapping apps (Leaflet, ArcGIS JS SDK) |
| [postgis-spatial-database-projects](https://github.com/passianymike-tech/postgis-spatial-database-projects) | PostGIS spatial SQL, network analysis |
| [gis-training-capacity-building](https://github.com/passianymike-tech/gis-training-capacity-building) | Training materials, SOPs, evaluation reports |

---

## Author

**Mike Papayai Passiany**
MSc Geographic Information Systems — University of Nairobi
[LinkedIn](https://www.linkedin.com/in/59b641a2/) | [Portfolio](https://papayai.droneverse.pro/portfolio/) | [Dashboard](https://papayai.droneverse.pro/dashboard/)
