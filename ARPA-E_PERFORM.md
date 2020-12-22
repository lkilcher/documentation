# NREL Dataset Creation for ARPA-E PERFORM Program

## ARPA-E PERFORM

The ARPA-E PERFORM is a dataset that consists of two year (2017-2018) of time-coincident load, wind, and solar actuals and one year (2018) of time-coincident load, wind, and solar 

The ARPA-E PERFORM contains a total of 125 existing wind sites, 22 existing solar sites, 139 proposed wind sites, and 204 proposed solar sites. Existing wind and solar plants and their meta data are collected from multiple data sources, including the U.S. Wind Turbine Database, OpenEI, and the U.S. Energy Information Administration. Proposed wind and solar sites and their meta data are collected from ERCOT Interconnection Queue. These wind and solar sites are grouped into eight load/weather zones and the whole system area based on the ERCOT topology.

Actual data are provided with a 5-min resolution. Specifically, wind and solar data are generated based on the The National Solar Radiation Database (NSRDB) at site-level, zone-level, and system-level, respectively. Load data are collected and processes based on the ERCOT load at the zone-level and system-level. 

Forecasting data are provided at three temporal scales with different operational characterstics. Day-ahead forecasts are generated with a 11-hour-ahead lead time, a 48-hour horizon, an hourly resolution, and a daily update rate. Intra-day forecasts are generated with a 6-hour-ahead lead time, a 6-hour horizon, an hourly resolution, and a 6-hour update rate. Intra-hour forecasts are generated with a 1-hour-ahead lead time, a 2-hour horizon, a 15-minute resolution, and a hourly update rate. Day-ahead and intra-day forecasting relies on the European Centre for Medium-Range Weather Forecasts (ECMWF) output that consists of deterministic forecasts from 51 members. Intra-hour forecasting relies on the historical synthetic actual data. Probabilistic forecasts are in the form of 1-99 percentiles.


The following variables are provided by the NSRDB:
- Meta data (coordinates, capacity, and other configuration data):
    - 125 actual wind sites
    - 139 proposed wind sites
    - 22 actual solar sites
    - 204 proposed solar sites
- Actual data (power [MW]):
    - Wind power (site-level, zone-level, and system-level)
    - Solar power (site-level, zone-level, and system-level)
    - Load (zone-level and system-level)
- Probabilistic forecasting data (power [MW]):
    - Wind power (site-level, zone-level, and system-level)
    - Solar power (site-level, zone-level, and system-level)
    - Load (zone-level and system-level)


## Directory structure

The ARPA-E PERFORM is made available as a series of .h5 files and can be found at: 

## Data Format

The data is provided in high density data file (.h5).  


## Python Examples

Example scripts to extract solar resource data using python are provided below:


## References