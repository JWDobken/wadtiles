# Processor job

This job will fetch the required data and generates maptiles onto a persistent volume.

1. Create Kubernetes secret with your [OSS-Deltaress](https://oss.deltares.nl) credentials:
   ```cmd
   $ k create secret generic oss-deltares-creds --from-literal=username=XXXX --from-literal=password=XXXX
   secret/oss-deltares-creds created
   ```
2. Create the persistent volume (`k apply -f persistent-volume.yaml`)
3. Run the specific Job: `k apply -f processor-job.yaml`

## Considerations

### [GDAL](https://gdal.org/index.html)

GDAL is used to create the geotiff files.

- [`gdal_translate`](https://gdal.org/programs/gdal_translate.html) transforms the ASC files to GeoTIFF.
- [`gdalwarp`](https://gdal.org/programs/gdalwarp.html) changes the projection from the dutch EPSG:28992 to the webstandard EPSG:3857
- [`gdalbuildvrt`](https://gdal.org/programs/gdalbuildvrt.html) creates a mosaic of many tiff files.
- [`gdaldem`](https://gdal.org/programs/gdaldem.html) is used to apply vizualization including color-relief, hillshade and slope. I learned about lazy raster processing from [this post by Matthew Perry](https://www.perrygeo.com/lazy-raster-processing-with-gdal-vrts.html). This [post by ](https://blog.mastermaps.com/2012/06/creating-hillshades-with-gdaldem.html) explains the usage of hillshade.
- [`gdalcalc`](https://gdal.org/programs/gdal_calc.html) combines the color-relief and hillshade.

It works but the user experience is not great. The [Docker container](https://hub.docker.com/r/geodata/gdal/) is not maintained since 2017 so I consider to launch my own with just the drivers I need.

### Mapnik

Used to generate PNG tiles from the GeoTIFF files. It works but installation is horrible.

- maybe drop Mapnik and `gdal2tiles` should just work?

### Possibilities...

GEOS.
Shapely.
Fiona.
Python Shapefile Library (pyshp)
pyproj.
Rasterio.
GeoPandas.

## File sizes

| YEAR | total ASC | raw TIF |
| :--: | --------: | ------: |
| 2015 |      4.0K |    122M |
