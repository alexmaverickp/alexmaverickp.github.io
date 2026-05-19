---
hide:
  - toc
---
<!--
CHECKLIST FOR THIS PAGE:
- [ ] Replace the two placeholder cards (marked [YOUR PROJECT ...]) with your real projects
- [ ] For each project: add a thumbnail image to docs/assets/images/ and update the path below
- [ ] For each project: create a project page by copying sample-project.md
- [ ] For each project: add a nav entry in mkdocs.yml (see the comments there)
- [ ] Delete placeholder cards you don't need yet
-->

# Projects

A selection of my geospatial projects. Click any card to see the full write-up.

<div class="grid" markdown>

<div class="project-card" markdown>
![](../assets/images/pulangui.png)

**[Nature-based Solution: Proposed Riparian Buffer Restoration and Protection Strategy for Pulangui River Basin, Bukidnon](pulangui.md)**

This project proposes a nature-based riparian buffer restoration and protection strategy for the Pulangui River Basin in Bukidnon, Philippines, using GIS-based watershed analysis to identify sediment-prone and degraded riparian zones requiring ecological restoration. The workflow integrates terrain analysis, hydrologic modeling, land cover exposure, and riparian buffer assessment to support flood resilience and sustainable watershed management through spatially explicit planning.

The project was developed as an entry to the Geographic Innovations for Development Solutions, Inc. (GRIDS) Mapping Contest with the theme: Enhancing flood management and resilience through nature-based solutions, and was awarded a Consolation Prize.

`QGIS` `Google Earth Engine` `ChatGPT Plus`

[View Project →](pulangui.md){ .md-button }
</div>

<div class="project-card" markdown>
![](../assets/images/pulangui.png)

<div class="grid" markdown>

<div class="project-card" markdown>
![](../assets/images/NDVIapp.png)

**[Remote Sensing NDVI Tool](ndvi-tool.md)**

An interactive web application built on Google Earth Engine that calculates and visualizes the Normalized Difference Vegetation Index (NDVI) for all provinces of the Northern Mindanao Region (Region X), Philippines. Designed as a passion project and presented at an exhibit, the app makes satellite remote sensing engaging and understandable for young audiences — turning complex geospatial science into a hands-on, visual experience.

`Google Earth Engine` `ChatGPT Plus` `Claude AI`

[View Project →](ndvi-tool.md){ .md-button }
</div>

<div class="project-card" markdown>
![](../assets/images/NDVIapp.png)

```js
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
      ui.Label({ value: key,  style: { color: COLORS.textMuted, fontSize: '11px', width: '100px' } }),
      ui.Label({ value: val,  style: { color: COLORS.text,      fontSize: '11px', fontWeight: 'bold' } })
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

// Placeholder while names load; replaced once evaluate() resolves
var dropDownHolder  = ui.Panel({ style: { margin: '4px', stretch: 'horizontal' } });
dropDownHolder.add(ui.Label({ value: 'Loading provinces…', style: { color: COLORS.textMuted, fontSize: '11px' } }));
content.add(dropDownHolder);

// Declared here so runAnalysis() can reference it
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

var showRGB  = ui.Checkbox({ label: 'True Colour (RGB)',     value: true,  style: { color: COLORS.text, fontSize: '12px' } });
var showNDVI = ui.Checkbox({ label: 'NDVI Heatmap',          value: true,  style: { color: COLORS.text, fontSize: '12px' } });
var showFALSE= ui.Checkbox({ label: 'False Colour (NIR)',    value: false, style: { color: COLORS.text, fontSize: '12px' } });
content.add(showRGB);
content.add(showNDVI);
content.add(showFALSE);

// ============================================================
// 4. ANALYSE BUTTON
// ============================================================
content.add(divider());

// ui.Button ignores backgroundColor, so we wrap it in a green panel
// and make the button itself transparent (white text on green bg)
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

// Helper to update button state
function setBtnState(label, disabled) {
  analyseBtn.setLabel(label);
  analyseBtn.setDisabled(disabled);
}

// Status label
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

var palette   = ['#d73027','#fc8d59','#fee08b','#91cf60','#1a9850'];
var labels    = ['Water / Bare', 'Sparse Veg.', 'Moderate', 'Healthy Veg.', 'Dense Forest'];
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

var guideCard = ui.Panel({ style: CARD_STYLE });
var guideItems = [
  { range: 'NDVI < 0',       meaning: 'Water, clouds, snow',      color: '#0077b6' },
  { range: '0.0 – 0.2',      meaning: 'Bare soil / urban',         color: '#c0440a' },
  { range: '0.2 – 0.4',      meaning: 'Sparse / stressed veg.',    color: '#b07d00' },
  { range: '0.4 – 0.6',      meaning: 'Moderate vegetation',       color: '#4a8c2a' },
  { range: '0.6 – 1.0',      meaning: 'Dense, healthy vegetation', color: '#1a6b3a' }
];
guideItems.forEach(function(g) {
  var row = ui.Panel({
    layout: ui.Panel.Layout.flow('horizontal'),
    style:  { margin: '2px' }
  });
  row.add(ui.Label({ value: g.range,   style: { color: g.color,       fontSize: '10px', width: '80px', fontWeight: 'bold' } }));
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
// NDVI VIS PARAMS  (improved palette)
// ============================================================
var rgbVis   = { min: 0,    max: 3000, bands: ['B4','B3','B2'] };
var nirVis   = { min: 0,    max: 5000, bands: ['B8','B4','B3'] };
var ndviVis  = { min: -0.2, max: 0.8,  palette: ['#d73027','#fc8d59','#fee08b','#91cf60','#1a9850'] };

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

  // Rebuild panel layer (mapPanel.clear() removes it)
  // panel is in SplitPanel, no re-add needed;

  // Filter collection
  var filteredS2 = s2
    .filter(ee.Filter.date(startDate.getValue(), endDate.getValue()))
    .filter(ee.Filter.bounds(geometry))
    .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', cloudSlider.getValue()));

  // Count scenes
  var sceneCount = filteredS2.size();
  var image      = filteredS2.median();

  // NDVI
  var ndvi = image.normalizedDifference(['B8','B4']).rename('ndvi');

  // Add layers conditionally
  if (showRGB.getValue()) {
    mapPanel.addLayer(image.clip(geometry), rgbVis,  'True Colour RGB', true);
  }
  if (showFALSE.getValue()) {
    mapPanel.addLayer(image.clip(geometry), nirVis,  'False Colour NIR', true);
  }
  if (showNDVI.getValue()) {
    mapPanel.addLayer(ndvi.clip(geometry),  ndviVis, 'NDVI', true);
  }

  // Boundary outline
  mapPanel.addLayer(
    ee.Image().byte().paint({ featureCollection: ee.FeatureCollection([selected]), width: 2 }),
    { palette: [COLORS.accent.replace('#','')] },
    'Boundary'
  );

  // ---- Compute statistics ----
  var stats = ndvi.reduceRegion({
    reducer:    ee.Reducer.mean()
      .combine(ee.Reducer.min(),  '', true)
      .combine(ee.Reducer.max(),  '', true)
      .combine(ee.Reducer.stdDev(), '', true),
    geometry:   geometry,
    scale:      30,
    maxPixels:  1e10,
    bestEffort: true
  });

  // Area stats per NDVI class
  var water    = ndvi.lt(0);
  var bareSoil = ndvi.gte(0).and(ndvi.lt(0.2));
  var sparse   = ndvi.gte(0.2).and(ndvi.lt(0.4));
  var moderate = ndvi.gte(0.4).and(ndvi.lt(0.6));
  var dense    = ndvi.gte(0.6);

  var pixelArea = ee.Image.pixelArea().divide(1e6); // km²

  var waterArea    = pixelArea.updateMask(water).reduceRegion({ reducer: ee.Reducer.sum(), geometry: geometry, scale: 30, maxPixels: 1e10, bestEffort: true });
  var bareSoilArea = pixelArea.updateMask(bareSoil).reduceRegion({ reducer: ee.Reducer.sum(), geometry: geometry, scale: 30, maxPixels: 1e10, bestEffort: true });
  var sparseArea   = pixelArea.updateMask(sparse).reduceRegion({ reducer: ee.Reducer.sum(), geometry: geometry, scale: 30, maxPixels: 1e10, bestEffort: true });
  var moderateArea = pixelArea.updateMask(moderate).reduceRegion({ reducer: ee.Reducer.sum(), geometry: geometry, scale: 30, maxPixels: 1e10, bestEffort: true });
  var denseArea    = pixelArea.updateMask(dense).reduceRegion({ reducer: ee.Reducer.sum(), geometry: geometry, scale: 30, maxPixels: 1e10, bestEffort: true });

  // Evaluate stats and update UI
  ee.Dictionary({
    mean:    stats.get('ndvi_mean'),
    min:     stats.get('ndvi_min'),
    max:     stats.get('ndvi_max'),
    std:     stats.get('ndvi_stdDev'),
    scenes:  sceneCount,
    water:   waterArea.get('area'),
    bare:    bareSoilArea.get('area'),
    sparse:  sparseArea.get('area'),
    moderate:moderateArea.get('area'),
    dense:   denseArea.get('area')
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

    // Vegetation health label
    var healthLabel = 'N/A';
    var healthColor = COLORS.textMuted;
    if (result.mean !== null) {
      if (result.mean >= 0.6)       { healthLabel = '🌳 Dense / Healthy';   healthColor = '#1a6b3a'; }
      else if (result.mean >= 0.4)  { healthLabel = '🌿 Moderate';          healthColor = '#4a8c2a'; }
      else if (result.mean >= 0.2)  { healthLabel = '🌾 Sparse / Stressed'; healthColor = '#b07d00'; }
      else if (result.mean >= 0)    { healthLabel = '🪨 Bare / Urban';      healthColor = '#c0440a'; }
      else                          { healthLabel = '💧 Water / Non-veg.';  healthColor = '#0077b6'; }
    }

    statsCard.clear();
    statsCard.add(infoRow('Province:', provinceName));
    statsCard.add(infoRow('Scenes used:', String(result.scenes || 0)));
    statsCard.add(infoRow('Date range:', startDate.getValue() + ' → ' + endDate.getValue()));
    statsCard.add(divider());
    statsCard.add(ui.Label({ value: 'NDVI Values', style: { color: COLORS.accent2, fontSize: '11px', fontWeight: 'bold', margin: '4px' } }));
    statsCard.add(infoRow('Mean NDVI:', mean));
    statsCard.add(infoRow('Min NDVI:', min));
    statsCard.add(infoRow('Max NDVI:', max));
    statsCard.add(infoRow('Std Dev:', std));
    statsCard.add(divider());
    statsCard.add(ui.Label({ value: 'Overall Health', style: { color: COLORS.accent2, fontSize: '11px', fontWeight: 'bold', margin: '4px' } }));
    statsCard.add(ui.Label({ value: healthLabel, style: { color: healthColor, fontSize: '13px', fontWeight: 'bold' } }));
    statsCard.add(divider());
    statsCard.add(ui.Label({ value: 'Area by Class (km²)', style: { color: COLORS.accent2, fontSize: '11px', fontWeight: 'bold', margin: '4px' } }));
    statsCard.add(infoRow('Water/Non-veg:', result.water   !== null ? result.water.toFixed(1)    + ' km²' : 'N/A'));
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
**[Sample Notebook](sample-notebook.ipynb)**

[YOUR PROJECT DESCRIPTION — one or two sentences: what you did, what data you used,
and what you found or built.]

`Python` `pandas` `Folium`

[View Project →](sample-notebook.ipynb){ .md-button }
</div>

</div>
