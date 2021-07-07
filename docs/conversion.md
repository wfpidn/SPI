# 7. Convert the result to GeoTIFF

!!! warning
    Below guideline using CHIRPS data as example. If you are using IMERG, just replace text CHIRPS in the script or filename below with IMERG.

I need CDO to do a conversion of the result into GeoTIFF format, and CDO required the variable should be in `time`, `lat`, `lon`, while the output from SPI: `java_xxxxx_spi_xxxxx_x_month.nc` in `lat`, `lon`, `time`, you can check this via `ncdump -h java_CHIRPS_spi_gamma_3_month.nc`

- Deactivate an active environment `climate_indices` then activate environment `gis` to start working on output conversion using CDO and GDAL.

``` bash
conda deactivate && conda activate gis
```

- Navigate your Terminal to folder `Output_nc`

- Let's re-order the variables into `time`,`lat`,`lon` using `ncpdq` command from NCO and save the result to folder `Output_TEMP`

``` bash
ncpdq -a time,lat,lon java_CHIRPS_spi_gamma_3_month.nc ../Output_TEMP/java_CHIRPS_spi_gamma_3_month_rev.nc
```

- Navigate your Terminal to folder `Output_TEMP`

- Check result and metadata to make sure everything is correct.

``` bash
ncdump -h java_CHIRPS_spi_gamma_3_month_rev.nc
```

- Then convert all SPI value into GeoTIFF with time dimension information as the filename using CDO and GDAL.

- Navigate your Terminal to folder `Output_TEMP`, execute below script and save the result to folder `Output_TIF`

``` bash
for t in `cdo showdate java_CHIRPS_spi_gamma_3_month_rev.nc`; do
    cdo seldate,$t java_CHIRPS_spi_gamma_3_month_rev.nc dummy.nc     
    gdal_translate -of GTiff -a_ullr 105.05 -5.05 116.25 -8.8 -a_srs EPSG:4326 -co COMPRESS=LZW -co PREDICTOR=1 dummy.nc ../Output_TIF/java_cli_chirps-v2.0.$t.spi3.tif
done
```

!!! note
    If you are using IMERG or other data, or different area of interest, please make sure check the bounding box of the data `<ulx> <uly> <lrx> <lry>`. This `-a_ullr` assigns georeferenced bounds to the output file, ignoring what would have been derived from the source file.

FINISH

Congrats, now you are able to calculate SPI based on monthly rainfall in netCDF or GeoTIFF format.