# Roadmap

Small increments, in order. Each one ends with something that runs. "Learn" is what the step is really for.

## Phase 0: Tooling and Python through geodata

- [x] **0.1 Setup.** WSL2, Docker Desktop (WSL backend), git, Python 3 in WSL, a virtual environment.
  Learn: venv vs `node_modules`, pip/`requirements.txt` vs npm/`package.json`.
- [ ] **0.2 Python crash course in a tiny script.** Read a JSON file, functions, dicts, lists, comprehensions, f-strings, type hints, `if __name__ == "__main__"`.
  Learn: Python ↔ JS/Java differences.
- [ ] **0.3 First GeoTIFF.** Download one small AHN4 DSM tile around Zandvoort. Open it with rasterio and print CRS, bounds, size, resolution, dtype, band count, nodata; recreate a mini `gdalinfo`.
  Learn: the GeoTIFF concepts already discussed, now in code; `with` blocks.
- [ ] **0.4 Make a COG.** Convert with `gdal_translate -of COG` (CLI), then from Python. Compare `gdalinfo` of both files (tiling, overviews).
  Learn: why COGs exist.
- [ ] **0.5 Read a value at a coordinate.** Take a lat/lon, reproject it to EPSG:28992 with pyproj, convert to row/col, read the height. Then use rasterio's `sample()`.
  Learn: CRS, affine transform, pixel ↔ world coordinates.

## Phase 1: Containers and the first service

- [ ] **1.1 Docker basics.** Dockerfile for the Phase 0 script; build and run it.
  Learn: images, containers, layers, volumes.
- [ ] **1.2 PostGIS.** `docker compose` with `postgis/postgis`; connect with psql; create a table with a geometry column; run `ST_Area`, `ST_Distance`, `ST_Transform`.
  Learn: SRIDs, geometry vs geography.
- [ ] **1.3 Django project `core`.** `startproject`, settings from env vars, `django.contrib.gis`, connect to PostGIS, run in compose.
  Learn: Django ↔ Spring Boot map (settings ≈ application.properties, apps ≈ modules, models ≈ JPA entities, migrations ≈ Flyway, views ≈ controllers, urls ≈ request mappings).
- [ ] **1.4 First model.** `Project` (name, boundary polygon, working CRS); migrations; Django admin.
- [ ] **1.5 First API.** Django REST Framework serializer + viewset; GeoJSON output (djangorestframework-gis). Test with curl/HTTPie.
- [ ] **1.6 First tests.** pytest + pytest-django for the API.

## Phase 2: Storage and the map

- [ ] **2.1 S3 locally.** SeaweedFS in compose; with boto3: create bucket, upload, list, download, presigned URL.
  Learn: buckets, keys, objects; the same API as AWS S3.
- [ ] **2.2 Dataset catalog.** `datasets` Django app (still inside the monolith): upload a COG to S3, register it as a `Dataset`.
- [ ] **2.3 TiTiler.** Add to compose, reading COGs from SeaweedFS; open a tile URL in the browser.
- [ ] **2.4 Frontend.** Vite + React + TS + MapLibre: basemap, list of projects from the API, the COG as a layer via TiTiler.
- [ ] **2.5 Styling rasters.** DSM as colormap and hillshade via TiTiler parameters; orthophoto on top.
- [ ] **2.6 CI.** GitHub Actions: ruff, pytest, frontend build.

## Phase 3: Background work

- [ ] **3.1 Celery.** Why long GDAL jobs can't run inside a request. Celery + Redis broker; task: uploaded GeoTIFF → COG → S3; `status` on `Dataset`.
- [ ] **3.2 Source adapters.** `AhnAdapter` with `find(bbox)` and `fetch(item)`; shared `ingest(source, project)` pipeline: clip → reproject → COG → upload → register.
- [ ] **3.3 Status in the UI.** Show processing status (polling first; WebSockets/SSE optional later).

## Phase 4: Vector data

- [ ] **4.1 Import vectors.** Zandvoort track outline and grandstands from OSM (GeoJSON) and a Shapefile into PostGIS, first with `ogr2ogr`, then in code.
  Learn: Shapefile parts (.shp, .shx, .dbf, .prj), why GeoPackage exists.
- [ ] **4.2 Vector tiles.** Martin in compose; style the layers in MapLibre.
- [ ] **4.3 Drawing.** Draw lines/polygons in the frontend, save as `Annotation`.

## Phase 5: Analysis service (first real microservice)

- [ ] **5.1 FastAPI service.** Health endpoint, Dockerfile, compose. Compare with Django: when to use which.
- [ ] **5.2 Elevation profile.** Line → sampled heights from the DSM COG; chart in the frontend.
- [ ] **5.3 Distance and area** in a metric CRS. Show the Web Mercator error side by side.
- [ ] **5.4 Volume and height difference** (two DSMs, or DSM − DTM for object heights).
- [ ] **5.5 Gateway and auth.** Traefik or Nginx routing; core issues JWTs, analysis verifies them.

## Phase 6: Telemetry

- [ ] **6.1 Explore FastF1** in a script/notebook: load a Dutch GP session; laps; position data (X/Y/Z in 1/10 m, local track coordinates, not lat/lon).
- [ ] **6.2 Georeferencing.** Fit a similarity/affine transform from FastF1's track shape to the OSM track geometry (least squares); measure the residual error.
  Learn: ground control points, transformations.
- [ ] **6.3 Telemetry service.** Import session → laps as LineStrings with per-point speed/throttle/brake, in its own database.
- [ ] **6.4 Frontend.** Lap colored by speed, replay animation, two-driver comparison, elevation profile of a lap.

## Phase 7: Event-driven microservices (RabbitMQ)

- [ ] **7.1 RabbitMQ basics.** Add to compose; management UI; publish/consume "hello" with pika.
  Learn: exchanges, queues, routing keys, acknowledgements.
- [ ] **7.2 Domain events.** `project.created` → dataset service ingests → `dataset.ready` → core updates.
- [ ] **7.3 Extract the dataset service** from the Django monolith into its own service and database.
  Learn: no foreign keys across services, eventual consistency, idempotency, retries, dead-letter queues.
- [ ] **7.4 Telemetry reacts to events** (new circuit → import sessions).
- [ ] **7.5 Prove it's generic:** add Spa through the same pipeline with a new adapter only.

## Phase 8: Second domain: earthquake response

- [ ] **8.1 New project** for Nurdağı or İslahiye (first check drone coverage on OpenAerialMap). Adapters: Maxar Open Data (STAC), OpenAerialMap, USGS ShakeMap.
- [ ] **8.2 Vectors:** Overture building footprints, Copernicus EMSR648 damage grading, HOT destroyed-buildings export.
- [ ] **8.3 Zonal statistics per building** (PGA, Vs30, radar damage value) and conflict detection between damage sources.
- [ ] **8.4 Damage assessment module:** review queue, statuses, change history, field assignments.
- [ ] **8.5 Before/after swipe view;** multiple imagery sources with dates and resolutions.

## Phase 9: Production and AWS

- [ ] **9.1 Hardening:** presigned uploads, input validation, permissions, rate limits.
- [ ] **9.2 AWS account (free plan),** budget alert, IAM user with least privilege, S3 bucket; switch config from SeaweedFS to S3.
- [ ] **9.3 Deploy** compose to a small EC2 with HTTPS (RDS optional).
- [ ] **9.4 Observability and performance:** structured logs, basic metrics, load-test tile serving (k6).
- [ ] **9.5 Portfolio:** README with architecture diagram, `DECISIONS.md` cleaned up, demo video.

## Phase 10: Extras

- [ ] React Native (Expo) app: field assignments with offline tiles (earthquake); spectator view (F1).
- [ ] GraphQL endpoint.
- [ ] Viewshed analysis (what each grandstand can see).
- [ ] Cypress end-to-end tests.
