# Watershed Catchment Area Mapping for Water System Development

![Watershed Catchment Area of Water Source](../assets/images/Watershed2.png)

## Overview

This project involves delineating and mapping the watershed catchment areas of a proposed water source along the Budas River in Balabagan, Lanao del Sur. Using terrain analysis from satellite-derived elevation data, multiple pour points were evaluated to identify viable catchment extents that will inform the feasibility of developing a community water system.

**Study Area:** Municipality of Balabagan, Province of Lanao del Sur  
**Duration:** March 2026 – Present  
**Role:** Contributor  
**Status:** In progress (Feasibility Study Phase)

---

## Methods & Tools

**Data Sources**

- ESA Copernicus Digital Elevation Model (DEM)
- Hydrologically derived stream network, flow accumulation, and drainage direction rasters

**Processing Steps**

1. Acquired and exported DEM data using Google Earth Engine
2. Reprojected terrain data to PRS 92 / Philippines Zone 5 (EPSG:3125)
3. Preprocessed the DEM through sink filling and hydrologic conditioning
4. Generated flow accumulation, drainage direction, and stream network rasters using hydrologic analysis tools
5. Identified and refined multiple pour points along the Budas River for watershed delineation
6. Delineated watershed catchment boundaries for each pour point scenario
7. Calculated catchment extents and produced cartographic outputs for feasibility assessment and technical reporting

**Tools Used**

| Tool | Purpose |
|------|---------|
| QGIS + GRASS GIS | DEM preprocessing, watershed delineation, stream network extraction, and cartographic layout production |
| Google Earth Engine | DEM acquisition and export |

---

## Key Findings

- Four pour point scenarios were analyzed, yielding catchment areas of **40.54 sq.km**, **1.92 sq.km**, **0.86 sq.km**, and **0.056 sq.km**
- Stream network and topographic contours were mapped along the **Budas River** corridor to support site assessment
- Cartographic outputs were produced in compliance with PRS 92 / Philippines Zone 5 coordinate reference system for local government use

---

> **Note:** This project is currently under feasibility study and has not yet been formally submitted. Detailed findings and hydrological analysis results are not disclosed at this stage.
