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
- CHIRPS monthly in netCDF
- [CHIRPS monthly in GeoTIFF](../chirpstif/)

If you are prefer to use your own dataset also fine, you can still follow this guideline and adjust some steps and code related to filename, unit, format and structure.