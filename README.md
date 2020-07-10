# Standardized Precipitation Index (SPI)
Satellite-based monitoring of dry and wet conditions using SPI

The SPI analysis is following the training conducted in 28 Jan 2020 by [NASA ARSET](https://arset.gsfc.nasa.gov) on **Application of GPM IMERG Reanalysis for Assessing Extreme Dry and Wet Periods**. Link: https://arset.gsfc.nasa.gov/water/webinars/IMERG-2020

Some of the step have been modified and adjusted based on experiencing of several problems during the training, and latest version of [Climate Indices in Python](https://github.com/monocongo/climate_indices) software is used in this tutorial. While NASA ARSET training still used the official release version from [U.S. Drought Portal](https://www.drought.gov/drought/python-climate-indices)

Information on SPI background, software requirement, data acquisition and SPI analysis using [IMERG](https://gpm.nasa.gov/category/keywords/imerg) data, please check the [notebook](https://github.com/wfpidn/SPI/blob/master/SPI_based_on_IMERG.ipynb)

Below is another way to acquired different data ([CHIRPS](https://chc.ucsb.edu/data/chirps)) using different tool ([CDO](https://code.mpimet.mpg.de/projects/cdo) - Climate Data Operator)

## CHIRPS data acquisition for SPI analysis using CDO
### Download CDO and [install](https://code.mpimet.mpg.de/projects/cdo/wiki#Download-Compile-Install) from [source](https://code.mpimet.mpg.de/projects/cdo/files) or you can install via ```Homebrew``` or ```Conda```
- Download all dekad data in netcdf format from:

  - ```https://data.chc.ucsb.edu/products/CHIRPS-2.0/global_dekad/netcdf/```
  
- Crop with bounding box

  - ```for fl in *.nc; do cdo sellonlatbox,114.3,115.8,-8,-9 $fl bali_cli"_"$fl; done```
  
- Merge all netcdf in a folder into single netcdf

  - ```cdo mergetime bali_*.nc chirps_dekad.nc```
  
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
  
- Check result and metadata to make sure everything is set as required to run SPI

  - ```ncdump -h chirps_monthly_bydekad_d01.nc```

### Calculate SPI
In order to pre-compute fititng parameters for later use as inputs to subsequent SPI calculations we can save both gamma and Pearson distributinon fitting parameters to NetCDF, and later use this file as input for SPI calculations over the same calibration period.

  - Dekad1: ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_d01.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_01 --multiprocessing all --save_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_01_fitting.nc --overwrite```
  - Dekad2: ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_d11.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_11 --multiprocessing all --save_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_11_fitting.nc --overwrite```
  - Dekad3: ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_d21.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_21 --multiprocessing all --save_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_21_fitting.nc --overwrite```

The above command will compute SPI (standardized precipitation index, both gamma and Pearson Type III distributions) from an input precipitation dataset (in this case, CHIRPS precipitation dataset). The input dataset is 3-dekads rainfall accumulation data and the calibration period used will be Jan-1981 through Dec-2019. The index will be computed at 1,2,3,6,9,12,24,36,48,60 and 72-month timescales. The output files will be <out_dir>/CHIRPS_spi_gamma_xx.nc, and <out_dir>/CHIRPS_spi_pearson_xx.nc. Parallelization will occur utilizing all CPUs.

### **Notes**: Updating procedure when new data is coming
In the above example we demonstrate how distribution fitting parameters can be saved as NetCDF. This fittings NetCDF can then be used as pre-computed variables in subsequent SPI computations. Initial command computes both distribution fitting values and SPI for various month scales. The distribution fitting variables are written to the file specified by the ```–save_params``` option. The second command also computes SPI but instead of computing the distribution fitting values it loads the pre-computed fitting values from the NetCDF file specified by the ```–load_params``` option. See below code:

  - **Dekad1**: ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_d01.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_01 --multiprocessing all --load_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_01_fitting.nc
  - **Dekad2**: ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_d11.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_11 --multiprocessing all --load_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_11_fitting.nc
  - **Dekad3**: ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_d21.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_21 --multiprocessing all --load_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_21_fitting.nc


### Open the result using [Panoply](https://www.giss.nasa.gov/tools/panoply/)

## Example output
March 2020, SPI3
- IMERG
  ![IMERG](/Exercise/img/IMERG_SPI3_Mar2020.png)

- CHIRPS
  ![CHIRPS](/Exercise/img/CHIRPS_SPI3_Mar2020.png)

And comparison with SPI generated by:
- BMKG [Link](https://www.bmkg.go.id/iklim/indeks-presipitasi-terstandarisasi.bmkg?p=the-standardized-precipitation-index-maret-2020&tag=spi&lang=ID)
  ![BMKG](/Exercise/img/BMKG_SPI3_Mar2020.png)

- Climate Engine [Link](https://climengine.page.link/nTyi)
  ![ClimEngine](/Exercise/img/ClimateEngine_SPI3_Mar2020.png)

## Contact
Benny Istanto | UN World Food Programme, Indonesia, benny.istanto@wfp.org

## Thanks
Thanks to Yanmarshus Bachtiar for CDO's tips
