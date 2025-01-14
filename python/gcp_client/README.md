# HydroTools :: GCP Client

This subpackage implements an interface to retrieve National Water Model (NWM) data from [Google Cloud Platform](https://console.cloud.google.com/marketplace/details/noaa-public/national-water-model). The primary use for this tool is to populate `pandas.Dataframe` objects with NWM streamflow data. See the [GCP Client Documentation](https://noaa-owp.github.io/hydrotools/hydrotools.gcp_client.html) for a complete list and description of the currently available methods. To report bugs or request new features, submit an issue through the [HydroTools Issue Tracker](https://github.com/NOAA-OWP/hydrotools/issues) on GitHub.

## Installation

In accordance with the python community, we support and advise the usage of virtual
environments in any workflow using python. In the following installation guide, we
use python's built-in `venv` module to create a virtual environment in which the
tool will be installed. Note this is just personal preference, any python virtual
environment manager should work just fine (`conda`, `pipenv`, etc. ).

```bash
# Create and activate python environment, requires python >= 3.8
$ python3 -m venv env
$ source env/bin/activate
$ python3 -m pip install --upgrade pip wheel

# Install gcp_client
$ python3 -m pip install hydrotools.gcp_client
```

## Usage

The following example demonstrates how one might use `hydrotools.gcp_client` to retrieve NWM streamflow forecasts.

### Code
```python
# Import the GCP Client
from hydrotools.gcp_client import gcp

# Instantiate model data service
model_data_service = gcp.NWMDataService()

# Retrieve forecast data
#  By default, only retrieves data at USGS gaging sites in
#  CONUS that are used for model assimilation
forecast_data = model_data_service.get(
    configuration = "short_range",
    reference_time = "20210101T01Z"
    )

# Look at the data
print(forecast_data.info(memory_usage='deep'))
print(forecast_data[['value_time', 'value']].head())
```
### Output
```console
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 137628 entries, 0 to 137627
Data columns (total 8 columns):
 #   Column            Non-Null Count   Dtype         
---  ------            --------------   -----         
 0   reference_time    137628 non-null  datetime64[ns]
 1   value_time        137628 non-null  datetime64[ns]
 2   nwm_feature_id    137628 non-null  int64         
 3   value             137628 non-null  float32       
 4   usgs_site_code    137628 non-null  category      
 5   configuration     137628 non-null  category      
 6   measurement_unit  137628 non-null  category      
 7   variable_name     137628 non-null  category      
dtypes: category(4), datetime64[ns](2), float32(1), int64(1)
memory usage: 5.1 MB
None
           value_time  value
0 2021-01-01 02:00:00   5.29
1 2021-01-01 03:00:00   5.25
2 2021-01-01 04:00:00   5.20
3 2021-01-01 05:00:00   5.12
4 2021-01-01 06:00:00   5.03
```
### System Requirements
We employ several methods to make sure the resulting `pandas.DataFrame` produced by `gcp_client` are as efficient and manageable as possible. Nonetheless, this package can potentially use a large amount of memory.

The National Water Model generates multiple forecasts per day at over 3.7 million locations across the United States. A single forecast could be spread across hundreds of files and require repeated calls to Google Cloud Platform. The intermediate steps of retrieving and processing these files into leaner `DataFrame` may use several GB of memory. As such, recommended minimum requirements to use this package are a 4-core consumer processor and 8 GB of RAM.

## Development

This package uses a setup configuration file (`setup.cfg`) and assumes use of the `setuptools` backend to build the package. To install the package for development use:
```bash
$ python3 -m venv env
$ source env/bin/activate
$ python3 -m pip install -U pip
$ python3 -m pip install -U setuptools
$ python3 -m pip install -e .[develop]
```

To generate a source distribution:
```bash
$ python3 -m pip install -U wheel build
$ python3 -m build
```

The packages generated in `dist/` can be installed directly with `pip` or uploaded to PyPI using `twine`.