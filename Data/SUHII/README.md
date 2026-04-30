# Surface Urban Heat Island Intensity (SUHII) — Data Pipeline & Computation

Data pipeline and sensitivity analysis for SUHII estimation in Chennai, India.

---

## 1. Data Sources

| Data Type | Dataset | Source | URL | License |
|---|---|---|---|---|
| Administrative Boundary | Chennai administrative boundary | GADM | [gadm.org](https://gadm.org/data.html) | Open Data |
| Land Surface Temperature (LST) | Landsat 8 and 9 Collection 2 Level 2 | USGS/NASA | [earthexplorer.usgs.gov](https://earthexplorer.usgs.gov/) | Public Domain |
| Vegetation Index (NDVI) | Sentinel-2 Harmonized | Copernicus (ESA) | [browser.dataspace.copernicus.eu](https://browser.dataspace.copernicus.eu/) | Public Domain |
| Land Use / Land Cover (LULC) | Dynamic World V1 | Google Earth Engine | [GEE Catalog](https://developers.google.com/earth-engine/datasets/catalog/GOOGLE_DYNAMICWORLD_V1) | Public Domain |
| Elevation | SRTM DEM | USGS/NASA | [earthexplorer.usgs.gov](https://earthexplorer.usgs.gov/) | Public Domain |

---

## 2. SUHII Computation

```
SUHII = μ(LSTurban) − μ(LSTrural)
```

For the full step-by-step methodology, refer to the [Urban Heat Technical Manual](https://github.com/sakinamammad/Urban-Heat-Technical-Manual). The SUHII calculation was implemented using the [SUHII Analysis Tool](https://github.com/nishansrepo/SUHIIAnalysisTool/).

### Coastal City Consideration

In coastal cities, land surface temperature (LST) patterns may be influenced by marine effects such as sea-breeze circulation, which can introduce systematic differences between inland and coastal rural areas and affect the definition of the rural baseline in SUHII estimation. To reduce potential bias, the rural reference should be selected to maintain comparable proximity to the coastline as the urban area. In practice, this consideration can be further evaluated through a robustness check by excluding rural pixels near the coastline and comparing SUHII results under different exclusion distances. In Chennai, this check showed only minimal variation in the rural baseline, indicating that coastal proximity does not materially affect the SUHII estimation in this case.

---

## 3. Output

Aggregated SUHII values at the hexagonal grid level (spatial heat intensity map).

---

## Tools

- [SUHII Analysis Tool](https://github.com/nishansrepo/SUHIIAnalysisTool/)
- [Urban Heat Technical Manual](https://github.com/sakinamammad/Urban-Heat-Technical-Manual)

## References

- Stewart, I. D., & Mills, G. (2021). The urban heat island: A guidebook. Elsevier. [PDF](https://urban-climate.org/wp-content/uploads/2023/10/Stewart_Mills_UHI_Guidebook_2021.pdf)
