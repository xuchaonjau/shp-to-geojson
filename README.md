# SHP / KML / CSV → GeoJSON Converter

A single-file, browser-based vector data converter that turns **Shapefile (.shp)**, **KML**, and **CSV/TXT point data** into **GeoJSON** (WGS84, EPSG:4326). Everything runs locally in the browser — there is no backend, no upload to a server, and no API key required.

## Features

- **Multi-format input**
  - Shapefile: `.shp` together with its `.dbf` and `.prj` sidecar files
  - KML: Point / LineString / Polygon (including inner rings)
  - CSV / TXT: point data with longitude/latitude columns, or a single combined coordinate column
- **Coordinate conversion to WGS84**
  - Reads the ESRI `.prj` WKT and converts projected coordinates (Transverse Mercator / Gauss-Krüger, Albers) to WGS84 via [proj4js](https://proj4js.org/)
  - CSV projected coordinates support Gauss-Krüger projection with automatic zone detection
- **Geometry compression & cleanup**
  - Douglas-Peucker simplification ([turf.simplify](https://turfjs.org/)) with light / medium / heavy tolerance
  - Configurable coordinate precision (4 / 5 / 6 decimals)
  - Optional property slimming (keep only color-related fields) and compact JSON output
- **Automatic styling**: each layer gets a distinct fill/stroke color written into `fill_color`, `stroke_color`, `fill_opacity`, and `layer_name` properties
- **Normalized geometry types**: Polygon → MultiPolygon, LineString → MultiLineString, Point → MultiPoint, to avoid QGIS splitting mixed geometries into folder containers
- **Batch conversion**: drop multiple layers at once and download one `.geojson` per layer

## Quick start

No build step and no install required.

1. Open `index.html` directly in a modern browser, or serve the folder locally:

   ```bash
   npx serve .
   ```

2. Drag your `.shp` (with `.dbf` / `.prj`), `.kml`, or `.csv` / `.txt` files onto the drop zone.
3. Adjust the conversion options, then click **Convert**. Each layer downloads as `<layer-name>.geojson`.

> **Note:** Shapefile conversion requires the `.prj` file alongside `.shp`, otherwise the coordinate system cannot be reconstructed. KML and CSV/TXT do not need it.

## Conversion options

| Option | Description |
| --- | --- |
| Convert to WGS84 (EPSG:4326) | Reproject projected source coordinates using the `.prj` definition |
| Auto-assign layer colors | Write distinct `fill_color` / `stroke_color` per layer |
| CSV/TXT coordinate mode | WGS84 lon/lat, or Gauss-Krüger projected coordinates (zone auto-detected) |
| Simplify geometry | Douglas-Peucker simplification with selectable tolerance |
| Coordinate precision | Number of decimal places kept in output coordinates |
| Slim properties | Keep only color-related properties, drop raw DBF attributes |
| Compact JSON | Emit minified GeoJSON without indentation |

## Tech stack

- Plain HTML / CSS / JavaScript — one self-contained `index.html`
- [shapefile](https://github.com/mbostock/shapefile) — parse `.shp` / `.dbf`
- [proj4js](https://proj4js.org/) — coordinate reprojection
- [Turf.js](https://turfjs.org/) — geometry simplification

Libraries are loaded from the jsDelivr CDN; you can also vendor them locally for offline / intranet use.

## Input formats expected

- **SHP**: matching `.shp`, `.dbf`, `.prj` files sharing the same base name
- **KML**: standard OGC KML with `Placemark` Point / LineString / Polygon
- **CSV / TXT**: header row with coordinate columns such as `lon/lng/longitude/x/经度`, `lat/latitude/y/纬度`, or a single `坐标/coord` column; delimiter auto-detected (tab / comma / semicolon)

## Known limitations

- Projected CRS support covers common Transverse Mercator / Gauss-Krüger and Albers definitions parsed from ESRI WKT; other projections are reported as unsupported.
- Gauss-Krüger zone detection reads the leading digits of the easting; values without a zone prefix default to zone 36.
- The converter works on client-side data; very large datasets are bounded by browser memory.

## License

[MIT](./LICENSE)
