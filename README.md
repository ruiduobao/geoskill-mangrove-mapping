# Mangrove Mapping (geoskill-mangrove-mapping)

> Maps mangroves by fusing NDVI high values, NDWI land-water boundaries, coastal buffers, and SAR tidal multi-scattering signatures; outputs a mangrove GeoTIFF, score rasters, area statistics, and multi-date change. Maps mangroves by fusing NDVI, NDWI coast buffer and SAR tidal signatures.

---

## 1. Overview

Fuses multispectral and SAR features to extract mangrove distribution in the intertidal zone of tropical / subtropical coasts, and supports multi-date change (gain / loss) detection. Suitable for mangrove baseline surveys, coastal ecological monitoring, and conservation effectiveness assessment. Four remotely detectable characteristics of mangroves:
- **High NDVI**: dense evergreen vegetation — high NIR reflectance, low red reflectance, NDVI typically > 0.5.
- **NDWI boundary**: water is located using the McFeeters NDWI=(Green−NIR)/(Green+NIR); a distance transform yields the distance from each land pixel to the coastline.
- **Coastal buffer**: mangroves occur only within a certain buffer distance from the coastline (intertidal zone).
- **SAR tidal influence**: tidal inundation in the intertidal zone causes multiple scattering from trunks, making SAR backscatter brighter.
The algorithm converts the above features into [0,1] membership values and fuses them by rules (product + SAR modulation), then thresholds the result to obtain the mangrove mask. The `--synthetic` mode generates physically consistent coastal scenes (offline).

## 3. Quick Start

```bash
pip install -r requirements.txt
python geoskill-mangrove-mapping.py --bbox 116 39 117 40 --synthetic --output-dir ./out
```

## 4. CLI Parameters

Run `python <skill>.py --help` for the full list. Common parameters:

| Parameter | Type | Description |
|---|---|---|
| `--bbox` | `float[4]` | WGS84 bounding box `min_lon min_lat max_lon max_lat` |
| `--input` | `path` | Local input file (GeoJSON/GeoTIFF/etc.) |
| `--output-dir` | `path` | Output directory (default `./output`) |
| `--synthetic` | `flag` | Use synthetic data instead of real input |
| `--quiet` | `flag` | Suppress non-essential stdout |

## 5. Input / Output

| File | Format | Description |
|---|---|---|
| `mangrove.tif` | GeoTIFF (float32) | Mangrove mask of the latest epoch (1=mangrove), EPSG:4326 |
| `mangrove_score.tif` | GeoTIFF (float32) | Fusion score [0,1] |
| `mangrove_change.tif` | GeoTIFF (float32) | Multi-date change (1=persistent, 2=gain, 3=loss); only when n-dates≥2 |
| `mangrove_area.json` | JSON | Pixel/area (m², ha, km²) + change statistics |
| `output-manifest.json` | JSON | Run manifest (input/output/QA/software versions) |


## 6. Technical Principle

(See SKILL.md for details.)

## 8. License

MIT License. See [`LICENSE`](./LICENSE) for full text.

---

> 中文完整版：[references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)
