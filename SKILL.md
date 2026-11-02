---
name: geoskill-mangrove-mapping
description: '融合 NDVI 高值、NDWI 水陆边界、海岸缓冲与 SAR 潮汐多次散射特征，规则融合提取红树林分布，输出红树林 GeoTIFF、得分栅格、面积统计与多期变化。Maps mangroves by fusing NDVI, NDWI coast buffer and SAR tidal signatures.'
---

# 红树林制图 | Mangrove Mapping

Fuses multispectral and SAR features to map mangrove distribution in the intertidal zone of tropical/subtropical coasts, and supports multi-temporal change (gain/loss) detection. Suitable for mangrove resource baseline surveys, coastal ecological monitoring and conservation effectiveness assessment.

Four remotely detectable characteristics of mangroves:

- **High NDVI**: dense evergreen vegetation with high near-infrared reflectance and low red reflectance; NDVI is typically > 0.5.
- **NDWI boundary**: McFeeters NDWI=(Green−NIR)/(Green+NIR) is used to locate water bodies, and a distance transform yields the distance from each land pixel to the coastline.
- **Coastal buffer**: mangroves occur only within a certain buffer distance from the coastline (intertidal zone).
- **SAR tidal influence**: tidal inundation of the intertidal zone causes multiple scattering in tree trunks, making SAR backscatter brighter.

The algorithm converts the above features into [0,1] memberships and fuses them by rules (product + SAR modulation), then thresholding yields the mangrove mask. `--synthetic` mode generates a physically consistent coastal scene (offline).

## Dependencies / 依赖

```bash
pip install numpy rasterio scipy
```

## Usage / 使用方法

### Basic Usage (synthetic data, offline)

```bash
python geoskill-mangrove-mapping.py --bbox 110.0 21.0 111.0 22.0 --synthetic --output-dir ./output
```

### Example 1: multi-temporal change detection

```bash
python geoskill-mangrove-mapping.py \
    --bbox 110.0 21.0 111.0 22.0 \
    --synthetic --n-dates 3 \
    --output-dir ./change
```

### Example 2: adjust the fusion threshold

```bash
python geoskill-mangrove-mapping.py \
    --bbox 110.0 21.0 111.0 22.0 \
    --synthetic --score-threshold 0.5 \
    --output-dir ./thr
```

### Example 3: real multiband imagery

```bash
python geoskill-mangrove-mapping.py \
    --input coastal.tif \
    --output-dir ./real
```

Input band order: green / red / nir / swir (required), with the 5th band being SAR backscatter (optional; when missing, the algorithm degrades to NDVI + coastal buffer only).

## Output / 输出

| File | Format | Description |
|---|---|---|
| `mangrove.tif` | GeoTIFF (float32) | Final-epoch mangrove mask (1 = mangrove), EPSG:4326 |
| `mangrove_score.tif` | GeoTIFF (float32) | Fusion score [0,1] |
| `mangrove_change.tif` | GeoTIFF (float32) | Multi-temporal change (1=persistent 2=gain 3=loss), only when n-dates≥2 |
| `mangrove_area.json` | JSON | Pixel/area statistics (m², ha, km²) + change statistics |
| `output-manifest.json` | JSON | Run manifest (inputs/outputs/QA/software versions) |

## Data Source / 数据源 / Source

- **Synthetic mode**: generated locally, no external data source
- **Real mode**: user-provided multiband GeoTIFF (e.g., coregistered Sentinel-2 + Sentinel-1 products)

## Privacy / 隐私声明 / Privacy

- Runs fully offline by default; no network requests are made
- All computation is done locally; no user data is uploaded

## License / License

MIT

---

> 中文完整版：需要时再读 [references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)，不要默认全量加载。
