---
title: "Water System Planning Support Through GIS Mapping and Hazard Assessment — Balabagan, Lanao del Sur"
description: "Independent GIS consulting engagement delivering 13 report-ready map layouts for a Level III water system feasibility study in Balabagan, Lanao del Sur — covering infrastructure planning, hazard assessment, and field survey geotagging."
---

# Water System Planning Support Through GIS Mapping and Hazard Assessment

**Role:** GIS Analyst, Cartographer, and Field Survey Support  
**Study Area:** Municipality of Balabagan, Province of Lanao del Sur, Philippines  
**Type:** Independent GIS Consulting — Feasibility Study Phase  

---

## Overview

This project provided GIS mapping and spatial analysis support for a Level III water system feasibility assessment in Balabagan, Lanao del Sur — a coastal municipality in the Bangsamoro Autonomous Region in Muslim Mindanao (BARMM). The engagement combined desktop GIS analysis with direct field survey participation, producing **13 report-ready map layouts** alongside a verified spatial dataset of intake sources and survey points.

Desktop work covered service area delineation, infrastructure layout mapping, and natural hazard assessment across flood, landslide, and storm surge exposure. In the field, I served as the GIS and geotagging specialist within a three-person team — responsible for GPS geotagging of proposed intake sources, survey point recording, and survey coordinate management, while the other members handled rodman duties and the RTK receiver. Field-collected coordinates were subsequently verified against GIS-derived reference points in Google Earth Pro and integrated directly into the map outputs.

All deliverables were produced in **PRS 92 / Philippines Zone 5 (EPSG:3125)**, the required coordinate reference system for local government and national agency submissions in the Philippines.

---

## Map Outputs

### Proposed Service Area and Waterline Layout

![Proposed Service Area Map — Level III Water System, Balabagan, Lanao del Sur](../assets/images/Service_Areas.png)

The proposed service area encompasses six barangays — **Lorenzo, Banago, Narra, Poblacion, Batuan, and Magulalung Occidental** — delineated in green along the Ilana Bay coastal zone. The complete waterline layout is shown with three line types: the **Intake Line** (white) connecting the Rumagurook Spring Source to the Filtration Tank; the **Main Line** (blue) conveying treated water from the filtration point through Magulalung Occidental and into the Banago and Narra corridor; and **Lateral Lines** (yellow) branching through Poblacion, Banago, Narra, and Batuan to reach distribution endpoints.

An inset map zooms to the **Rumagurook Spring Source** in Kapatagan, showing the three candidate intake points (Intake 1, 2, and 3) and the Filtration Tank location — the origin of the entire distribution system. These intake coordinates were GPS-tagged and field-verified during the survey.

*Data: Google Satellite, LGU Balabagan Municipal Boundary | CRS: PRS 92 / Philippines Zone 5*

---

### Digitised Built-up Areas of the Proposed Service Area

![Digitised Built-up Areas — Proposed Service Area, Balabagan, Lanao del Sur](../assets/images/ServiceArea.png)

Built-up areas within the service area were manually digitised from high-resolution satellite imagery, yielding a total built-up extent of **0.066 sq.km**. The road network was traced and measured at **23.08 km** — establishing the spatial reference for waterline alignment and distribution planning. Barangay labels confirm coverage across Banago, Lorenzo, and Upper Igabay in this early delineation layer.

*Data: OpenStreetMap, PHL Admin Boundary | CRS: PRS 92 / Philippines Zone 5*

---

### Landslide Hazard Map — Rumagurook Intake Site

![Landslide Hazard Map — Rumagurook Intake Site with Proposed Infrastructure Layout](../assets/images/_Rumagurook_Landslide.png)

Site-scale landslide hazard map for the Rumagurook intake area, integrating UP NOAH susceptibility data (High / Medium / Low) with the proposed infrastructure layout: Intake 1, 2, and 3 (star symbols); Filtration Building (pink triangle); Mainline (blue); Intake Line (white); and Pipe Crossing 1 (red circle) at the stream crossing. Rumagurook Downstream is marked as a downstream reference.

The proposed intake points fall within low-to-medium susceptibility zones, while the mainline route traverses mixed susceptibility terrain — spatial information directly applicable to engineering risk assessment and pipeline route refinement.

*Data: UP Nationwide Operational Assessment of Hazards (UP NOAH), PHL Admin Boundary | CRS: PRS 92 / Philippines Zone 5*

---

### 100-Year Return Period Flood Hazard Map — Municipality of Balabagan

![100-Year Return Period Flood Hazard Map — All Barangays, Balabagan, Lanao del Sur](../assets/images/FloodMap.png)

Municipality-wide flood susceptibility map covering all 22 barangays of Balabagan, classified from UP NOAH 100-Year return period data into High (dark blue), Medium (light blue), and Low (white) zones. Coastal barangays fringing Ilana Bay carry the highest flood exposure, with hazard concentrations along stream corridors and coastal lowlands. Barangays further inland show comparatively lower susceptibility — a pattern that directly informed infrastructure siting decisions for the intake and service area.

*Data: UP NOAH, PHL Admin Boundary, ESRI | CRS: PRS 92 / Philippines Zone 5*

---

## Methods

### Data Sources

| Source | Use |
|---|---|
| ESA Copernicus DEM GLO-30 (via Google Earth Engine) | Terrain analysis and DEM preprocessing |
| UP NOAH | Flood, landslide, and storm surge hazard data |
| Google Satellite / ESRI Basemap | Map layout backgrounds |
| OpenStreetMap | Road network and administrative boundaries |
| LGU Balabagan Municipal Boundary | Barangay extents |
| GPS Field Survey | Intake source coordinates and survey points |

### Workflow

All datasets were reprojected to PRS 92 / Philippines Zone 5 before processing. Built-up areas and the proposed waterline network were digitised from satellite imagery. UP NOAH flood, landslide, and storm surge datasets were integrated, classified, and overlaid with the infrastructure layout across 13 print layouts. Field-collected GPS coordinates were plotted in Google Earth Pro, verified against GIS reference points, and incorporated into the intake location maps to ensure ground-truth accuracy.

### Tools

QGIS (spatial analysis, digitisation, cartographic layout production), QGIS Print Layout and Atlas, Google Earth Engine (DEM and satellite data acquisition), Google Earth Pro (survey point plotting and verification), UP NOAH (hazard datasets), OpenStreetMap.

---

## Field Survey

On-site, I worked as the **GIS and geotagging specialist** within a three-person field team. I handled GPS geotagging of the three proposed intake sources at the Rumagurook Spring site, recorded and managed survey point coordinates throughout the traverse, and refined initial survey points against GIS-derived reference data for spatial consistency. The remaining team members operated as rodman and RTK receiver handler. I also conducted discharge measurements along the Budas River using bucket and float methods, and captured site photography using a DJI Mavic 3 Air for intake area documentation.

Post-field, GPS-tagged points were plotted in Google Earth Pro and cross-checked against GIS reference layers before being incorporated into the final map layouts.

---

## Skills Demonstrated

Infrastructure planning map production — service area delineation — waterline layout digitisation — built-up area mapping — flood, landslide, and storm surge hazard assessment (UP NOAH) — multi-barangay cartographic layout production (13 outputs, QGIS Print Layout) — CRS management for LGU submissions — GPS field geotagging — survey point coordination — Google Earth Pro point verification — discharge measurement — DJI Mavic 3 Air aerial photography (image acquisition).

---

> This project was completed as an independent consulting engagement. Full map layouts and client data are available for review on request during a hiring process.
