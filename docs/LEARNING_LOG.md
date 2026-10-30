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
