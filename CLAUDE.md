# geo-sim-f1: F1 circuits + earthquake response

A learning project. One generic geospatial platform (ingest → store → serve → analyze rasters and vectors) with two domain modules on top: **F1 circuits** (telemetry on real terrain) and **earthquake response** (2023 Kahramanmaraş earthquakes, building damage assessment).

## Why this project exists

Eray is preparing for a full-stack mapping role at Baykar Haritalama (an aerial imaging system that maps ~4,000 km²/day). The posting asks for: React and React Native, TypeScript, Redux, Vite, Django (or similar), REST (GraphQL a plus), PostgreSQL, Docker, AWS (EC2, S3, RDS), Jest/Cypress, Git, CI/CD, microservices, **GeoTIFF and Shapefile**, MapLibre/Leaflet/Mapbox, big data and map visualization.

**The goal is learning these technologies, not shipping fast.** Understanding beats features. A small thing Eray understands and can explain in an interview is worth more than a big thing he can't.

## Who you're working with

- Eray: Computer Engineering graduate, ~2 years fullstack. Good (not senior) with **JavaScript/TypeScript and React**. Has **Spring** backend experience (so: REST, controllers, JPA entities, relational databases). Worked on a GIS project before.
- **New to:** Python (has only "vibe-coded" before), Django, AWS, microservices, RabbitMQ, Celery, Docker beyond basics, GDAL/rasterio, PostGIS.
- Already understands conceptually: GeoTIFF structure (TIFF header, IFDs, tags, strips vs tiles, overviews), COGs and HTTP range requests, PNG/JPEG compression basics, raster vs vector, DSM vs DTM.
- Dev machine: Windows PC. Use **WSL2 + Docker Desktop**, keep the repo inside the WSL filesystem. Don't install GDAL natively on Windows; run geo Python in WSL or Docker.

## How to work with Eray (most important section)

1. **One small increment at a time.** Each increment takes roughly 30–90 minutes and ends with something that runs. Follow `docs/ROADMAP.md` in order; don't jump ahead.
2. **Before coding, explain.** In a few sentences: what the concept is, why it exists, and how it maps to something he knows (Spring, Node/TS, React). Then state what we'll build and how we'll verify it works.
3. **Eray writes the code by default.** Give him the structure, the steps, names of functions or docs to look at. Write code yourself only when he asks, or for pure boilerplate/config (Dockerfiles, compose files, CI YAML), and then **explain every line**.
4. **When he's stuck: hint → partial code → full solution**, in that order, checking in between.
5. **Review what he writes:** correctness, idiomatic Python, security, naming. Always say *why*.
6. **Run everything together.** When something fails, show how to read the error and let him try to fix it before you do.
7. **Explain Python differences from JS/Java as they appear:** indentation, `None`/truthiness, list/dict comprehensions, `with` (context managers), decorators, `*args/**kwargs`, type hints, modules/packages, virtual environments vs `node_modules`, pip vs npm. Use **type hints everywhere**.
8. **Ask before adding any dependency, tool or service** that the current roadmap step doesn't introduce. Pin versions; use current stable releases.
9. **After each increment:**
   - tick the box in `docs/ROADMAP.md`
   - add a short entry to `docs/LEARNING_LOG.md` (what we built, concepts learned, open questions)
   - if a design decision was made, add it to `docs/DECISIONS.md` (decision, alternatives, why); these are interview talking points
   - suggest a git commit with a clear message
10. **Now and then, ask one interview-style check question** about what we just did ("Why did we reproject before measuring?"). Keep it light.
11. Eray decides when to stop. Don't tell him he has done "enough".
12. JavaScript/TypeScript examples: **use arrow functions**.

## Architecture (end state, built incrementally)

```
 Web (React/MapLibre) / Mobile (React Native)
                 │
           API gateway (Traefik or Nginx; routing, JWT check)
   ┌──────────┬──────────┼───────────┬────────────┐
 Core      Dataset    Telemetry    Analysis      Tiles
 Django    catalog +  FastF1       FastAPI +     TiTiler (raster)
 PostGIS   ingest     service      rasterio      Martin (vector)
           (Celery+GDAL)
   └──────────┴──── RabbitMQ events ─┴────────────┘
              S3-compatible storage (COGs, raw files)
```

- **Core (Django + DRF + PostGIS):** users/auth (issues JWTs), generic `Project` (name, boundary, working CRS), `Layer`/`Feature` (vector data), `Annotation`. Domain modules (F1 sessions; earthquake building assessments) are separate Django apps or services that point at a `Project`.
- **Dataset service:** raster catalog (`Dataset`: kind = orthophoto/DSM/DTM/hazard, source, CRS, bounds, resolution, S3 key, status) + workers that clip, reproject and convert to COG. Each data provider is a small **source adapter** (`find(bbox)`, `fetch(item)`); everything after fetching is one shared pipeline.
- **Telemetry service:** imports FastF1 sessions, georeferences them, stores laps as PostGIS lines.
- **Analysis service (FastAPI):** stateless; elevation profile, distance/area, volume, DSM height difference, zonal statistics, viewshed.
- **Tiles:** off-the-shelf TiTiler and Martin.

**Rules:**
- The core, dataset, analysis and tile services must stay **domain-agnostic**: no F1- or earthquake-specific code in them.
- **Each service owns its data.** No service reads another service's tables; it calls the API or reacts to events.
- Split into a separate service only when there's a reason (heavy dependencies, long-running work, independent scaling, external failure modes). Start as a Django monolith and extract services when the roadmap says so.

## Technical conventions

- **Monorepo:** `services/core`, `services/dataset`, `services/telemetry`, `services/analysis`, `web/`, `mobile/`, `infra/`, `docs/`.
- **Configuration via environment variables only.** `.env` is never committed; `.env.example` is.
- **Storage always through the S3 API (boto3)**, never local file paths. Locally: **SeaweedFS** (not MinIO; MinIO Community Edition stopped publishing images in Oct 2025 and is archived). Switching to AWS later must be a config change (`S3_ENDPOINT_URL` unset).
- **Rasters stored as COGs.** Vectors in PostGIS.
- **Measurements always in a metric CRS**, never Web Mercator (EPSG:3857 inflates distances ~1.6× at Türkiye's latitude). Zandvoort: EPSG:28992 (RD New). SE Türkiye: UTM 36N/37N.
- Python: ruff for lint/format, pytest for tests. Frontend: Vite + React + TS + Redux Toolkit + MapLibre GL JS, Vitest/Jest, Cypress later.
- CI: GitHub Actions from Phase 2.
- The repo will be public on Eray's GitHub as a portfolio piece; keep the README and `docs/` presentable.

## Budget: $0

- Everything runs locally in Docker first.
- AWS only in Phase 9, on the **AWS free plan** (new accounts since July 2025: credits, 6 months or until credits run out; the account closes instead of billing unless upgraded). Choose the Free plan at sign-up, never click "Upgrade plan", set a budget alert, stop instances when idle.

## Reference docs

- `docs/ROADMAP.md`: the increments, in order
- `docs/DATA_SOURCES.md`: where data comes from, licenses, caveats
- `docs/LEARNING_LOG.md`: progress notes (create if missing)
- `docs/DECISIONS.md`: design decisions (create if missing)
