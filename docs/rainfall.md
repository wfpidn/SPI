# 3. Preparing input for SPI

SPI requires monthly rainfall data, and there are many source providing global high-resolution gridded monthly rainfall data:

- [CHIRPS](https://chc.ucsb.edu/data/chirps)
- [IMERG](https://disc.gsfc.nasa.gov/datasets/GPM_3IMERGM_06/summary?keywords=IMERG)
- [FLDAS](https://disc.gsfc.nasa.gov/datasets/FLDAS_NOAH01_C_GL_M_001/summary?keywords=FLDAS)
- [TerraClimate](https://data.nkn.uidaho.edu/dataset/monthly-climate-and-climatic-water-balance-global-terrestrial-surfaces-1958-2015)
- [CRU](https://catalogue.ceda.ac.uk/uuid/89e1e34ec3554dc98594a5732622bce9)

For better result, SPI required minimum 30-years of data.

I provide 3 different approach to prepare rainfall data ready to use as SPI input. For learning matters, you may follow all the approach. Or you can choose which one is suit for you depend on the data source and format:

- [IMERG monthly in netCDF](../imergnc/)
- [CHIRPS monthly in GeoTIFF](../chirpstif/)
- [CHIRPS monthly in netCDF](../chirpsnc/)

Why I chosed CHIRPS as an alternative example from IMERG? It is produced at 0.05 x 0.05 degree spatial resolution, make CHIRPS the highest gridded rainfall data, and long-term historical data from 1981 – now.

If you are prefer to use your own dataset also fine, you can still follow this guideline and adjust some steps and code related to filename, unit, format and structure.


## Input specification

The [climate-indices](https://pypi.org/project/climate-indices/) python package enables the user to calculate SPI using any gridded netCDF dataset. However, there are certain specifications for input files that vary based on input type.

- Precipitation unit must be written as `millimeters`, `milimeter`, `mm`, `inches`, `inch` or `in`.

- Data dimension and order must be written as `lat`, `lon`, `time` (Windows machine required this order) or `time`, `lat`, `lon` (Works tested on Mac/Linux and Linux running on WSL).