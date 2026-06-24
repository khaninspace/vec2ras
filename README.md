# Bulk CSV to 100m Raster Pipeline

This project uses [pipeline_bulk_100m_sf_simplified.ipynb](pipeline_bulk_100m_sf_simplified.ipynb) to convert Google Open Buildings CSV.gz files into 100m WGS84 GeoTIFF rasters. The notebook splits large files, converts them to GeoPackage, processes geometries in PostGIS, computes building footprint area per grid cell, and builds a final mosaic.

## Requirements

Install GDAL and PostgreSQL/PostGIS, then make sure the Python packages below are available:

- `numpy`
- `rasterio`
- `fiona`
- `psycopg2-binary`
- `psutil`
- `pyproj`

Recommended setup:

```bash
conda install -c conda-forge gdal postgresql rasterio fiona psycopg2 numpy psutil pyproj
```

You also need a PostgreSQL database with the `postgis` extension enabled.

## Input Data

Place the source `.csv.gz` files in the folder pointed to by `CSV_DIR`.
If `CSV_DIR` is not set, the notebook uses `/data/csv` by default.

Expected geometry column:

- `geometry` in WKT format, using EPSG:4326 coordinates

## How to Run

1. Open [pipeline_bulk_100m_sf_simplified.ipynb](pipeline_bulk_100m_sf_simplified.ipynb).
2. Update environment variables if needed:
   - `CSV_DIR`
   - `SPLIT_ROOT`
   - `GPKG_DIR`
   - `RASTER_DIR`
   - `LOG_DIR`
   - `BUILDINGS_DB_NAME`
   - `BUILDINGS_DB_USER`
   - `BUILDINGS_DB_HOST`
   - `BUILDINGS_DB_PORT`
   - `BUILDINGS_DB_PASSWORD`
3. Run the notebook cells from top to bottom.
4. Wait for the conversion, rasterization, and mosaic steps to finish.

## Outputs

- Individual rasters: `{RASTER_DIR}/tifs/*_100m.tif`
- Mosaic raster: `{RASTER_DIR}/mosaic_100m_<RUN_ID>.tif`
- Logs: `{LOG_DIR}/bulk_pipeline_<RUN_ID>.log`
- Summary: `{LOG_DIR}/summary_<RUN_ID>.json`

Each raster stores building footprint area in square meters per 100m cell, uses Float32 data, and sets NoData to `-9999`.

## Notes

- Large CSV files are split automatically.
- Existing raster outputs are skipped, so the notebook can resume interrupted runs.
- The pipeline expects `ogr2ogr`, `gdal_rasterize`, `gdalbuildvrt`, and `psql` to be available on the system PATH.
