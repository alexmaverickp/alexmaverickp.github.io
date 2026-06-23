# Nature-based Solution: Proposed Riparian Buffer Restoration and Protection Strategy for Pulangui River Basin, Bukidnon

![Project overview image](../assets/images/pulangui.png)

## Overview

This project proposes a nature-based riparian buffer restoration and protection strategy for the Pulangui River Basin in Bukidnon, Philippines, using GIS-based watershed analysis to identify sediment-prone and degraded riparian zones requiring ecological restoration. The workflow integrates terrain analysis, hydrologic modeling, land cover exposure, and riparian buffer assessment to support flood resilience and sustainable watershed management through spatially explicit planning.

The project was developed as an entry to the Geographic Innovations for Development Solutions, Inc. (GRIDS) Mapping Contest with the theme: *Enhancing flood management and resilience through nature-based solutions*, and was awarded a **Consolation Prize**.

**Study Area:** Pulangui River Basin, Bukidnon, Philippines  
**Duration:** 2026  
**Role:** Team Lead / GIS & Remote Sensing Analyst  
**Status:** Completed

---

## Methods & Tools

### Data Sources

- Copernicus DEM 30m
- HydroATLAS / HydroSHEDS river datasets
- ESRI Sentinel-2 Land Cover 2024
- Google Satellite Imagery (visual validation)
- OpenStreetMap basemap

### Processing Steps

1. Generated terrain derivatives including slope and flow accumulation from DEM data
2. Extracted stream networks and delineated a 30-meter riparian buffer corridor
3. Reclassified land cover to identify exposed and degraded riparian areas
4. Developed a Sediment Susceptibility Index (SSI) using:
            - Slope gradient
            - Flow accumulation (runoff convergence)
            - Land cover exposure
6. Applied raster normalization and weighted overlay analysis to generate basin-scale sediment susceptibility
7. Intersected susceptibility outputs with degraded riparian zones to generate a risk-informed restoration priority map
8. Performed visual validation using high-resolution satellite imagery

### Tools Used

| Tool | Purpose |
|------|---------|
| QGIS | Spatial analysis, raster processing, hydrologic modeling, and map production |
| SAGA GIS | Terrain analysis and hydrologic processing |
| GDAL | Raster processing and proximity analysis |
| Google Earth Engine | Land cover data access and satellite-based validation support |

---

## Key Findings

- Produced a basin-scale Sediment Susceptibility Index (SSI) classified into four levels: Low, Moderate, High, and Very High
- Identified erosion-prone and sediment-generating zones influencing downstream flood behavior
- Delineated a 30-meter riparian restoration corridor and generated a risk-informed restoration priority map
- High-priority zones were concentrated in degraded riparian areas adjacent to streams and exposed land cover
- Forested riparian regions generally exhibited lower restoration priority due to reduced erosion exposure
- Developed a spatially explicit framework supporting nature-based watershed restoration and flood resilience planning

---

## Project Outputs

### 1. Basin-Scale Sediment Susceptibility Mapping

The first output identifies areas within the Pulangui River Basin with elevated potential for sediment generation, a major contributor to river siltation and flood intensity. The Sediment Susceptibility Index integrates slope, flow convergence, and land cover exposure to reveal erosion-prone zones influencing watershed stability.

### 2. Risk-Informed Riparian Restoration Priority Mapping

The second output prioritizes restoration zones within a 30-meter riparian corridor by intersecting sediment susceptibility with degraded land cover. High-priority areas represent locations where erosion risk and ecological disturbance coincide, guiding strategic restoration to reduce sediment delivery, stabilize riverbanks, and enhance flood resilience.

---

## Recognition

> 🏅 **Consolation Prize** — Geographic Innovations for Development Solutions, Inc. (GRIDS) Nature-based Solutions Mapping Contest (2026)

---

## Key Skills Demonstrated

- Watershed-scale spatial analysis
- Hydrologic terrain modeling
- Sediment susceptibility assessment
- Riparian buffer analysis
- Raster normalization and weighted overlay
- Nature-based solutions (NbS) planning
- Scientific cartographic design
- GIS storytelling and environmental visualization
