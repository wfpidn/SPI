# Standardized Precipitation Index (SPI)
Satellite-based monitoring of dry and wet conditions using SPI

The SPI analysis is following the training conducted in 28 Jan 2020 by [NASA ARSET](https://arset.gsfc.nasa.gov) on **Application of GPM IMERG Reanalysis for Assessing Extreme Dry and Wet Periods**. Link: https://arset.gsfc.nasa.gov/water/webinars/IMERG-2020

The training session from NASA ARSET provided information on how to access and download IMERG data, and use it to calculate SPI on defined time scales. Information on SPI background, software requirement, data acquisition and SPI analysis using [IMERG](https://gpm.nasa.gov/category/keywords/imerg) data, please check the [notebook](https://github.com/wfpidn/SPI/blob/master/SPI_based_on_IMERG.ipynb)

Below is another way to acquired different data ([CHIRPS](https://chc.ucsb.edu/data/chirps)) using different tool ([CDO](https://code.mpimet.mpg.de/projects/cdo) - Climate Data Operator) and [NCO](http://nco.sourceforge.net) - netCDF Operator. 

Why CHIRPS? Because I want to get higher resolution, more frequent monitoring (updated every dekad ~ 10days), and long-term historical data from 1981 – now.

Some of the step have been modified and adjusted based on experiencing of several problems during the training, and latest version of [Climate Indices in Python](https://github.com/monocongo/climate_indices) software is used in this tutorial. While NASA ARSET training still used the official release version from [U.S. Drought Portal](https://www.drought.gov/drought/python-climate-indices)

This step-by-step guide was tested using Macbook Pro, 2.9 GHz 6-Core Intel Core i9, 32 GB 2400 MHz DDR4, running on macOS Catalina 10.15.7


## 1. Software requirement

If you encounter a problem, please look for a online solution. The installation and configuration described below is performed using a bash shell on macOS. Windows users will need to install and configure a bash shell in order to follow the usage shown below. Try to use [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10) for this purpose.

1.1. If you are new to using Bash refer to the following lessons with Software Carpentry: http://swcarpentry.github.io/shell-novice/

1.2. If you don't have [Homebrew](https://brew.sh/), you can install it by pasting below code in your macOS terminal.

  - ```bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"```

1.3. Install wget (for downloading data). Use Hombrew to install it by pasting below code in your macOS terminal.

  - ```brew install wget```

1.4. Download and install [Panoply Data Viewer](https://www.giss.nasa.gov/tools/panoply/) from [NASA GISS](https://www.giss.nasa.gov/tools/panoply/download/) on your machine: [macOS](https://www.giss.nasa.gov/tools/panoply/download/PanoplyMacOS-4.11.4.dmg), [Windows](https://www.giss.nasa.gov/tools/panoply/download/PanoplyWin-4.11.4.zip), [Linux](https://www.giss.nasa.gov/tools/panoply/download/PanoplyJ-4.11.4.zip).

1.5. Download and install Anaconda Python version 3.7 on your machine: [macOS](https://repo.anaconda.com/archive/Anaconda3-2020.02-MacOSX-x86_64.pkg), [Windows](https://repo.anaconda.com/archive/Anaconda3-2020.02-Windows-x86_64.exe), [Linux](https://repo.anaconda.com/archive/Anaconda3-2020.02-Linux-x86_64.sh).

1.6. Or you can use Miniconda: [macOS](https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.pkg), [Windows](https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe), [Linux](https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh)

1.7. For Windows users, follow [this](https://arset.gsfc.nasa.gov/sites/default/files/water/20-IMERG/IMERG_Week2_FINAL.pdf) procedure, starting from pages 16.


## 2. Configure the python environment

The code for calculating SPI is written in Python 3. It is recommended to use either the [Miniconda3](https://docs.conda.io/en/latest/miniconda.html) (minimal Anaconda) or Anaconda3 distribution. The below instructions will be Anaconda specific (although relevant to any Python [virtual environment](https://virtualenv.pypa.io/en/stable/)), and assume the use of a bash shell.

A new Anaconda [environment](https://conda.io/docs/using/envs.html) can be created using the [conda](https://conda.io/docs/) environment management system that comes packaged with Anaconda. In the following examples, I’ll use an environment named climate_indices (any environment name can be used instead of climate_indices) which will be created and populated with all required dependencies through the use of the provided setup.py file.

2.1. First, create the Python environment:

  - ```conda create -n climate_indices python=3.7```

2.2. The environment created can now be ‘activated’:

  - ```conda activate climate_indices```

2.3. Install [climate-indices](https://pypi.org/project/climate-indices/) package. Once the environment has been activated then subsequent Python commands will run in this environment where the package dependencies for this project are present. Now the package can be added to the environment along with all required modules (dependencies) via [pip](https://pip.pypa.io/en/stable/):

  - ```pip install climate-indices```

2.4. Install CDO using conda

  - ```conda install -c conda-forge cdo```

2.5. Install NCO using conda

  - ```conda install -c conda-forge nco```


## 3. CHIRPS data acquisition

As described above, I use dekad data and will calculate monthly rainfall by summing 3 dekads (rolling window accumulation for 3 dekads) in order to get more frequent monitoring.

3.1. Download using ```wget``` all CHIRPS dekad data in netcdf format from Jan 1981 to Aug 2020 (this is lot of data ~ 20GB, please make sure you have bandwidth and unlimited data package):

  - ```wget -r https://data.chc.ucsb.edu/products/CHIRPS-2.0/global_dekad/netcdf/```
  
3.2. Crop your area of interest using bounding box. Example: Indonesia bounding box with format ```lon1,lon2,lat1,lat2``` is ```94,145,-12,7```

  - ```for fl in *.nc; do cdo sellonlatbox,94,145,-12,7 $fl idn_cli"_"$fl; done```
  
3.3. Merge all netcdf in a folder into single netcdf

  - ```cdo mergetime idn_*.nc chirps_dekad.nc```

If you have limited data connection or lazy to download ~20GB, you can get pre-processed chirps_dekad.nc for Indonesia covering Jan 1981 to Dec 2019, with file size ~2.1GB. Link: https://on.istan.to/2GzorUj

3.4. Check result and metadata

  - ```ncdump -h chirps_dekad.nc```
  
3.5. Calculate rolling window accumulation for 3 dekads

  - ```cdo runsum,3 chirps_dekad.nc chirps_monthly_bydekad.nc```
  
3.6. Check result and metadata

  - ```ncdump -h chirps_monthly_bydekad.nc```
  
3.7. Extract dekad 1,2 and 3 into separate files

Dekad 1
  - ```cdo selday,1 chirps_monthly_bydekad.nc chirps_monthly_bydekad_a01.nc```

Dekad 2
  - ```cdo selday,11 chirps_monthly_bydekad.nc chirps_monthly_bydekad_a11.nc```

Dekad 3
  - ```cdo selday,21 chirps_monthly_bydekad.nc chirps_monthly_bydekad_a21.nc```
  
3.8. Edit variable name for longitude to lon, and latitude to lat

  - Dekad 1: ```cdo chname,longitude,lon chirps_monthly_bydekad_a01.nc chirps_monthly_bydekad_b01.nc```
  - Dekad 1: ```cdo chname,latitude,lat chirps_monthly_bydekad_b01.nc chirps_monthly_bydekad_c01.nc```
  - Dekad 2: ```cdo chname,longitude,lon chirps_monthly_bydekad_a11.nc chirps_monthly_bydekad_b11.nc```
  - Dekad 2: ```cdo chname,latitude,lat chirps_monthly_bydekad_b11.nc chirps_monthly_bydekad_c11.nc```
  - Dekad 3: ```cdo chname,longitude,lon chirps_monthly_bydekad_a21.nc chirps_monthly_bydekad_b21.nc```
  - Dekad 3: ```cdo chname,latitude,lat chirps_monthly_bydekad_b21.nc chirps_monthly_bydekad_c21.nc```

3.9. Check result and metadata

  - ```ncdump -h chirps_monthly_bydekad_c01.nc```
  
3.10. Show attributes

  - ```cdo -s showatts chirps_monthly_bydekad_c01.nc```
  
3.11. Edit precipitation unit from mm/dekad to mm

  - Dekad 1: ```cdo -setattribute,precip@units="mm" chirps_monthly_bydekad_c01.nc chirps_monthly_bydekad_d01.nc```
  - Dekad 2: ```cdo -setattribute,precip@units="mm" chirps_monthly_bydekad_c11.nc chirps_monthly_bydekad_d11.nc```
  - Dekad 3: ```cdo -setattribute,precip@units="mm" chirps_monthly_bydekad_c21.nc chirps_monthly_bydekad_d21.nc```

3.12. Edit precipitation timestep from dekad to month

  - Dekad 1: ```cdo -setattribute,precip@time_step="month" chirps_monthly_bydekad_d01.nc chirps_monthly_bydekad_e01.nc```
  - Dekad 2: ```cdo -setattribute,precip@time_step="month" chirps_monthly_bydekad_d11.nc chirps_monthly_bydekad_e11.nc```
  - Dekad 3: ```cdo -setattribute,precip@time_step="month" chirps_monthly_bydekad_d21.nc chirps_monthly_bydekad_e21.nc```
  
3.13. Check result and metadata to make sure everything is set as required to run SPI

  - ```ncdump -h chirps_monthly_bydekad_e01.nc```


## 4. Calculate SPI
In order to pre-compute fititng parameters for later use as inputs to subsequent SPI calculations we can save both gamma and Pearson distributinon fitting parameters to NetCDF, and later use this file as input for SPI calculations over the same calibration period.

**Dekad 1**: 
  - ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_e01.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_01 --multiprocessing all --save_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_01_fitting.nc --overwrite```

**Dekad 2**: 
  - ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_e11.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_11 --multiprocessing all --save_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_11_fitting.nc --overwrite```

**Dekad 3**: 
  - ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_e21.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_21 --multiprocessing all --save_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_21_fitting.nc --overwrite```

The above command will compute SPI (standardized precipitation index, both gamma and Pearson Type III distributions) from an input precipitation dataset (in this case, CHIRPS precipitation dataset). The input dataset is 3-dekads rainfall accumulation data and the calibration period used will be Jan-1981 through Dec-2019. 

The index will be computed at 1,2,3,6,9,12,24,36,48,60 and 72-month timescales. The output files will be <out_dir>/CHIRPS_spi_gamma_xx.nc, and <out_dir>/CHIRPS_spi_pearson_xx.nc. Parallelization will occur utilizing all CPUs.


> ## **5. Notes**: Updating procedure when new data is coming
> What if the new data is coming (September 2020)? Should we re-run again for the whole periods, 1981 to date? That's not practical as it required lot of storage and time processing if you do for bigger coverage (country or regional analysis).
> 
> To update SPI up to SPI72, we should have data at least 6 years back (2014) from the latest (Sep 2020). To avoid computation for the whole periods (1981-2020), we could extract the data ```chirps_monthly_bydekad.nc``` result from step **3.5.** only for year 2014 to 2020, using command: 
> 
>   - ```cdo selyear,2014/2020 chirps_monthly_bydekad.nc chirps_monthly_bydekad_a.nc``` 
> 
> After that, you can continue the process following above mentioned steps.
> 
> In the above example (**Step 4**) we demonstrate how distribution fitting parameters can be saved as NetCDF. This fittings NetCDF can then be used as pre-computed variables in subsequent SPI computations. Initial command computes both distribution fitting values and SPI for various month scales. 
> 
> The distribution fitting variables are written to the file specified by the ```--save_params``` option. 
> 
> The second command also computes SPI but instead of computing the distribution fitting values it loads the pre-computed fitting values from the NetCDF file specified by the ```--load_params``` option. See below code:
> 
> **Dekad 1**: 
>   - ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_e01.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_01 --multiprocessing all --load_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_01_fitting.nc```
>   
> **Dekad 2**: 
>   - ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_e11.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_11 --multiprocessing all --load_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_11_fitting.nc```
>   
> **Dekad 3**: 
>   - ```spi --periodicity monthly --scales 1 2 3 6 9 12 24 36 48 60 72 --calibration_start_year 1981 --calibration_end_year 2019 --netcdf_precip /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/chirps_monthly_bydekad_e21.nc --var_name_precip precip --output_file_base /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Output/CHIRPS_21 --multiprocessing all --load_params /Users/bennyistanto/Temp/CHIRPS/SPI/Regional/Input/CHIRPS_21_fitting.nc```
> 


## 6. Visualized the result using [Panoply](https://www.giss.nasa.gov/tools/panoply/)

Let see the result.

6.1. From the output directory in Finder, right-click file CHIRPS_11_spi_gamma_3_month.nc (you can download this file from this link: https://on.istan.to/3ntmTfi) and Open With Panoply.

6.2. From the Datasets tab select spi_gamma_1_month and click Create Plot

6.3. In the Create Plot window select option Georeferenced Longitude-Latitude.

6.4. When the Plot window opens:

  - Array tab: Change the time into 467 to view the latest/last data ~ Dec 2019
  - Scale tab: Change value on Min -3, Max 3, Major 6, Color Table CB_RdBu_09.cpt
  - Map tab: Change value on Center on Lon 118.0, then Zoom in the map through menu-editor Plot > Zoom Plot In few times until Indonesia appear proportionally. Set grid spacing 5.0 and Labels on every grid lines.
  - Overlays tab: Change Overlay 1 to MWDB_Coasts_Countries_1.cnob
  

## 7. Convert the result to GeoTIFF
To convert the result into GeoTIFF format, you need additional software: [GDAL](https://gdal.org). 
The software can be installed via ```Homebrew``` or ```conda```.

7.1. Install GDAL using conda

  - ```conda install -c conda-forge gdal```

or using Homebrew

  - ```brew install gdal```

7.2. CDO required the variable should be in ```"time, lat, lon"```, while the output from SPI: ```CHIRPS_XX_spi_xxxxx_x_month.nc``` in ```"lat, lon, time"```, you can check this via ```ncdump -h file.nc```

- Let's re-order the variables into ```time,lat,lon``` using ```ncpdq``` command from NCO

  - ```ncpdq -a time,lat,lon CHIRPS_01_spi_gamma_1_month.nc CHIRPS_01_spi_gamma_1_month_rev.nc```
  
- Check result and metadata to make sure everything is correct.

  - ```ncdump -h CHIRPS_01_spi_gamma_1_month_rev.nc```

- Then convert all SPI value into GeoTIFF with ```time``` dimension information as the ```filename``` using CDO and GDAL

  ``` 
  for t in `cdo showdate CHIRPS_01_spi_gamma_1_month_rev.nc`; do
    cdo seldate,$t CHIRPS_01_spi_gamma_1_month_rev.nc dummy.nc
    gdal_translate -of GTiff -a_ullr <top_left_lon> <top_left_lat> <bottom_right_lon> <bottom_right_lat> -a_srs EPSG:4326 -co COMPRESS=LZW -co PREDICTOR=1 dummy.nc $t.tif
  done
  ```


## 8. Example output
March 2020, SPI3

8.1. IMERG and CHIRPS
- IMERG

  ![IMERG](/Exercise/img/IMERG_SPI3_Mar2020.png)

- CHIRPS

  ![CHIRPS](/Exercise/img/CHIRPS_SPI3_Mar2020.png)

And comparison with SPI generated by:

- BMKG [Link](https://www.bmkg.go.id/iklim/indeks-presipitasi-terstandarisasi.bmkg?p=the-standardized-precipitation-index-maret-2020&tag=spi&lang=ID)

  ![BMKG](/Exercise/img/BMKG_SPI3_Mar2020.png)

- Climate Engine [Link](https://climengine.page.link/nTyi)

  ![ClimEngine](/Exercise/img/ClimateEngine_SPI3_Mar2020.png)


## 8. References
- Guttman, N. B., 1999: Accepting the Standardized Precipitation Index: A calculation algorithm. J. Amer. Water Resour. Assoc., 35(2), 311-322. [Link](https://climatedataguide.ucar.edu/climate-data/standardized-precipitation-index-spi)
- Keyantash, John & National Center for Atmospheric Research Staff (Eds). "The Climate Data Guide: Standardized Precipitation Index (SPI)." Retrieved from https://climatedataguide.ucar.edu/climate-data/standardized-precipitation-index-spi
- Lloyd Hughes, B., and M. A. Saunders, 2002: A drought climatology for Europe. Int. J. Climatol., DOI:10.1002/joc.846 [Link](https://rmets.onlinelibrary.wiley.com/doi/epdf/10.1002/joc.846)
- McKee, T.B., N. J. Doesken, and J. Kliest, 1993: The relationship of drought frequency and duration to time scales. In Proceedings of the 8th Conference of Applied Climatology, 17 22 January, Anaheim, CA. American Meterological Society, Boston, MA. 179-18. [Link](https://www.droughtmanagement.info/literature/AMS_Relationship_Drought_Frequency_Duration_Time_Scales_1993.pdf)
- National Drought Mitigation Center (NDMC) at the University of Nebraska Lincoln. [Link](https://drought.unl.edu/droughtmonitoring/SPI.aspx)
- World Meteorological Organization (WMO), 2012: Standardized Precipitation Index User Guide. [Link](https://library.wmo.int/doc_num.php?explnum_id=7768)
- Climate Indices in Python https://climate-indices.readthedocs.io/en/latest/
- Climate Engine - https://app.climateengine.org/climateEngine
- BMKG - https://www.bmkg.go.id/iklim/indeks-presipitasi-terstandarisasi.bmkg
- NASA ARSET on Application of GPM IMERG Reanalysis for Assessing Extreme Dry and Wet Periods. Link: https://arset.gsfc.nasa.gov/water/webinars/IMERG-2020
- Climate Data Operator - https://code.mpimet.mpg.de/projects/cdo
- CHIRPS, Climate Hazard Centre UCSB - https://chc.ucsb.edu/data/chirps
- My LinkedIn post - https://www.linkedin.com/pulse/satellite-based-monitoring-dry-wet-conditions-using-spi-benny-istanto/?published=t


## Contact
Benny Istanto | UN World Food Programme, Indonesia, benny.istanto@wfp.org


## Thanks
Thanks to Yanmarshus Bachtiar for CDO's tips
