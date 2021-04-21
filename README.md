# Standardized Precipitation Index (SPI)

Satellite-based monitoring of dry and wet conditions using SPI

The SPI analysis is following the training conducted in 28 Jan 2020 by [NASA ARSET](https://arset.gsfc.nasa.gov) on **Application of GPM IMERG Reanalysis for Assessing Extreme Dry and Wet Periods**. Link: https://appliedsciences.nasa.gov/join-mission/training/english/arset-applications-gpm-imerg-reanalysis-assessing-extreme-dry-and-wet

The training session from NASA ARSET provided information on how to access and download IMERG data, and use it to calculate SPI on defined time scales. Information on SPI background, software requirement, data acquisition and SPI analysis using [IMERG](https://gpm.nasa.gov/category/keywords/imerg) data, please check the [notebook](/SPI_based_on_IMERG.ipynb)

Some of the step have been modified and adjusted based on experiencing of several problems during the training, and latest version of [Climate Indices in Python](https://github.com/monocongo/climate_indices) software is used in this tutorial. While NASA ARSET training still used the official release version from [U.S. Drought Portal](https://www.drought.gov/drought/python-climate-indices)

You can try different (data source and format) approach below: 

- SPI based on IMERG netCDF ([notebook](/SPI_based_on_IMERG.ipynb))
- SPI based on CHIRPS netCDF ([how-to guideline](/SPI_based_on_CHIRPS_netCDF.md))
- SPI based on CHIRPS GeoTIFF ([how-to guideline](/SPI_based_on_CHIRPS_GeoTIFF.md))


### References
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
- https://benny.istan.to/blog/20200706-calculate-spi-using-imerg-data
- https://benny.istan.to/blog/20200710-calculate-spi-using-chirps-data
- https://benny.istan.to/blog/20201210-geotiff-to-netcdf-file-with-time-dimension-enabled-and-cf-compliant
- https://benny.istan.to/blog/20210125-calculate-spi-using-monthly-rainfall-data-in-geotiff-format


### Contact
Benny Istanto | UN World Food Programme, Indonesia


### Thanks
Thanks to Yanmarshus Bachtiar for CDO's tips
