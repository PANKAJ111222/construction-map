# Delhi NCR Construction Tracker 🏗️

A live construction site mapping dashboard for Delhi and its NCR region, combining **OpenStreetMap (OSM)** live data, **Sentinel-2 satellite imagery** from Google Earth Engine, and a custom **GeoJSON construction detection layer** generated via change-detection algorithms.

![Map Preview](https://img.shields.io/badge/Region-Delhi%20NCR-blue) ![Satellite](https://img.shields.io/badge/Satellite-Sentinel--2-green) ![License](https://img.shields.io/badge/License-MIT-yellow)

---

## Features

- **Live OSM construction data** — fetches real-time building, road, and land-use construction sites from OpenStreetMap via Overpass API
- **Sentinel-2 satellite layer** — overlays true-colour and change-detection imagery from Google Earth Engine
- **GeoJSON construction sites** — 59 detected construction zones over Delhi NCR with intensity scoring (High / Medium / Low)
- **Street View integration** — click any point on the map to jump into Google Street View
- **Filter layers** — toggle buildings, roads, land development, and GeoJSON sites independently
- **Site list panel** — browse all detected sites with OSM / GeoJSON tabs and click to focus

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML, CSS, JavaScript (Vanilla) |
| Map | Google Maps JavaScript API |
| Live OSM data | Overpass API (via multiple mirrors) |
| Satellite imagery | Google Earth Engine (Sentinel-2 SR Harmonized) |
| Change detection | NDVI drop + NDBI rise + BSI rise composite score |
| GeoJSON processing | Python (rasterio, scipy, json) |

---

## Project Structure

```
delhi-ncr-construction-tracker/
│
├── index.html                           # Main dashboard (open in browser)
├── delhi_ncr_construction_sites.geojson # 59 detected construction polygons
├── gee_scripts/
│   └── delhi_ncr_construction_gee.js   # Google Earth Engine export script
└── README.md
```



---

## How the Construction Detection Works

The GeoJSON was generated using a 3-index change-detection algorithm on Sentinel-2 satellite imagery:

```
Construction Score = 0.4 × NDVI_drop + 0.35 × NDBI_rise + 0.25 × BSI_rise
```

| Index | What it measures | Construction signal |
|---|---|---|
| NDVI (Vegetation) | Greenness of land | Drops when vegetation is cleared |
| NDBI (Built-up) | Presence of buildings/roads | Rises when construction starts |
| BSI (Bare Soil) | Exposed soil | Rises when land is excavated |

**Before period:** November 2022 – January 2023  
**After period:** November 2023 – January 2024  
**Resolution:** 20 metres per pixel  
**Cloud filter:** < 15% cloud cover  
**Threshold:** 0.08 (detects meaningful change)

Sites are classified by intensity:

| Intensity | Score | Meaning |
|---|---|---|
| High | ≥ 0.20 | Dense, active construction |
| Medium | 0.13 – 0.20 | Moderate construction activity |
| Low | < 0.13 | Early-stage or low-density change |

---

## Re-generating the GeoJSON (Optional)

If you want to regenerate the construction sites with different parameters:

1. Open [Google Earth Engine Code Editor](https://code.earthengine.google.com)
2. Paste the contents of `gee_scripts/delhi_ncr_construction_gee.js`
3. Click **Run**
4. Go to the **Tasks** tab → click **Run** on the export task
5. Download from your Google Drive → `GEE_Delhi_Construction/` folder
6. Replace `delhi_ncr_construction_sites.geojson` with the new file

**Tuning tips:**

```javascript
// In the GEE script, adjust these values:
var THRESHOLD = 0.08;   // Lower = more sites detected, Higher = fewer but more certain

// Change the date ranges to compare different years:
var before = getS2Composite('2022-11-01', '2023-01-31');
var after  = getS2Composite('2023-11-01', '2024-01-31');
```

---

## Delhi NCR Coverage Area

The dashboard and GeoJSON cover the following bounding box:

```
North: 28.88°N  (above Rohtak)
South: 28.25°N  (below Palwal)
East:  77.55°E  (Meerut side)
West:  76.84°E  (Rewari side)
```

Districts covered: **Delhi, Gurugram, Faridabad, Noida, Ghaziabad, Sonipat, Bahadurgarh, Rohtak**

---

## Known Limitations

- OSM construction data depends on volunteer mapping — rural areas may have sparse coverage
- Sentinel-2 detects surface change, not confirmed construction permits
- Seasonal vegetation changes (crops, monsoon) can cause false positives if comparing different seasons
- GEE free tier has compute limits — large exports may take 10–30 minutes

---

## License

MIT License — free to use, modify, and distribute.

---

## Acknowledgements

- [OpenStreetMap](https://www.openstreetmap.org/) contributors
- [Google Earth Engine](https://earthengine.google.com/) — Sentinel-2 SR Harmonized dataset
- [Copernicus Programme](https://www.copernicus.eu/) — Sentinel-2 satellite data
- [osmtogeojson](https://github.com/tyrasd/osmtogeojson) — OSM to GeoJSON conversion
