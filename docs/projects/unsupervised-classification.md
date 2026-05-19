# Unsupervised Land Cover Classification

![Unsupervised Land Cover Classification – Malaybalay City, Bukidnon](../assets/images/Unsupervised.png)

## Overview

A machine learning project applying unsupervised K-Means clustering to Landsat 8 satellite imagery to classify land cover across Malaybalay City, Bukidnon, Philippines — down to the barangay level. Built as a personal initiative to explore machine learning in the context of remote sensing, the app generates a 7-class land cover map and provides an interactive barangay-level insight panel with per-class area statistics and export capability.

**Study Area:** Malaybalay City, Bukidnon, Philippines — all barangays  
**Role:** Solo project  
**Status:** In Progress

---

## Methods & Tools

**Data Sources**

- Landsat 8 Collection 2 Tier 1 Surface Reflectance (LANDSAT/LC08/C02/T1_L2) — multispectral satellite imagery via Google Earth Engine
- Custom barangay boundary asset — Malaybalay City barangay polygons uploaded as a personal GEE asset (`projects/ee-alexmaverickpabillaran/assets/MalaybalayCityBrgy`)

**Processing Steps**

1. Filtered Landsat 8 imagery over Malaybalay City for 2024–2025, limited to the dry season months (March–May) and scenes with less than 15% cloud cover
2. Applied surface reflectance scaling factors and computed a median composite across all valid scenes
3. Selected six spectral bands — Blue, Green, Red, NIR, SWIR1, SWIR2 — as input features for clustering
4. Trained a K-Means clusterer (Weka K-Means, 7 clusters, seed = 42) on 5,000 randomly sampled pixels from the study area
5. Applied the trained clusterer to the full composite image to generate a 7-class land cover map
6. Computed per-class pixel area (km²) for any selected barangay or the whole city using `ee.Image.pixelArea()`
7. Rendered results in an interactive side panel with barangay dropdown, area statistics, and export functionality

**Tools Used**

| Tool | Purpose |
|------|---------|
| Google Earth Engine | Satellite imagery processing, K-Means clustering, area computation, and interactive map rendering |
| JavaScript (GEE Code Editor) | App scripting, UI panel and insight panel development |
| Claude AI | Code refinement, documentation, and project write-up |

---

## Key Features

- **7-Class Land Cover Map** — Classifies Malaybalay City into Built-up/Urban, Bare Soil/Exposed, Turbid/Shallow Water, Sparse Vegetation, Moderate Vegetation, Dense Vegetation, and Cloud/Shadow/Mixed
- **Barangay-level Insight Panel** — Select any barangay from a dropdown to zoom in, highlight its boundary, and view a ranked breakdown of land cover classes by area (km²) and percentage
- **Whole-City View** — Defaults to a city-wide analysis, then narrows to barangay level on selection
- **Export Cluster Map** — Queues a GeoTIFF export of the classified raster for any selected area to Google Drive via the GEE Tasks tab
- **Satellite Basemap** — Classification layer rendered on top of a satellite basemap for visual context

---

## Sample Output — Barangay 1

The screenshot above shows the app's output zoomed into **Barangay 1**, with the barangay boundary highlighted in gold. The insight panel on the right shows the area breakdown for that barangay.

| Class | Area | Share |
|-------|------|-------|
| Bare Soil / Exposed | 1.118 km² | 30.5% |
| Dense Vegetation | 1.033 km² | 28.1% |
| Built-up / Urban | 0.583 km² | 15.9% |
| Sparse Vegetation | 0.463 km² | 12.6% |
| Cloud / Shadow / Mixed | 0.453 km² | 12.4% |
| Turbid / Shallow Water | 0.017 km² | 0.5% |
| Moderate Vegetation | 0.001 km² | 0.0% |
| **Total** | **3.669 km²** | |

The map reveals a mix of bare soil and agricultural land dominating Barangay 1, with pockets of dense vegetation and scattered built-up areas. The high proportion of bare soil likely reflects dry-season field conditions captured during the March–May image window.

---

## Land Cover Classes

| Color | Class |
|-------|-------|
| 🟥 Dark Red | Built-up / Urban |
| 🟧 Orange-Red | Bare Soil / Exposed |
| 🟦 Blue | Turbid / Shallow Water |
| 🟨 Yellow | Sparse Vegetation |
| 🟩 Light Green | Moderate Vegetation |
| 🟢 Dark Green | Dense Vegetation |
| ⬜ Grey | Cloud / Shadow / Mixed |

---

## Source Code

```javascript
// ============================================================
// Unsupervised Classification – Landsat 8 | 8-Class K-Means
// Malaybalay City, Bukidnon — Barangay Insight Panel
// ============================================================

// ── 0. ASSET ─────────────────────────────────────────────
var malaybalay = ee.FeatureCollection('projects/ee-alexmaverickpabillaran/assets/MalaybalayCityBrgy');
var cityBoundary = malaybalay.geometry().dissolve();

// ── 1. IMAGE ─────────────────────────────────────────────
var composite = ee.ImageCollection('LANDSAT/LC08/C02/T1_L2')
  .filterBounds(cityBoundary)
  .filterDate('2024-01-01', '2025-12-31')
  .filter(ee.Filter.calendarRange(3, 5, 'month'))
  .filter(ee.Filter.lt('CLOUD_COVER', 15))
  .map(function(image) {
    return image.select('SR_B.').multiply(0.0000275).add(-0.2);
  })
  .median()
  .select(
    ['SR_B2','SR_B3','SR_B4','SR_B5','SR_B6','SR_B7'],
    ['Blue','Green','Red','NIR','SWIR1','SWIR2']
  )
  .clip(cityBoundary);

// ── 2. CLUSTERING ────────────────────────────────────────
var NUM_CLUSTERS = 7;

var clusterer = ee.Clusterer.wekaKMeans({
  nClusters: NUM_CLUSTERS,
  seed: 42,
  fast: true
}).train(
  composite.sample({
    region: cityBoundary,
    scale: 30,
    numPixels: 5000,
    seed: 42,
    geometries: false
  })
);

var clustered = composite.cluster(clusterer);

// ── 3. CONFIG ────────────────────────────────────────────
var PALETTE = [
  '#d73027','#f46d43','#74add1','#ffff00',
  '#91cf60','#1a9850','#878787',
];

var LABELS = [
  'Built-up / Urban',
  'Bare Soil / Exposed',
  'Turbid / Shallow Water',
  'Sparse Vegetation',
  'Moderate Vegetation',
  'Dense Vegetation',
  'Cloud / Shadow / Mixed'
];

// ── 4. MAP ───────────────────────────────────────────────
Map.setOptions('SATELLITE');
Map.centerObject(cityBoundary, 11);

Map.addLayer(
  clustered,
  {min: 0, max: 7, palette: PALETTE},
  'Land Cover Clusters'
);

Map.addLayer(
  malaybalay.style({color: '#ffffff', fillColor: '00000000', width: 1}),
  {},
  'Barangay Boundaries'
);

Map.addLayer(ee.Image(0).selfMask(), {}, 'Selected Barangay', false);

// ── 5. LEGEND ────────────────────────────────────────────
var legend = ui.Panel({
  style: {
    position: 'bottom-left',
    padding: '10px',
    width: '210px',
    backgroundColor: 'rgba(255,255,255,0.93)'
  }
});

legend.add(ui.Label({
  value: 'Land Cover Classes',
  style: {fontWeight: 'bold', fontSize: '13px', margin: '0 0 6px 0'}
}));

LABELS.forEach(function(label, i) {
  var row = ui.Panel({
    layout: ui.Panel.Layout.flow('horizontal'),
    style: {margin: '2px 0'}
  });
  row.add(ui.Label({
    style: {
      backgroundColor: PALETTE[i],
      padding: '7px',
      margin: '0 6px 0 0',
      border: '1px solid #ccc'
    }
  }));
  row.add(ui.Label({
    value: label,
    style: {fontSize: '11px', margin: '3px 0'}
  }));
  legend.add(row);
});

Map.add(legend);

// ── 6. INSIGHT PANEL ─────────────────────────────────────
var insightPanel = ui.Panel({
  style: {
    position: 'top-right',
    padding: '12px',
    width: '285px',
    backgroundColor: 'rgba(255,255,255,0.96)'
  }
});

insightPanel.add(ui.Label({
  value: 'Cluster Area Insights',
  style: {fontWeight: 'bold', fontSize: '14px', margin: '0 0 4px 0'}
}));

insightPanel.add(ui.Label({
  value: 'Select a Barangay:',
  style: {fontSize: '11px', color: '#444', margin: '4px 0 2px 0'}
}));

var dropdownSlot = ui.Panel({
  style: {margin: '0 0 8px 0', stretch: 'horizontal'}
});
dropdownSlot.add(ui.Label({
  value: 'Loading barangays...',
  style: {fontSize: '11px', color: '#888'}
}));
insightPanel.add(dropdownSlot);

insightPanel.add(ui.Panel({
  style: {
    height: '1px',
    backgroundColor: '#ddd',
    margin: '2px 0 8px 0',
    stretch: 'horizontal'
  }
}));

var resultsContainer = ui.Panel({layout: ui.Panel.Layout.flow('vertical')});
insightPanel.add(resultsContainer);

// ── Export buttons ────────────────────────────────────────
insightPanel.add(ui.Panel({
  style: {
    height: '1px',
    backgroundColor: '#ddd',
    margin: '8px 0 6px 0',
    stretch: 'horizontal'
  }
}));

insightPanel.add(ui.Label({
  value: 'Export Selected Area:',
  style: {fontSize: '11px', color: '#444', margin: '0 0 4px 0'}
}));

var btnRow = ui.Panel({
  layout: ui.Panel.Layout.flow('horizontal'),
  style: {margin: '0'}
});

btnRow.add(ui.Button({
  label: 'Export Cluster Map',
  style: {fontSize: '11px', margin: '0', stretch: 'horizontal'},
  onClick: function() { exportLayer('cluster'); }
}));

insightPanel.add(btnRow);

var exportStatus = ui.Label({
  value: '',
  style: {fontSize: '10px', color: '#888', margin: '4px 0 0 0'}
});
insightPanel.add(exportStatus);

Map.add(insightPanel);

// ── 7. STATE ─────────────────────────────────────────────
var activeGeom = cityBoundary;
var activeName = 'WholeCity';

// ── 8. EXPORT ────────────────────────────────────────────
function exportLayer(type) {
  var safeName = activeName.replace(/[^a-zA-Z0-9]/g, '_');

  if (type === 'cluster') {
    Export.image.toDrive({
      image: clustered.clip(activeGeom).toByte(),
      description: 'ClusterMap_' + safeName,
      folder: 'GEE_Exports',
      fileNamePrefix: 'ClusterMap_' + safeName,
      scale: 30,
      region: activeGeom,
      maxPixels: 1e9,
      fileFormat: 'GeoTIFF'
    });
    exportStatus.setValue('✓ Cluster export queued — check Tasks tab');
  }
}

// ── 9. POPULATE DROPDOWN ─────────────────────────────────
malaybalay.aggregate_array('NAME_3').sort().evaluate(function(names) {
  var items = ['— Whole City —'].concat(names);

  var dropdown = ui.Select({
    items: items,
    value: '— Whole City —',
    style: {stretch: 'horizontal'},
    onChange: function(selected) {
      exportStatus.setValue('');

      Map.layers().set(2,
        ui.Map.Layer(ee.Image(0).selfMask(), {}, 'Selected Barangay', false)
      );

      if (selected === '— Whole City —') {
        activeGeom = cityBoundary;
        activeName = 'WholeCity';
        Map.centerObject(cityBoundary, 12);
        runAnalysis('— Whole City —');
      } else {
        var brgy = malaybalay.filter(ee.Filter.eq('NAME_3', selected));
        activeGeom = brgy.geometry();
        activeName = selected;
        Map.layers().set(2,
          ui.Map.Layer(
            brgy.style({color: '#FFD700', fillColor: 'FFD70033', width: 2}),
            {},
            'Selected: ' + selected
          )
        );
        Map.centerObject(brgy, 14);
        runAnalysis(selected);
      }
    }
  });

  dropdownSlot.clear();
  dropdownSlot.add(dropdown);
  runAnalysis('— Whole City —');
});

// ── 10. ANALYSIS ─────────────────────────────────────────
function runAnalysis(selected) {
  resultsContainer.clear();
  resultsContainer.add(ui.Label({
    value: 'Calculating...',
    style: {fontSize: '11px', color: '#888'}
  }));

  var geom = selected === '— Whole City —'
    ? cityBoundary
    : malaybalay.filter(ee.Filter.eq('NAME_3', selected)).geometry();

  var areaImage = ee.Image.pixelArea().divide(1e6);
  var areas = new Array(NUM_CLUSTERS);
  var done = 0;

  for (var i = 0; i < NUM_CLUSTERS; i++) {
    (function(idx) {
      areaImage
        .updateMask(clustered.eq(idx))
        .reduceRegion({
          reducer: ee.Reducer.sum(),
          geometry: geom,
          scale: 30,
          maxPixels: 1e9
        })
        .evaluate(function(val) {
          areas[idx] = (val && val.area) ? val.area : 0;
          done++;
          if (done === NUM_CLUSTERS) renderResults(areas, selected);
        });
    })(i);
  }
}

// ── 11. RENDER ───────────────────────────────────────────
function renderResults(areas, selected) {
  resultsContainer.clear();

  var total = areas.reduce(function(a, b) { return a + b; }, 0);

  resultsContainer.add(ui.Label({
    value: selected,
    style: {fontWeight: 'bold', fontSize: '12px', color: '#222', margin: '0 0 6px 0'}
  }));

  var sorted = areas
    .map(function(a, i) { return {idx: i, area: a}; })
    .sort(function(a, b) { return b.area - a.area; });

  sorted.forEach(function(item) {
    var pct = total > 0
      ? ((item.area / total) * 100).toFixed(1)
      : '0.0';

    var row = ui.Panel({
      layout: ui.Panel.Layout.flow('vertical'),
      style: {
        margin: '3px 0',
        padding: '5px',
        backgroundColor: '#f5f5f5',
        stretch: 'horizontal'
      }
    });

    var topRow = ui.Panel({layout: ui.Panel.Layout.flow('horizontal')});
    topRow.add(ui.Label({
      style: {
        backgroundColor: PALETTE[item.idx],
        padding: '5px',
        margin: '0 6px 0 0',
        border: '1px solid #bbb'
      }
    }));
    topRow.add(ui.Label({
      value: LABELS[item.idx],
      style: {fontSize: '11px', fontWeight: 'bold', color: '#222', margin: '2px 0'}
    }));
    row.add(topRow);

    row.add(ui.Label({
      value: '  ' + item.area.toFixed(3) + ' km²  (' + pct + '%)',
      style: {fontSize: '10px', color: '#555', margin: '1px 0 0 0'}
    }));

    resultsContainer.add(row);
  });

  resultsContainer.add(ui.Panel({
    style: {
      height: '1px',
      backgroundColor: '#ccc',
      margin: '6px 0 4px 0',
      stretch: 'horizontal'
    }
  }));

  resultsContainer.add(ui.Label({
    value: 'Total: ' + total.toFixed(3) + ' km²',
    style: {fontSize: '11px', fontWeight: 'bold', color: '#333'}
  }));
}
```

---

## References

- USGS / NASA. *Landsat 8 Collection 2 Tier 1 Surface Reflectance* [Dataset]. Google Earth Engine. Retrieved from [https://developers.google.com/earth-engine/datasets/catalog/LANDSAT_LC08_C02_T1_L2](https://developers.google.com/earth-engine/datasets/catalog/LANDSAT_LC08_C02_T1_L2)

---

## Links

[Open in Google Earth Engine](https://code.earthengine.google.com/b56b254862a52b6687f7513b0d070768){ .md-button }
[Landsat 8 Dataset](https://developers.google.com/earth-engine/datasets/catalog/LANDSAT_LC08_C02_T1_L2){ .md-button }
