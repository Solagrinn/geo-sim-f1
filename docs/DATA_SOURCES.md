# Data sources

Checked in October 2026. Licenses and access terms change; re-check before publishing anything.

## F1 circuits

| Data | What | Resolution | License / access | Notes |
|---|---|---|---|---|
| **AHN4** (Netherlands) | LiDAR DSM + DTM | 0.5 m, 5 cm vertical accuracy, 2020–2022 | CC-0, free | Whole Netherlands. CRS EPSG:28992 (RD New). Best data for Zandvoort. |
| Dutch aerial photos (PDOK) | Orthophotos | Very high resolution | Open | Check PDOK for current products and download method. |
| Wallonia (Belgium) | LiDAR surface/terrain models (2013–2014, 2021–2022), orthophotos | High | Open (check terms) | For Spa-Francorchamps; services at geoservices.wallonie.be. |
| Environment Agency (England) | LiDAR DSM/DTM | 1 m and 2 m | Open Government Licence | Silverstone. Open high-res imagery is not available in England. |
| **FastF1** (Python library) | Laps, X/Y/Z position (1/10 m, local track coordinates), speed, RPM, gear, throttle, brake, DRS | Per session | Unofficial access to F1 live timing; fine for learning | X/Y are not lat/lon: georeferencing needed. |
| OpenStreetMap | Track outline, grandstands, roads | Vector | ODbL | Export via Overpass or Geofabrik. |

## Earthquake (6 February 2023, Kahramanmaraş)

| Data | What | Resolution | License / access | Notes |
|---|---|---|---|---|
| **Maxar Open Data** | Pre- and post-event satellite imagery | High (sub-metre class) | CC BY-NC 4.0; on AWS (`maxar-opendata`, us-west-2), no AWS account needed; STAC catalog | Whole cities, before and after. Index: github.com/opengeos/maxar-open-data; leafmap Turkey notebook. |
| **OpenAerialMap** | Drone imagery uploaded after the earthquake | ~11 cm (Havadan Harita); Help.NGO high-res | CC BY 4.0 | Patches, not whole cities. Havadan Harita via Yer Çizenler account: `map.openaerialmap.org/#/36.265,37.074,10/user/63e60f41c6ef740006cfd0e9`. Help.NGO: `map.openaerialmap.org/#/36.9218,37.5701,15/user/63e9ccceca43600005f4d8e5`. Help.NGO sites (~11.5 km² total): Kahramanmaraş, Antakya, Elbistan, Şekeroba, Nurdağı, İslahiye, Gökçedere. |
| **Copernicus EMS EMSR648** | Damage grading maps + vector data | Building level | Downloadable from the EMS site | 20 areas, 53 maps, ~4,000 damaged/destroyed buildings or blocks, 72 km roads. |
| HOT destroyed buildings | OSM export | Vector | ODbL, on HDX | Compare with Copernicus. |
| **Overture Maps buildings** | Global building footprints (OSM, Microsoft, Google, others), some heights | Vector (GeoParquet on S3/Azure) | ODbL | Pre-event outlines for the building database. |
| NASA JPL ARIA damage proxy maps | Radar change detection (ALOS-2: İslahiye, Kahramanmaraş, Nurdağı; also Sentinel-1) | 30 m | License not stated; check NASA/Copernicus terms | `aria-share.jpl.nasa.gov/20230206_Turkey_EQ/DPM/`; also WMS. Neighbourhood level, preliminary. |
| **USGS ShakeMap** | PGA, PGV, MMI, spectral acceleration; rupture geometry | Grid | Public | Events `us6000jllz` (M7.8 Pazarcık) and `us6000jlqa` (M7.5 Elbistan). ESRI raster, grid.xml, GeoJSON contours. |
| USGS Global Vs30 | Site stiffness estimated from slope | ~1 km (30 arc-seconds) | Public | GMT `.grd` (631 MB zip). An estimate, not measured. |
| AFAD station dataset (Zenodo 15606222) | Vs30 and site parameters for 44 stations + damage status | Points | CC BY 4.0 | Small Excel file. |
| Landslide inventory (Aperta 274145) | 2,596 co-seismic landslides | Vector (KMZ) | CC BY-SA 4.0 | Mapped from 25 cm post-event aerial orthophotos (imagery itself not public). |
| Copernicus DEM | Global elevation | 30 m / 90 m | Free | Via Earth Search STAC. Terrain only, too coarse for buildings. |
| Earth Search STAC API | Sentinel-2 L2A COGs, Landsat, Copernicus DEM, NAIP | Varies | Free, no account | `https://earth-search.aws.element84.com/v1` |

### Not openly available (request only)

- **Ministry of Environment and Urbanization (ÇŞİDB CBS Genel Müdürlüğü):** post-event drone surveys (Feb 2023) with orthophotos, **5 cm DSM** and 3D city twins over parts of Hatay, Kahramanmaraş, Gaziantep (incl. Nurdağı, İslahiye), Adıyaman, Osmaniye, Adana; plus a **pre-event 10 cm DSM and true orthophoto** (2015–2017 DASK project). The ideal before/after pair for collapse detection. Ask by email for non-commercial use.
- **MTA geological maps:** 1:25,000 (5,547 sheets, ~99% complete) and 1:500,000. Paid (nominal fee); reproduction or publication needs MTA's written permission.
- **Havadan Harita:** may share more data or raw photos on request.

## Infrastructure notes

- **MinIO Community Edition** is retired (no images since Oct 2025, repository archived). Use **SeaweedFS** (or Garage) as local S3.
- **LocalStack:** Community Edition ended March 2026; the image now needs a free account and auth token; free tier is non-commercial only. Not needed for this project.
- **AWS free plan** (accounts created after 15 July 2025): credits ($100 at sign-up, up to $100 more by trying services), 6 months or until credits run out; then the account closes (data kept 90 days) unless upgraded. Some actions auto-upgrade the account (joining AWS Organizations, Control Tower, Partner Network, Enterprise Agreement): avoid them.
