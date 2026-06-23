---
title: "Water System Planning Support Through GIS Mapping and Hazard Assessment — Balabagan, Lanao del Sur"
description: "Multi-deliverable GIS consulting engagement producing 13 report-ready map layouts for a Level III water system feasibility study in Balabagan, Lanao del Sur. Includes service area mapping, hazard assessment, intake location mapping, and field survey geotagging."
---

# Water System Planning Support Through GIS Mapping and Hazard Assessment

**Role:** GIS Analyst, Cartographer, and Field Survey Support
**Study Area:** Municipality of Balabagan, Province of Lanao del Sur, Philippines
**Engagement Type:** Independent GIS Consulting
**Status:** Completed — Feasibility Study Phase

---

## Overview

This consulting engagement provided end-to-end GIS mapping and spatial analysis support for a Level III water system feasibility assessment in Balabagan, Lanao del Sur — a coastal municipality in the Bangsamoro Autonomous Region in Muslim Mindanao (BARMM).

The engagement spanned desktop GIS analysis, cartographic production, and field survey participation. On the desktop side, the work involved spatial data integration from multiple open government sources, terrain analysis, natural hazard assessment, and the production of **13 report-ready map layouts** formatted for direct inclusion in the client's feasibility documentation. In the field, I served as the GIS and geotagging specialist within a three-person survey team — responsible for GPS geotagging of proposed intake sources, survey point documentation, and survey coordinate management while other team members handled rodman duties and RTK equipment.

All map outputs were produced in **PRS 92 / Philippines Zone 5 (EPSG:3125)** — the correct coordinate reference system for local government and national agency submissions in the Philippines.

---

## Project Scope

### Desktop GIS Deliverables

- Service area mapping with built-up area digitisation
- Road network mapping and measurement
- Intake location mapping (multiple candidate sites)
- Waterline routing (proposed mainline and intake lines)
- Flood hazard mapping — 100-Year return period
- Landslide hazard mapping overlaid with proposed infrastructure
- Storm surge advisory mapping — Levels 1–4
- Infrastructure planning maps
- 13 report-ready QGIS print layout outputs

### Field Survey Participation

- GPS geotagging of proposed water intake sources
- Survey point documentation and coordinate recording
- Survey point coordination within a three-person field team
- Support for RTK survey operations (survey point handler)
- Aerial photography using DJI Mavic 3 Air (image acquisition)
- River discharge measurement using bucket and float methods

---

## Map Outputs

### Service Area Map — Digitized Built-up Areas

![Service Area Map — Digitized Built-up Areas of the Proposed Service Area, Balabagan, Lanao del Sur](../assets/images/ServiceArea.png)

The service area was delineated to cover the proposed distribution footprint of the Level III water system, encompassing barangays including Banago, Lorenzo, and Upper Igabay along the Ilana Bay coastal zone. Built-up areas were manually digitised from high-resolution satellite imagery, yielding a total digitised built-up area of **0.066 sq.km** — establishing the population centre coverage for system design. The road network within the service area was mapped at **23.08 km** total length, providing the alignment reference for proposed waterline routing.

*Data sources: OpenStreetMap, PHL Admin Boundary | CRS: PRS 92 / Philippines Zone 5*

---

### Landslide Hazard Map — Intake Site (Rumagurook)

![Landslide Hazard Map — Proposed Intake Site with Waterline Layout, Rumagurook, Balabagan](../assets/images/_Rumagurook_Landslide.png)

Site-scale landslide hazard mapping overlaid with the proposed water infrastructure layout for the Rumagurook intake area. The map integrates UP NOAH landslide susceptibility data classified into three levels — High (dark green), Medium (medium green), and Low (light green) — with the spatial layout of proposed water infrastructure including:

- **Intake 1, 2, and 3** — candidate water intake points (star symbols) along the stream channel
- **Filtration Building** — proposed location (pink triangle)
- **Proposed Mainline** — water conveyance route to the service area (blue line)
- **Intake Line** — intake-to-filtration connection (white line)
- **Pipe Crossing 1** — proposed stream crossing location (red circle)
- **Rumagurook Downstream** — downstream reference point (pink star)

The overlay reveals that the proposed intake locations sit within low-to-medium landslide susceptibility zones, while the mainline route traverses areas of mixed susceptibility — information critical for engineering risk assessment and route refinement.

*Data sources: UP Nationwide Operational Assessment of Hazards (UP NOAH), PHL Admin Boundary | CRS: PRS 92 / Philippines Zone 5*

---

### 100-Year Return Period Flood Hazard Map — Municipality of Balabagan

![100-Year Return Period Flood Hazard Map — All 22 Barangays, Balabagan, Lanao del Sur](../assets/images/FloodMap.png)

Municipality-wide flood hazard map covering all 22 barangays of Balabagan using UP NOAH 100-Year return period flood susceptibility data. Susceptibility is classified into three levels — High (dark blue), Medium (light blue), and Low (white) — displayed against a dark basemap to maximise readability of flood extent.

The map reveals that the coastal barangays fringing Ilana Bay face significant high-susceptibility flood exposure, with high-hazard zones concentrated along stream corridors and coastal lowlands. Inland barangays in the northern portion of the municipality show comparatively lower susceptibility. This municipality-wide spatial picture directly informed site selection priorities for water intake infrastructure and service area risk characterisation.

All 22 barangay boundaries are delineated in yellow, with barangay names labelled — producing a map legible at the scale required for multi-barangay feasibility reporting.

*Data sources: UP Nationwide Operational Assessment of Hazards (UP NOAH), PHL Admin Boundary, ESRI | CRS: PRS 92 / Philippines Zone 5*

---

## Methods & Workflow

### Data Sources

| Source | Dataset | Use |
|---|---|---|
| ESA Copernicus DEM GLO-30 | Digital Elevation Model | Terrain analysis, slope, watershed delineation |
| Google Earth Engine | DEM acquisition and export, satellite imagery | Desktop processing input |
| UP Nationwide Operational Assessment of Hazards (UP NOAH) | Flood susceptibility, landslide susceptibility, storm surge advisory | Hazard mapping |
| OpenStreetMap | Road network, barangay administrative boundaries | Service area and infrastructure mapping |
| PHL Admin Boundary | Municipal and barangay extents | All map layouts |
| ESRI Basemap | Satellite and dark basemap | Map layout backgrounds |
| GPS Field Survey | Intake source coordinates, survey points | Geotagging and intake mapping |

### Processing Steps

1. Acquired Copernicus DEM via Google Earth Engine; exported for local QGIS processing
2. Reprojected all datasets to PRS 92 / Philippines Zone 5 (EPSG:3125)
3. Manually digitised built-up areas within the proposed service area from satellite imagery
4. Mapped and measured road network within the service area boundary
5. Integrated UP NOAH flood susceptibility data and classified by hazard level for all 22 barangays
6. Integrated UP NOAH landslide susceptibility data at the intake site scale
7. Overlaid proposed infrastructure layout (intake lines, mainline, filtration building, pipe crossing) onto hazard layers
8. Integrated UP NOAH storm surge advisory data for Levels 1–4 across the service area
9. Produced 13 report-ready QGIS print layout outputs with full cartographic elements
10. Incorporated field-collected GPS coordinates for intake sources into intake location maps

### Tools Used

| Tool | Purpose |
|---|---|
| QGIS | All spatial analysis, hazard overlay, digitisation, cartographic layout production |
| QGIS Print Layout | Report-ready map production (13 layouts) |
| Google Earth Engine | DEM and satellite data acquisition |
| UP NOAH | Flood, landslide, and storm surge hazard datasets |
| OpenStreetMap | Road network, administrative boundaries |
| GPS Device | Field geotagging of intake sources and survey points |
| DJI Mavic 3 Air | Site aerial photography (image acquisition) |

---

## Field Survey Contribution

The desktop GIS work was complemented by direct field survey participation as the **GIS and geotagging specialist** within a three-person survey team. Responsibilities in the field included:

- **GPS geotagging** of proposed water intake sources (Intake 1, 2, and 3) along the stream channel
- **Survey point documentation** — recording and managing coordinate data during field traversal
- **Survey point coordination** — managing the instrument station and ensuring spatial accuracy of recorded points while other team members operated as rodman and managed the RTK receiver
- **Initial survey point improvement** — refining field-identified survey points against GIS-derived reference data for consistency
- **Discharge measurement** — measuring river flow using bucket and float methods at the Budas River intake site
- **Aerial photography** using DJI Mavic 3 Air for site documentation and intake area visual record

The GPS coordinates collected in the field were integrated directly into the intake location maps, ensuring that cartographic outputs reflected actual ground conditions rather than desktop-estimated positions.

---

## Key Skills Demonstrated

- Multi-deliverable GIS consulting within a feasibility study scope
- Built-up area digitisation and service area mapping
- Flood hazard mapping (municipality-wide, 22 barangays, 100-Year return period)
- Landslide hazard mapping overlaid with proposed infrastructure alignment
- Storm surge advisory mapping (Levels 1–4)
- Hazard data integration from UP NOAH open government datasets
- Infrastructure planning map production (waterlines, intake sites, filtration buildings, pipe crossings)
- Report-ready cartographic layout production (13 map outputs, QGIS Print Layout)
- CRS management — PRS 92 / Philippines Zone 5 for LGU and national agency submissions
- Field GPS geotagging and survey point coordination
- Multi-person field survey team participation

---

> **Note:** This project was completed as an independent consulting engagement for a water system feasibility study. Detailed client data, full map layout files, and the complete 13-map output package are not publicly disclosed. The three map outputs shown on this page are selected examples. Full deliverables are available for review upon request during a hiring process.
