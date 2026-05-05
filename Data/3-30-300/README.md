# 3-30-300 Green Infrastructure Framework for Chennai

## Quick Reference Guide

---

## Overview

Evidence-based assessment of urban green infrastructure using the 3-30-300 rule (Konijnendijk, 2021). Applied to Chennai, India using freely available satellite imagery and geospatial datasets.

---

## The 3-30-300 Framework

Three quantitative indicators for healthy, livable cities:

1. **3 Trees Visible** - Every resident should see at least 3 trees from their home
2. **30% Canopy Cover** - Neighborhoods should maintain ≥30% tree canopy coverage  
3. **300m to Park** - Everyone should live within 300m of quality green space (≥1 hectare)

**Reference:** Konijnendijk, C. C. (2021). Promoting health and wellbeing through urban forests – Introducing the 3-30-300 rule. *IUCN Urban Alliance*. https://iucnurbanalliance.org/promoting-health-and-wellbeing-through-urban-forests-introducing-the-3-30-300-rule/

---

## Data Sources

| Dataset | Source | Specifications | Usage |
|---------|--------|----------------|-------|
| **Landsat 9 NDVI** | USGS EarthExplorer | Scene: LC09_L2SP_142051_20250424<br>Date: April 24, 2025<br>Resolution: 30m | Tree visibility proxy<br>Canopy cover assessment |
| **Parks (629)** | OpenCity Foundation & Greater Chennai Corporation (2024) | Point features<br>Categories: Small/Medium/Large | 300m accessibility analysis |
| **Road Network** | OpenStreetMap (2024) | ~8,500 km<br>Topology validated | Network distance for park access |
| **Hexagon Grid** | Generated (ArcGIS Pro) | 132 cells × 2 km²<br>WGS84 UTM Zone 44N | Spatial aggregation unit |
| **Heat Risk Scores** | Check previous analysis (SUHII) | SUHII × SVI × 100<br>Range: 0-356.912 | Priority zone overlay |

**References:**
- OpenCity Foundation & Greater Chennai Corporation. (2024). Chennai parks dataset.
- U.S. Geological Survey. (2025). Landsat 9 OLI/TIRS Collection 2 Level-2. https://earthexplorer.usgs.gov/

---

## Methodology

### Indicator 1: Three Trees Visible

**Challenge:** Cannot count individual trees from 30m satellite imagery  
**Solution:** Focal NDVI analysis as proxy for clustered vegetation

**Workflow:**
1. Focal Statistics (50m radius, mean NDVI)
2. Threshold: NDVI ≥ 0.35 → clustered woody vegetation
3. Zonal Statistics → proportion of hexagon meeting threshold
4. Output: `"3 Trees Score"` (0-1)

**Reference:** Weier, J., & Herring, D. (2000). Measuring vegetation (NDVI & EVI). *NASA Earth Observatory*. https://earthobservatory.nasa.gov/features/MeasuringVegetation

---

### Indicator 2: 30% Canopy Cover

**Challenge:** Distinguish tree canopy from grass/shrubs  
**Solution:** NDVI threshold 0.4 (validated for dense tree canopy)

**Workflow:**
1. Raster Calculator: Binary mask (NDVI > 0.4 = canopy, else non-canopy)
2. Zonal Statistics → mean per hexagon = canopy percentage
3. Normalize: `"Canopy Score" = "Chennai_grid.canopypct" / 30.0` (capped at 1.0)
4. Output: `"Canopy Score"` (0-1)

**Reference:** Carlson, T. N., & Ripley, D. A. (1997). On the relation between NDVI, fractional vegetation cover, and leaf area index. *Remote Sensing of Environment*, *62*(3), 241-252. https://doi.org/10.1016/S0034-4257(97)00104-1

---

### Indicator 3: 300m Park Access

**Challenge:** Measure realistic pedestrian access (not straight-line distance)  
**Solution:** Network service area analysis

**Workflow:**
1. Network Analyst: Service Area (300m walking distance from 629 parks)
2. Spatial Join: Service area polygons with hexagons
3. Calculate % of hexagon within 300m
4. Apply scoring (raw percentage normalized 0-1)
5. Output: `"Park Access Score"` (0-1)

**Reference:** Apparicio, P., Abdelmajid, M., Riva, M., & Shearmur, R. (2008). Comparing alternative approaches to measuring the geographical accessibility of urban health services: Distance types and aggregation-error issues. *International Journal of Health Geographics*, *7*(1), 7. https://doi.org/10.1186/1476-072X-7-7

---

### Combined Greenness Index

**Formula:**
```
Green Index = (3 Trees Score + Canopy Score + Park Access Score) / 3
```

**Interpretation:**
- 0.0 = Fails all three standards
- 1.0 = Meets all three standards
- Median: 0.030 (severe deficit)

**Equal weighting rationale:** All three indicators contribute independently to livability; no evidence one is more important than others.

---

### Priority Intervention Zones

**Overlay Analysis:** Identify areas with BOTH green deficit AND heat vulnerability

**Thresholds:**
- **Low Greenness:** Bottom 50% (greenness_333 < 0.030) → ~66 hexagons
- **High Heat Risk:** Top 25% (heat_risk > 134.57) → ~33 hexagons [from previous analysis -> check "../../SUHII"]

**Operation:** Spatial Intersect → **10 priority zones**

**Important:** This represents spatial co-occurrence (correlation), not causation. Areas with low greenness AND high heat risk would benefit most from green infrastructure investment.

**References:**
- Cutter, S. L., Boruff, B. J., & Shirley, W. L. (2003). Social vulnerability to environmental hazards. *Social Science Quarterly*, *84*(2), 242-261. https://doi.org/10.1111/1540-6237.8402002
- Zhou, D., Xiao, J., Bonafoni, S., Berger, C., Deilami, K., Zhou, Y., ... & Sobrino, J. A. (2019). Satellite remote sensing of surface urban heat islands: Progress, challenges, and perspectives. *Remote Sensing*, *11*(1), 48. https://doi.org/10.3390/rs11010048

---

## Tools & Software

- **GIS Platform:** ArcGIS Pro 3.2
- **Coordinate System:** WGS 1984 UTM Zone 44N (EPSG:32644)
- **Key Tools:**
  - Focal Statistics (Spatial Analyst)
  - Raster Calculator
  - Zonal Statistics
  - Network Analyst (Service Area)
  - Select by Attributes
  - Spatial Join

**Open-Source Alternative:** QGIS with GRASS/SAGA plugins can replicate this workflow

---

## Outputs

**Maps Produced:**
1. Three Trees Visibility (green gradient)
2. 30% Canopy Cover (green gradient)
3. 300m Park Access (green gradient)
4. Combined 3-30-300 Greenness Index (green gradient)
5. Priority Intervention Zones (overlay: yellow=low green, orange=high heat, red=both)

**Data Products:**
- `Chennai_Green_Index_3-30-300.shp` - Combined greenness scores (132 hexagons)
- `Chennai_Priority_Zones.shp` - 10 priority intervention areas
- `3-30-300_GreenIndex_Chennai` - .xls file (Attribute table of 3-30-300 Layer)

---

## Reproducibility

**Fully replicable with:**
- Freely available Landsat imagery  
- OpenStreetMap road networks  
- Municipal park inventories  
- Standard GIS software  

**Scalable to any city with:**
- Landsat coverage (global)
- Documented park locations
- Road network data

---

## Key Findings

- **Severe green deficit:** Median greenness = 0.030 (bottom 50% < threshold)
- **0 hexagons meet 30% canopy standard
- **10 priority zones** identified for urgent green infrastructure intervention

---

## Full Citations

### Framework
- Konijnendijk, C. C. (2021). Promoting health and wellbeing through urban forests – Introducing the 3-30-300 rule. *IUCN Urban Alliance*. https://iucnurbanalliance.org/promoting-health-and-wellbeing-through-urban-forests-introducing-the-3-30-300-rule/
- Konijnendijk, C. C. (2023). Evidence-based guidelines for greener, healthier, more resilient neighbourhoods: Introducing the 3-30-300 rule. *Journal of Forestry Research*, *34*(3), 821-830. https://doi.org/10.1007/s11676-022-01523-z

### Methodology
- Apparicio, P., Abdelmajid, M., Riva, M., & Shearmur, R. (2008). Comparing alternative approaches to measuring the geographical accessibility of urban health services: Distance types and aggregation-error issues. *International Journal of Health Geographics*, *7*(1), 7. https://doi.org/10.1186/1476-072X-7-7
- Carlson, T. N., & Ripley, D. A. (1997). On the relation between NDVI, fractional vegetation cover, and leaf area index. *Remote Sensing of Environment*, *62*(3), 241-252. https://doi.org/10.1016/S0034-4257(97)00104-1
- Cutter, S. L., Boruff, B. J., & Shirley, W. L. (2003). Social vulnerability to environmental hazards. *Social Science Quarterly*, *84*(2), 242-261. https://doi.org/10.1111/1540-6237.8402002
- Zhou, D., Xiao, J., Bonafoni, S., Berger, C., Deilami, K., Zhou, Y., ... & Sobrino, J. A. (2019). Satellite remote sensing of surface urban heat islands: Progress, challenges, and perspectives. *Remote Sensing*, *11*(1), 48. https://doi.org/10.3390/rs11010048

### Data
- OpenCity Foundation & Greater Chennai Corporation. (2024). Chennai parks dataset.
- U.S. Geological Survey. (2025). Landsat 9 OLI/TIRS Collection 2 Level-2. https://earthexplorer.usgs.gov/

---

## Contact

**Project Team:** Carnegie Mellon University & World Bank Group Capstone 2026  
**Authors:** Liufei Chen, Vashishth Doshi, Farrukh Masood, Yuhang Zhou

---

## License

MIT License

---

**Last Updated:** May 2026

---

*This README provides a quick-start guide to understanding and replicating the 3-30-300 green infrastructure assessment methodology. For detailed technical documentation, see the full project report.*
