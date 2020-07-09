# Standardized Precipitation Index (SPI)
Satellite-based monitoring of dry and wet conditions using SPI

The SPI analysis is following the training conducted in 28 Jan 2020 by [NASA ARSET](https://arset.gsfc.nasa.gov) on **Application of GPM IMERG Reanalysis for Assessing Extreme Dry and Wet Periods**. Link: https://arset.gsfc.nasa.gov/water/webinars/IMERG-2020

Some of the step have been modified and adjusted based on experiencing of several problems during the training, and latest version of [Climate Indices in Python](https://github.com/monocongo/climate_indices) software is used in this tutorial. While NASA ARSET training still used the official release version from [U.S. Drought Portal](https://www.drought.gov/drought/python-climate-indices)

Information on SPI background, software requirement, data acquisition and SPI analysis using [IMERG](https://gpm.nasa.gov/category/keywords/imerg) data, please check the [notebook](https://github.com/wfpidn/SPI/blob/master/SPI_based_on_IMERG.ipynb)

Below is another way to acquired different data ([CHIRPS](https://chc.ucsb.edu/data/chirps)) using different tool ([CDO](https://code.mpimet.mpg.de/projects/cdo) - Climate Data Operator)

## CHIRPS data acquisition for SPI analysis using CDO
Download CDO and [install](https://code.mpimet.mpg.de/projects/cdo/wiki#Download-Compile-Install) from [source](https://code.mpimet.mpg.de/projects/cdo/files) or you can install via ```Homebrew``` or ```Conda```
- Download all dekad data in netcdf format from:
  - ```https://data.chc.ucsb.edu/products/CHIRPS-2.0/global_dekad/netcdf/```
- Crop with bounding box
  - ```for fl in *.nc; do cdo sellonlatbox,60,180,-23.5,50 $fl ./Regional/rbb_cli"_"$fl; done```
- Merge all netcdf in a folder into single netcdf
  - ```cdo mergetime rbb_*.nc chirps_dekad.nc```
- Check result and metadata
  - ```ncdump -h chirps_dekad.nc```
- Calculate rolling window accumulation for 3 dekads
  - ```cdo runsum,3 chirps_dekad.nc chirps_monthly_bydekad.nc```
- Check result and metadata
  - ```ncdump -h chirps_dekad.nc```
- Extract dekad 1,2 and 3 into separate files
  - ```cdo selday,1 chirps_monthly_bydekad.nc chirps_monthly_bydekad_a11.nc```
  - ```cdo selday,11 chirps_monthly_bydekad.nc chirps_monthly_bydekad_a21.nc```
  - ```cdo selday,21 chirps_monthly_bydekad.nc chirps_monthly_bydekad_a01.nc```
- Edit variable name for longitude to lon, and latitude to lat
  - Dekad1: ```cdo chname,longitude,lon chirps_monthly_bydekad_a11.nc chirps_monthly_bydekad_b11.nc```
  - Dekad1: ```cdo chname,latitude,lat chirps_monthly_bydekad_b11.nc chirps_monthly_bydekad_c11.nc```
  - Dekad2: ```cdo chname,longitude,lon chirps_monthly_bydekad_a21.nc chirps_monthly_bydekad_b21.nc```
  - Dekad2: ```cdo chname,latitude,lat chirps_monthly_bydekad_b21.nc chirps_monthly_bydekad_c21.nc```
  - Dekad3: ```cdo chname,longitude,lon chirps_monthly_bydekad_a01.nc chirps_monthly_bydekad_b01.nc```
  - Dekad3: ```cdo chname,latitude,lat chirps_monthly_bydekad_b01.nc chirps_monthly_bydekad_c01.nc```
- Check result and metadata
  - ```ncdump -h chirps_monthly_bydekad_c01.nc```
- Show attributes
  - ```cdo -s showatts chirps_monthly_bydekad_c01.nc```
- Edit precipitation unit from mm/dekad to mm
  - Dekad1: ```cdo -setattribute,precip@units="mm" chirps_monthly_bydekad_c01.nc chirps_monthly_bydekad_d01.nc```
  - Dekad2: ```cdo -setattribute,precip@units="mm" chirps_monthly_bydekad_c11.nc chirps_monthly_bydekad_d11.nc```
  - Dekad3: ```cdo -setattribute,precip@units="mm" chirps_monthly_bydekad_c21.nc chirps_monthly_bydekad_d21.nc```
- Check result and metadata to makes sure everything is set as required to run SPI
  - ```ncdump -h chirps_monthly_bydekad_d01.nc```
- Calculate SPI
  - Dekad1: ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_d01.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_01 --multiprocessing all --save_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_01_fitting.nc --overwrite```
  - Dekad2: ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_d11.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_11 --multiprocessing all --save_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_11_fitting.nc --overwrite```
  - Dekad3: ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_d21.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_21 --multiprocessing all --save_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_21_fitting.nc --overwrite```
- Open the result using (Panoply)[https://www.giss.nasa.gov/tools/panoply/]

Thanks to Yanmarshus Bachtiar for CDO's tips

## Contact
Benny Istanto | UN World Food Programme, Indonesia, benny.istanto@wfp.org
