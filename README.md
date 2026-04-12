# ArcGIS Dashboards & Cloud/Hybrid GIS Environments

Documentation and configurations for **system integration, dashboard development, and cloud-based GIS deployments** supporting multi-agency geospatial data access and decision-making.

## Projects

### 1. Environmental Monitoring Dashboard — Eastern Mau Forest
**Platform**: ArcGIS Online + Power BI Integration
**Use Case**: Real-time monitoring of forest cover changes for Kenya Forest Service

**Features:**
- Interactive web map with multi-temporal LULC classification layers (1984–2024)
- Time slider for temporal comparison of forest cover
- Automated area statistics charts (bar, line, pie)
- NDVI vegetation health indicators with threshold alerts
- Change detection heat maps highlighting deforestation hotspots
- Downloadable reports for field officers

**Architecture:**
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│  Satellite Data  │───▶│  Processing      │───▶│  ArcGIS Online  │
│  (Sentinel-2)    │    │  (ArcGIS Pro +   │    │  Feature Service │
│                  │    │   Python)         │    │                  │
└─────────────────┘    └──────────────────┘    └────────┬────────┘
                                                         │
                        ┌──────────────────┐    ┌────────▼────────┐
                        │  Power BI        │◀───│  ArcGIS         │
                        │  Dashboard       │    │  Dashboard      │
                        │  (Analytics)     │    │  (Spatial View)  │
                        └──────────────────┘    └─────────────────┘
```

**Technologies**: ArcGIS Online, ArcGIS Dashboard, Power BI, Python, ArcGIS API for Python

---

### 2. Drone Survey Operations Dashboard
**Platform**: ArcGIS Online + Custom Web Application
**Use Case**: Real-time drone survey mission tracking and deliverable management

**Features:**
- Live flight tracking with mission boundary visualization
- Project status tracking (planned → in-progress → completed → delivered)
- Survey area coverage calculator
- Deliverable gallery (orthophotos, DEMs, 3D models)
- Client access portal with download links
- No-fly zone compliance checker

**Technologies**: ArcGIS Online, Leaflet.js, Node.js, PostgreSQL

---

### 3. County GIS Data Portal — Multi-Agency Access
**Platform**: Hybrid deployment (on-premise PostgreSQL/PostGIS + ArcGIS Online)
**Use Case**: Centralized geospatial data sharing for county government departments

**Architecture:**
```
┌──────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Department A │     │  PostgreSQL /    │     │  ArcGIS Online  │
│  (Planning)   │────▶│  PostGIS Server  │────▶│  (Public Portal)│
├──────────────┤     │  (On-Premise)    │     │                 │
│  Department B │────▶│                  │     │  Web Maps       │
│  (Lands)      │     │  - Vector data   │     │  Dashboards     │
├──────────────┤     │  - Raster tiles  │     │  Story Maps     │
│  Department C │────▶│  - Metadata      │     │                 │
│  (Environment)│     └──────────────────┘     └─────────────────┘
└──────────────┘            │
                    ┌───────▼────────┐
                    │  GeoServer     │
                    │  (WMS/WFS)     │
                    │  OGC Services  │
                    └────────────────┘
```

**Features:**
- Role-based access control for different government departments
- OGC-compliant WMS/WFS services via GeoServer
- Metadata catalog compliant with ISO 19115 / INSPIRE
- Automated data synchronization between on-premise and cloud
- Public-facing web map portal for citizen access

**Technologies**: PostgreSQL/PostGIS, GeoServer, ArcGIS Online, ArcGIS Enterprise (evaluation), Python

---

### 4. Agricultural Land Suitability Dashboard
**Platform**: ArcGIS Online + Power BI
**Use Case**: Multi-criteria land suitability analysis for agricultural planning

**Features:**
- Weighted overlay analysis visualization
- Soil type, elevation, rainfall, and proximity layers
- Interactive slider for criteria weight adjustment
- Suitability score heat map
- Parcel-level recommendation reports

**Technologies**: ArcGIS Online, ArcGIS Spatial Analyst, Power BI, Python

---

### 5. Urban Planning Decision Support System
**Platform**: Custom Web Application + ArcGIS Services
**Use Case**: Evidence-based urban development planning

**Features:**
- Population density layers with growth projections
- Infrastructure gap analysis maps
- Zoning compliance checker
- Environmental impact assessment layers
- Stakeholder comment and feedback system

**Technologies**: React.js, ArcGIS API for JavaScript, Node.js, PostgreSQL/PostGIS

---

## Cloud/Hybrid GIS Architecture Patterns

### Pattern 1: Hybrid On-Premise + Cloud
Best for government agencies requiring data sovereignty with cloud accessibility.

### Pattern 2: Full Cloud (ArcGIS Online)
Best for organizations leveraging Esri's SaaS platform for rapid deployment.

### Pattern 3: Open Source Cloud Stack
PostgreSQL/PostGIS + GeoServer + Leaflet/OpenLayers on AWS/Azure.

## Technologies Stack
- **Esri**: ArcGIS Online, ArcGIS Enterprise, ArcGIS Dashboards, Experience Builder
- **Database**: PostgreSQL/PostGIS, ArcGIS Enterprise Geodatabase
- **Visualization**: Power BI, ArcGIS Dashboard, Custom D3.js charts
- **Server**: GeoServer (OGC services), Node.js, Python Flask
- **Cloud**: AWS (EC2, RDS, S3), Azure
- **Frontend**: ArcGIS API for JavaScript, Leaflet.js, React

## Author
**Mike Papayai Passiany**
MSc Geographic Information Systems — University of Nairobi
[LinkedIn](https://www.linkedin.com/in/59b641a2/) | [Portfolio](https://papayai.droneverse.pro/)
