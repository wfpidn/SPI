# 0. Working Directory

For this tutorial, I am working at `/Users/bennyistanto/Temp/xxx/SPI/Java` (applied to Mac/Linux machine) or `Z:/Temp/xxx/SPI/Java` (applied to Windows machine) directory. I have some folder inside this directory:

1. `Downloads`
	1. `IMERG`
		1. IMERG_mmhr
		2. IMERG_mmmonth
		3. IMERG_originalfiles
		
	2. `CHIRPS`
	
	Place to put downloaded IMERG or CHIRPS data

2. `Fitting`

	Place to put fitting parameters output from the calculation

3. `Input_nc`

	Place to put netCDF data that will use as an input 

4. `Input_TIF`

	Place to put GeoTIFF file that will use to convert to single netCDF with time dimension enabled

5. `Output_nc`

	Output folder for SPI calculation

6. `Output_TEMP`

	Temporary for nc files from CDO arrange dimension process

7. `Output_TIF`

	Final output of SPI, generate by CDO and GDAL

8. `Script`

	Python script location to convert bunch of GeoTIFF file to single netCDF

9. `Subset`

	Place to put shapefile for clipping area of interest

Feel free to use your own preferences for this setting.


**Notes:**

xxx = IMERG or CHIRPS (depend on the data used in tutorial)