# Wind Resource Data: Wind Integration National Dataset (WIND) Toolkit

## Model

Wind resource data for North America was produced using the [Weather Research and Forecasting Model (WRF)](https://www.mmm.ucar.edu/weather-research-and-forecasting-model).
The WRF model was initialized with the European Centre for Medium Range Weather
Forecasts Interim Reanalysis (ERA-Interm) data set with an initial grid spacing
of 54 km.  Three internal nested domains were used to refine the spatial
resolution to 18, 6, and finally 2 km.  The WRF model was run for years 2007
to 2014. While outputs were extracted from WRF at 5 minute time-steps, due to
storage limitations instantaneous hourly time-step are provided for all
variables while full 5 min resolution data is provided for wind speed and wind
direction only.

The following variables were extracted from the WRF model data:
- Wind Speed at 10, 40, 60, 80, 100, 120, 140, 160, 200 m
- Wind Direction at 10, 40, 60, 80, 100, 120, 140, 160, 200 m
- Temperature at 2, 10, 40, 60, 80, 100, 120, 140, 160, 200 m
- Pressure at 0, 100, 200 m
- Surface Precipitation Rate
- Surface Relative Humidity
- Inverse Monin Obukhov Length

## Domains

The wind resource was produce using three distinct WRF domains shown below. The
CONUS domain for 2007-2013 was run by 3Tier while 2014 as well as all years of
the Canada and Mexico domains were run under NARIS. The data is provided in
three sets of files:

- CONUS: Extracted exclusively from the CONUS domain
- Canada: Combined data from the Canada and CONUS domains
- Mexico: Combined data from the Mexico and CONUS domains

## References

For more information about the WIND Toolkit please see the [website.](https://www.nrel.gov/grid/wind-toolkit.html)
Users of the WIND Toolkit should use the following citations:
- [Draxl, C., B.M. Hodge, A. Clifton, and J. McCaa. 2015. Overview and Meteorological Validation of the Wind Integration National Dataset Toolkit (Technical Report, NREL/TP-5000-61740). Golden, CO: National Renewable Energy Laboratory.](https://www.nrel.gov/docs/fy15osti/61740.pdf)
- [Draxl, C., B.M. Hodge, A. Clifton, and J. McCaa. 2015. "The Wind Integration National Dataset (WIND) Toolkit." Applied Energy 151: 355366.](https://www.sciencedirect.com/science/article/pii/S0306261915004237?via%3Dihub)
- [Lieberman-Cribbin, W., C. Draxl, and A. Clifton. 2014. Guide to Using the WIND Toolkit Validation Code (Technical Report, NREL/TP-5000-62595). Golden, CO: National Renewable Energy Laboratory.](https://www.nrel.gov/docs/fy15osti/62595.pdf)
- [King, J., A. Clifton, and B.M. Hodge. 2014. Validation of Power Output for the WIND Toolkit (Technical Report, NREL/TP-5D00-61714). Golden, CO: National Renewable Energy Laboratory.](https://www.nrel.gov/docs/fy14osti/61714.pdf)

## Directory structure

Wind resource data is made available as a series of hourly .h5 files
corresponding to each domain and year. Below is an example of the directory
structure for the CONUS domains:
- s3://nrel-pds-wtk/hdf5-source-files-hourly/conus -> root directory for the conus domain
    - /v1.0.0 -> version 1 of the data corresponding to years 2007-2013, run by 3Tier
        - /wtk_conus_${year}.h5 -> Hourly data for all variables for the given year
        - /${year}/wind_${hub_height}.h5 -> Five minute wind resource data for the given year and hub height
    - /v1.1.0 -> version 1.1 of the data corresponding to 2014, run under NARIS with an updated version of WRF and new Boundary Layer Physics (PBL scheme)

The WIND Toolkit data is also available via HSDS at /nrel/wtk/${domain} where
domain is conus, canada, or mexico

For examples on setting up and using HSDS please see our [examples repository](https://github.com/nrel/hsds-examples)

## Data Format

The data is provided in high density data file (.h5) separated by year. The
variables mentioned above are provided in 2 dimensional time-series arrays with
dimensions (time x location). The temporal axis is defined by the `time_index`
dataset, while the positional axis is defined by the `meta` dataset. For
storage efficiency each variable has been scaled and stored as an integer. The
scale-factor is provided in the `scale-factor` attribute.  The units for the
variable data is also provided as an attribute (`units`).

## Python Examples

Example scripts to extract wind resource data using python are provided below:

The easiest way to access and extract data from the Resource eXtraction tool
[`rex`](https://github.com/nrel/rex)


```python
from rex import WindX

wtk_file = '/nrel/wtk/conus/wtk_conus_2010.h5'
with WindX(wtk_file, hsds=True) as f:
    meta = f.meta
    time_index = f.time_index
    wspd_100m = f['windspeed_100m']
```

Note: `WindX` will automatically interpolate to the desired hub-height:

```python
from rex import WindX

wtk_file = '/nrel/wtk/conus/wtk_conus_2010.h5'
with WindX(wtk_file, hsds=True) as f:
    print(f.datasets)  # not 90m is not a valid dataset
    wspd_90m = f['windspeed_90m']
```

`rex` also allows easy extraction of the nearest site to a desired (lat, lon)
location:

```python
from rex import WindX

wtk_file = '/nrel/wtk/conus/wtk_conus_2010.h5'
nwtc = (39.913561, -105.222422)
with WindX(wtk_file, hsds=True) as f:
    nwtc_wspd = f.get_lat_lon_df('windspeed_100m', nwtc)
```

or to extract all sites in a given region:

```python
from rex import WindX

wtk_file = '/nrel/wtk/conus/wtk_conus_2010.h5'
state='Colorado'
with WindX(wtk_file, hsds=True) as f:
    co_wspd = f.get_region_df('windspeed_100m', state, region_col='state')
```

Lastly, `rex` can be used to extract all variables needed to run SAM at a given
location:

```python
from rex import WindX

wtk_file = '/nrel/wtk/conus/wtk_conus_2010.h5'
nwtc = (39.913561, -105.222422)
with WindX(wtk_file, hsds=True) as f:
    nwtc_sam_vars = f.get_SAM_df(nwtc)
```

If you would rather access the WIND Toolkit data directly using h5pyd:

```python
# Extract the average 100m wind speed
import h5pyd
import pandas as pd

# Open .h5 file
with h5pyd.File('/nrel/wtk/conus/wtk_conus_2010.h5', mode='r') as f:
    # Extract meta data and convert from records array to DataFrame
    meta = pd.DataFrame(f['meta'][...])
    # 100m windspeed dataset
    wspd = f['windspeed_100m']
    # Extract scale factor
    scale_factor = wspd.attrs['scale_factor']
    # Extract, average, and unscale windspeed
    mean_wspd_100m = wspd[...].mean(axis=0) / scale_factor

# Add mean windspeed to meta data
meta['Average 100m Wind Speed'] = mean_wspd_100m
```

```python
# Extract time-series data for a single site
import h5pyd
import pandas as pd

# Open .h5 file
with h5pyd.File('/nrel/wtk/conus/wtk_conus_2010.h5', mode='r') as f:
    # Extract time_index and convert to datetime
    # NOTE: time_index is saved as byte-strings and must be decoded
    time_index = pd.to_datetime(f['time_index'][...].astype(str))
    # Initialize DataFrame to store time-series data
    time_series = pd.DataFrame(index=time_index)
    # Extract 100m wind speed, wind direction, temperature, and pressure
    for var in ['windspeed_100m', 'winddirection_100m',
    			'temperature_100m', 'pressure_100m']:
    	# Get dataset
    	ds = f[var]
    	# Extract scale factor
    	scale_factor = ds.attrs['scale_factor']
    	# Extract site 100 and add to DataFrame
    	time_series[var] = ds[:, 100] / scale_factor
```