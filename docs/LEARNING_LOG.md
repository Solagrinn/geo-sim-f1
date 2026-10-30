# Learning log

One short entry per increment: what was built, concepts learned, open questions.

## Before coding: concepts covered

- TIFF structure: header (byte order, magic 42), IFDs as a table of contents, 12-byte tag entries, strips vs tiles, compression and predictor tags, BigTIFF.
- GeoTIFF: extra tags (ModelTiepoint, ModelPixelScale, ModelTransformation, GeoKeyDirectory), EPSG codes, pixel ↔ coordinate formula.
- COG: tiled + internal overviews + IFDs first → read only what you need with HTTP range requests; still a single file.
- Pixel data types (int/uint/float/complex, up to 64-bit per sample), bands, scale/offset, NoData.
- PNG (filters = prediction + DEFLATE) vs JPEG (DCT + quantization + Huffman); why measurement data uses lossless formats.
- DSM vs DTM; distance/area/volume/profile from a DSM; why measurements need a metric CRS.

## Entries

<!-- ### YYYY-MM-DD: 0.1 Setup
Built:
Learned:
Questions:
-->

### 2026-10-30: 0.1 Setup
Built: Ubuntu 24.04 in WSL2, Docker Desktop with WSL integration, git configured in Linux (reusing Windows Git Credential Manager), repo cloned to `~/code/geo-sim-f1`, `.gitattributes` forcing LF, Python 3.12 venv in `.venv/`.
Learned:
- WSL2 is a real Linux kernel in a lightweight VM; code lives in the Linux filesystem because `/mnt/c` is slow and breaks permissions, line endings and file watching.
- The `docker` CLI is a client talking HTTP to the daemon over `/var/run/docker.sock`; "permission denied" was Linux file permissions, fixed by joining the `docker` group (`usermod -aG`, takes effect on next login).
- `wsl --shutdown` also stops Docker Desktop's engine.
- Shell globs: `*` expands to filenames; heredocs with `'EOF'` turn expansion off.
- venv ≈ `node_modules`, but explicit: `activate` changes `PATH` in the current shell. Ubuntu blocks global `pip install` (externally-managed-environment).
- `core.autocrlf` is per machine; `.gitattributes` is per repo and applies to everyone.
Questions:
