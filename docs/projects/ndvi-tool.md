# Regional Vegetation Health Monitoring System

![NDVI Explorer – Bukidnon Province, Northern Mindanao](../assets/images/NDVIapp.png)

## Overview

An interactive web application built on Google Earth Engine that calculates and visualizes the Normalized Difference Vegetation Index (NDVI) for all provinces of the Northern Mindanao Region (Region X), Philippines. Designed as a passion project and presented at an exhibit, the app makes satellite remote sensing engaging and understandable for young audiences — turning complex geospatial science into a hands-on, visual experience.

[Launch Interactive NDVI Explorer](https://alexmaverickpabillaran.users.earthengine.app/view/ndvi-explorer){ .md-button .md-button--primary }

**Study Area:** Northern Mindanao Region (Region X), Philippines — all provinces  
**Role:** Solo project  
**Status:** Completed

---

## Methods & Tools

**Data Sources**

- Copernicus Sentinel-2 Harmonized (L1C) — multispectral satellite imagery via Google Earth Engine
- FAO GAUL Simplified 500m 2015 (Level 2) — provincial administrative boundaries via Google Earth Engine

**Processing Steps**

1. Filtered the Sentinel-2 Harmonized image collection by user-selected date range, province boundary, and maximum cloud cover percentage
2. Applied a median composite to reduce cloud noise across multiple scenes
3. Computed NDVI per pixel using the formula: `(NIR – Red) / (NIR + Red)` using Bands B8 and B4
4. Clipped the output to the selected province boundary from FAO GAUL 2015
5. Classified pixels into five NDVI classes: Water/Non-veg, Bare/Urban, Sparse Vegetation, Moderate Vegetation, and Dense/Healthy Vegetation
6. Calculated per-class area coverage in km² and derived summary statistics (mean, min, max, std dev)

**Tools Used**

| Tool | Purpose |
|------|---------|
| Google Earth Engine | Satellite imagery processing, NDVI computation, area statistics, and interactive map rendering |
| JavaScript (GEE Code Editor) | App scripting, UI panel design, and dynamic layer control |
| ChatGPT Plus | Logic drafting and development assistance |
| Claude AI | Code refinement, documentation, and project write-up |

---

## Key Features

- **Province-level NDVI Maps** — Generates a color-coded NDVI heatmap (red → yellow → green) for any selected province in Region X, clearly showing areas from bare/urban land to dense, healthy forest
- **Interactive Date Range Selector** — Users can define a custom start and end date tied to Sentinel-2 availability, allowing comparison of vegetation health across different seasons or years
- **Cloud Cover Filter** — A slider lets users set the maximum cloud cover percentage, ensuring cleaner and more accurate imagery composites
- **Multi-layer Toggle** — Switch between True Colour RGB, NDVI Heatmap, and False Colour (NIR) views independently
- **NDVI Statistics Panel** — After each analysis, the app displays mean, min, max, and standard deviation of NDVI, an overall vegetation health rating, and area breakdown by class in km²
- **Exhibit-Ready Design** — Built with a clean, intuitive side panel UI designed to be accessible and fun for young audiences at a public exhibit

---

## Sample Output — Bukidnon Province (2025)

The screenshot above shows the app's output for **Bukidnon**, analyzed using 37 Sentinel-2 scenes from January to December 2025 with a 20% maximum cloud cover filter.

| Metric | Value |
|--------|-------|
| Scenes Used | 37 |
| Mean NDVI | 0.629 |
| Min NDVI | -0.457 |
| Max NDVI | 0.886 |
| Std Dev | 0.157 |
| Overall Health | 🌳 Dense / Healthy |

**Area by Class (km²)**

| Class | Area |
|-------|------|
| Water / Non-veg | 22.6 km² |
| Bare / Urban | 228.7 km² |
| Sparse Vegetation | 542.6 km² |
| Moderate Vegetation | 2,109.2 km² |
| Dense / Healthy | 6,152.2 km² |

The NDVI heatmap reveals that the vast majority of Bukidnon is covered by dense, healthy vegetation (dark green), with clusters of sparse or stressed vegetation (orange/red) concentrated around urban centers and agricultural zones in the province's interior highlands.

---

## NDVI Legend

| Color | Class | NDVI Range |
|-------|-------|------------|
| 🟥 Red | Water / Bare | NDVI < 0 |
| 🟧 Orange | Sparse Veg. | 0.0 – 0.2 |
| 🟨 Yellow | Moderate | 0.2 – 0.4 |
| 🟩 Light Green | Healthy Veg. | 0.4 – 0.6 |
| 🟢 Dark Green | Dense Forest | 0.6 – 1.0 |

---

## Source Code

```javascript
// ============================================================
// Northern Mindanao NDVI Explorer — Improved GEE Script
// ============================================================

// --- Data Sources ---
var admin2 = ee.FeatureCollection('FAO/GAUL_SIMPLIFIED_500m/2015/level2');
var s2     = ee.ImageCollection('COPERNICUS/S2_HARMONIZED');

// ============================================================
// STYLE CONSTANTS
// ============================================================
var COLORS = {
  bg:        '#ffffff',
  panel:     '#f0faf7',
  card:      '#f7fffe',
  accent:    '#00a880',
  accent2:   '#0077b6',
  text:      '#1a1a1a',
  textMuted: '#555555',
  border:    '#b2dfdb',
  good:      '#00897b',
  warn:      '#f59e0b',
  bad:       '#e53935',
  white:     '#ffffff'
};

var LABEL_STYLE = {
  color:      COLORS.text,
  fontSize:   '12px',
  fontWeight: 'normal',
  margin:     '2px'
};

var HEADER_STYLE = {
  color:      COLORS.accent,
  fontSize:   '11px',
  fontWeight: 'bold',
  margin:     '8px'
};

var CARD_STYLE = {
  backgroundColor: COLORS.card,
  border:          '1px solid ' + COLORS.border,
  padding:         '10px',
  margin:          '6px'
};

// ============================================================
// HELPER: divider line
// ============================================================
function divider() {
  return ui.Panel({
    style: {
      height:          '1px',
      backgroundColor: COLORS.border,
      margin:          '8px',
      stretch:         'horizontal'
    }
  });
}

// ============================================================
// HELPER: section header label
// ============================================================
function sectionLabel(txt) {
  return ui.Label({ value: txt, style: HEADER_STYLE });
}

// ============================================================
// HELPER: info row (key / value)
// ============================================================
function infoRow(key, val) {
  return ui.Panel({
    widgets: [
      ui.Label({ value: key, style: { color: COLORS.textMuted, fontSize: '11px', width: '100px' } }),
      ui.Label({ value: val, style: { color: COLORS.text,      fontSize: '11px', fontWeight: 'bold' } })
    ],
    layout: ui.Panel.Layout.flow('horizontal'),
    style:  { margin: '2px' }
  });
}

// ============================================================
// MAIN PANEL
// ============================================================
var mainPanel = ui.Panel({
  style: {
    width:           '300px',
    backgroundColor: COLORS.bg,
    padding:         '0px'
  }
});

// --- Title bar ---
var titleBar = ui.Panel({
  style: {
    backgroundColor: COLORS.panel,
    padding:         '14px'
  }
});
titleBar.add(ui.Label({
  value: '🌿 NDVI EXPLORER',
  style: {
    color:      COLORS.accent,
    fontSize:   '16px',
    fontWeight: 'bold',
    margin:     '0'
  }
}));
titleBar.add(ui.Label({
  value: 'Northern Mindanao · Sentinel-2',
  style: { color: COLORS.textMuted, fontSize: '11px', margin: '2px' }
}));
mainPanel.add(titleBar);

// ============================================================
// CONTENT PANEL (scrollable area)
// ============================================================
var content = ui.Panel({
  style: {
    backgroundColor: COLORS.bg,
    padding:         '12px'
  }
});
mainPanel.add(content);

// ============================================================
// 1. AREA SELECTION
// ============================================================
content.add(sectionLabel('📍 AREA SELECTION'));

var filtered    = admin2.filter(ee.Filter.eq('ADM1_NAME', 'Region X (Northern Mindanao)'));
var admin2Names = filtered.aggregate_array('ADM2_NAME');

var dropDownHolder = ui.Panel({ style: { margin: '4px', stretch: 'horizontal' } });
dropDownHolder.add(ui.Label({ value: 'Loading provinces…', style: { color: COLORS.textMuted, fontSize: '11px' } }));
content.add(dropDownHolder);

var dropDown = null;

// ============================================================
// 2. DATE RANGE
// ============================================================
content.add(divider());
content.add(sectionLabel('📅 DATE RANGE'));

var dateRow = ui.Panel({
  layout: ui.Panel.Layout.flow('horizontal'),
  style:  { margin: '4px', stretch: 'horizontal' }
});

var startDate = ui.Textbox({
  value:       '2025-01-01',
  placeholder: 'YYYY-MM-DD',
  style: {
    color:           COLORS.text,
    backgroundColor: COLORS.card,
    fontSize:        '11px',
    width:           '110px',
    margin:          '0'
  }
});
var endDate = ui.Textbox({
  value:       '2025-12-31',
  placeholder: 'YYYY-MM-DD',
  style: {
    color:           COLORS.text,
    backgroundColor: COLORS.card,
    fontSize:        '11px',
    width:           '110px'
  }
});

dateRow.add(startDate);
dateRow.add(ui.Label({ value: '→', style: { color: COLORS.textMuted, fontSize: '12px', margin: '4px' } }));
dateRow.add(endDate);
content.add(dateRow);

// Cloud cover slider
content.add(ui.Label({ value: 'Max Cloud Cover (%)', style: { color: COLORS.textMuted, fontSize: '11px', margin: '6px' } }));
var cloudSlider = ui.Slider({
  min: 0, max: 100, value: 20, step: 5,
  style: { stretch: 'horizontal', color: COLORS.accent }
});
content.add(cloudSlider);

// ============================================================
// 3. LAYER TOGGLES
// ============================================================
content.add(divider());
content.add(sectionLabel('🗂  LAYERS'));

var showRGB   = ui.Checkbox({ label: 'True Colour (RGB)',  value: true,  style: { color: COLORS.text, fontSize: '12px' } });
var showNDVI  = ui.Checkbox({ label: 'NDVI Heatmap',       value: true,  style: { color: COLORS.text, fontSize: '12px' } });
var showFALSE = ui.Checkbox({ label: 'False Colour (NIR)', value: false, style: { color: COLORS.text, fontSize: '12px' } });
content.add(showRGB);
content.add(showNDVI);
content.add(showFALSE);

// ============================================================
// 4. ANALYSE BUTTON
// ============================================================
content.add(divider());

var btnWrapper = ui.Panel({
  style: {
    backgroundColor: '#1a6b3a',
    stretch:         'horizontal',
    margin:          '6px',
    padding:         '2px'
  }
});

var analyseBtn = ui.Button({
  label: '▶  ANALYSE',
  style: {
    color:      '#000000',
    fontSize:   '14px',
    fontWeight: 'bold',
    stretch:    'horizontal',
    margin:     '0'
  }
});
btnWrapper.add(analyseBtn);
content.add(btnWrapper);

function setBtnState(label, disabled) {
  analyseBtn.setLabel(label);
  analyseBtn.setDisabled(disabled);
}

var statusLabel = ui.Label({
  value: 'Select a province and click Analyse.',
  style: { color: COLORS.textMuted, fontSize: '11px', margin: '4px' }
});
content.add(statusLabel);

// ============================================================
// 5. STATISTICS CARD (populated after analysis)
// ============================================================
content.add(divider());
content.add(sectionLabel('📊 NDVI STATISTICS'));

var statsCard = ui.Panel({ style: CARD_STYLE });
statsCard.add(ui.Label({ value: 'Run analysis to see statistics.', style: { color: COLORS.textMuted, fontSize: '11px' } }));
content.add(statsCard);

// ============================================================
// 6. NDVI LEGEND
// ============================================================
content.add(divider());
content.add(sectionLabel('🎨 NDVI LEGEND'));

var palette = ['#d73027','#fc8d59','#fee08b','#91cf60','#1a9850'];
var labels  = ['Water / Bare', 'Sparse Veg.', 'Moderate', 'Healthy Veg.', 'Dense Forest'];
var legendPanel = ui.Panel({ style: { margin: '4px' } });

for (var i = 0; i < palette.length; i++) {
  var row = ui.Panel({
    layout: ui.Panel.Layout.flow('horizontal'),
    style:  { margin: '2px' }
  });
  row.add(ui.Panel({
    style: {
      backgroundColor: palette[i],
      width: '18px', height: '14px',
      margin: '1px',
      border: '1px solid rgba(255,255,255,0.15)'
    }
  }));
  row.add(ui.Label({ value: labels[i], style: { color: COLORS.text, fontSize: '11px' } }));
  legendPanel.add(row);
}
content.add(legendPanel);

// ============================================================
// 7. INTERPRETATION GUIDE
// ============================================================
content.add(divider());
content.add(sectionLabel('💡 INTERPRETATION'));

var guideCard  = ui.Panel({ style: CARD_STYLE });
var guideItems = [
  { range: 'NDVI < 0',  meaning: 'Water, clouds, snow',      color: '#0077b6' },
  { range: '0.0 – 0.2', meaning: 'Bare soil / urban',         color: '#c0440a' },
  { range: '0.2 – 0.4', meaning: 'Sparse / stressed veg.',    color: '#b07d00' },
  { range: '0.4 – 0.6', meaning: 'Moderate vegetation',       color: '#4a8c2a' },
  { range: '0.6 – 1.0', meaning: 'Dense, healthy vegetation', color: '#1a6b3a' }
];
guideItems.forEach(function(g) {
  var row = ui.Panel({
    layout: ui.Panel.Layout.flow('horizontal'),
    style:  { margin: '2px' }
  });
  row.add(ui.Label({ value: g.range,   style: { color: g.color,          fontSize: '10px', width: '80px', fontWeight: 'bold' } }));
  row.add(ui.Label({ value: g.meaning, style: { color: COLORS.textMuted, fontSize: '10px' } }));
  guideCard.add(row);
});
content.add(guideCard);

// ============================================================
// 8. FOOTER
// ============================================================
content.add(divider());
content.add(ui.Label({
  value: 'Data: Copernicus Sentinel-2 L1C | FAO GAUL 2015 | Region X PH',
  style: { color: COLORS.textMuted, fontSize: '10px', margin: '4px' }
}));

// ============================================================
// POPULATE DROPDOWN (async)
// ============================================================
admin2Names.evaluate(function(names) {
  names.sort();
  dropDown = ui.Select({
    placeholder: 'Select a Province / City…',
    items:       names,
    onChange: function() {
      setBtnState('▶  ANALYSE', false);
      statusLabel.setValue('Province changed — click Analyse to update.');
    },
    style: {
      color:           COLORS.text,
      backgroundColor: COLORS.card,
      fontSize:        '12px',
      stretch:         'horizontal',
      margin:          '4px'
    }
  });
  dropDownHolder.clear();
  dropDownHolder.add(dropDown);
});

// ============================================================
// NDVI VIS PARAMS
// ============================================================
var rgbVis  = { min: 0,    max: 3000, bands: ['B4','B3','B2'] };
var nirVis  = { min: 0,    max: 5000, bands: ['B8','B4','B3'] };
var ndviVis = { min: -0.2, max: 0.8,  palette: ['#d73027','#fc8d59','#fee08b','#91cf60','#1a9850'] };

// ============================================================
// ANALYSIS FUNCTION
// ============================================================
function runAnalysis() {
  var provinceName = dropDown.getValue();
  if (!provinceName) {
    statusLabel.setValue('⚠ Please select a province first.');
    return;
  }

  statusLabel.setValue('⏳ Loading data…');
  setBtnState('⏳  ANALYZING…', true);

  var selected = ee.Feature(
    filtered.filter(ee.Filter.eq('ADM2_NAME', provinceName)).first()
  );
  var geometry = selected.geometry();

  mapPanel.clear();
  mapPanel.centerObject(geometry, 10);

  var filteredS2 = s2
    .filter(ee.Filter.date(startDate.getValue(), endDate.getValue()))
    .filter(ee.Filter.bounds(geometry))
    .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', cloudSlider.getValue()));

  var sceneCount = filteredS2.size();
  var image      = filteredS2.median();
  var ndvi       = image.normalizedDifference(['B8','B4']).rename('ndvi');

  if (showRGB.getValue()) {
    mapPanel.addLayer(image.clip(geometry), rgbVis,  'True Colour RGB', true);
  }
  if (showFALSE.getValue()) {
    mapPanel.addLayer(image.clip(geometry), nirVis,  'False Colour NIR', true);
  }
  if (showNDVI.getValue()) {
    mapPanel.addLayer(ndvi.clip(geometry),  ndviVis, 'NDVI', true);
  }

  mapPanel.addLayer(
    ee.Image().byte().paint({ featureCollection: ee.FeatureCollection([selected]), width: 2 }),
    { palette: [COLORS.accent.replace('#', '')] },
    'Boundary'
  );

  var stats = ndvi.reduceRegion({
    reducer:    ee.Reducer.mean()
      .combine(ee.Reducer.min(),    '', true)
      .combine(ee.Reducer.max(),    '', true)
      .combine(ee.Reducer.stdDev(), '', true),
    geometry:   geometry,
    scale:      30,
    maxPixels:  1e10,
    bestEffort: true
  });

  var water    = ndvi.lt(0);
  var bareSoil = ndvi.gte(0).and(ndvi.lt(0.2));
  var sparse   = ndvi.gte(0.2).and(ndvi.lt(0.4));
  var moderate = ndvi.gte(0.4).and(ndvi.lt(0.6));
  var dense    = ndvi.gte(0.6);

  var pixelArea    = ee.Image.pixelArea().divide(1e6);
  var waterArea    = pixelArea.updateMask(water).reduceRegion({    reducer: ee.Reducer.sum(), geometry: geometry, scale: 30, maxPixels: 1e10, bestEffort: true });
  var bareSoilArea = pixelArea.updateMask(bareSoil).reduceRegion({ reducer: ee.Reducer.sum(), geometry: geometry, scale: 30, maxPixels: 1e10, bestEffort: true });
  var sparseArea   = pixelArea.updateMask(sparse).reduceRegion({   reducer: ee.Reducer.sum(), geometry: geometry, scale: 30, maxPixels: 1e10, bestEffort: true });
  var moderateArea = pixelArea.updateMask(moderate).reduceRegion({ reducer: ee.Reducer.sum(), geometry: geometry, scale: 30, maxPixels: 1e10, bestEffort: true });
  var denseArea    = pixelArea.updateMask(dense).reduceRegion({    reducer: ee.Reducer.sum(), geometry: geometry, scale: 30, maxPixels: 1e10, bestEffort: true });

  ee.Dictionary({
    mean:     stats.get('ndvi_mean'),
    min:      stats.get('ndvi_min'),
    max:      stats.get('ndvi_max'),
    std:      stats.get('ndvi_stdDev'),
    scenes:   sceneCount,
    water:    waterArea.get('area'),
    bare:     bareSoilArea.get('area'),
    sparse:   sparseArea.get('area'),
    moderate: moderateArea.get('area'),
    dense:    denseArea.get('area')
  }).evaluate(function(result) {
    if (!result) {
      statusLabel.setValue('⚠ No data found for this selection.');
      setBtnState('▶  ANALYSE', false);
      return;
    }

    var mean = result.mean !== null ? result.mean.toFixed(3) : 'N/A';
    var min  = result.min  !== null ? result.min.toFixed(3)  : 'N/A';
    var max  = result.max  !== null ? result.max.toFixed(3)  : 'N/A';
    var std  = result.std  !== null ? result.std.toFixed(3)  : 'N/A';

    var healthLabel = 'N/A';
    var healthColor = COLORS.textMuted;
    if (result.mean !== null) {
      if      (result.mean >= 0.6) { healthLabel = '🌳 Dense / Healthy';   healthColor = '#1a6b3a'; }
      else if (result.mean >= 0.4) { healthLabel = '🌿 Moderate';          healthColor = '#4a8c2a'; }
      else if (result.mean >= 0.2) { healthLabel = '🌾 Sparse / Stressed'; healthColor = '#b07d00'; }
      else if (result.mean >= 0)   { healthLabel = '🪨 Bare / Urban';      healthColor = '#c0440a'; }
      else                         { healthLabel = '💧 Water / Non-veg.';  healthColor = '#0077b6'; }
    }

    statsCard.clear();
    statsCard.add(infoRow('Province:',    provinceName));
    statsCard.add(infoRow('Scenes used:', String(result.scenes || 0)));
    statsCard.add(infoRow('Date range:',  startDate.getValue() + ' → ' + endDate.getValue()));
    statsCard.add(divider());
    statsCard.add(ui.Label({ value: 'NDVI Values', style: { color: COLORS.accent2, fontSize: '11px', fontWeight: 'bold', margin: '4px' } }));
    statsCard.add(infoRow('Mean NDVI:', mean));
    statsCard.add(infoRow('Min NDVI:',  min));
    statsCard.add(infoRow('Max NDVI:',  max));
    statsCard.add(infoRow('Std Dev:',   std));
    statsCard.add(divider());
    statsCard.add(ui.Label({ value: 'Overall Health', style: { color: COLORS.accent2, fontSize: '11px', fontWeight: 'bold', margin: '4px' } }));
    statsCard.add(ui.Label({ value: healthLabel, style: { color: healthColor, fontSize: '13px', fontWeight: 'bold' } }));
    statsCard.add(divider());
    statsCard.add(ui.Label({ value: 'Area by Class (km²)', style: { color: COLORS.accent2, fontSize: '11px', fontWeight: 'bold', margin: '4px' } }));
    statsCard.add(infoRow('Water/Non-veg:', result.water    !== null ? result.water.toFixed(1)    + ' km²' : 'N/A'));
    statsCard.add(infoRow('Bare/Urban:',    result.bare     !== null ? result.bare.toFixed(1)     + ' km²' : 'N/A'));
    statsCard.add(infoRow('Sparse Veg:',    result.sparse   !== null ? result.sparse.toFixed(1)   + ' km²' : 'N/A'));
    statsCard.add(infoRow('Moderate Veg:',  result.moderate !== null ? result.moderate.toFixed(1) + ' km²' : 'N/A'));
    statsCard.add(infoRow('Dense/Healthy:', result.dense    !== null ? result.dense.toFixed(1)    + ' km²' : 'N/A'));

    statusLabel.setValue('✅ Analysis complete for ' + provinceName + '.');
    setBtnState('▶  ANALYSE', false);
  });
}

// ============================================================
// WIRE BUTTON
// ============================================================
analyseBtn.onClick(runAnalysis);

// ============================================================
// MAP SETUP & ATTACH PANEL
// ============================================================
var mapPanel = ui.Map();
mapPanel.setOptions('HYBRID');
mapPanel.setCenter(124.3882, 8.2853, 8.5);
mapPanel.setControlVisibility({ all: true, mapTypeControl: true });

ui.root.clear();
ui.root.add(ui.SplitPanel({
  firstPanel:  mainPanel,
  secondPanel: mapPanel,
  orientation: 'horizontal',
  wipe:        false,
  style:       { stretch: 'both' }
}));
```

---

## References

- Copernicus / ESA. *Sentinel-2 Harmonized* [Dataset]. Google Earth Engine. Retrieved from [https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_HARMONIZED](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_HARMONIZED)
- Food and Agriculture Organization of the United Nations. *FAO GAUL Simplified 500m 2015 Level 2* [Dataset]. Google Earth Engine. Retrieved from [https://developers.google.com/earth-engine/datasets/catalog/FAO_GAUL_SIMPLIFIED_500m_2015_level2](https://developers.google.com/earth-engine/datasets/catalog/FAO_GAUL_SIMPLIFIED_500m_2015_level2)

---

## Links

[Open in Google Earth Engine](https://code.earthengine.google.com/ac3b538fe10abc7ded6e8223b369d0b6){ .md-button }
[Sentinel-2 Dataset](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_HARMONIZED){ .md-button }
[FAO GAUL 2015 Boundaries](https://developers.google.com/earth-engine/datasets/catalog/FAO_GAUL_SIMPLIFIED_500m_2015_level2){ .md-button }
