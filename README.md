### Code Description
"grassland-habitat_model.ipynb" is a Jupyter Notebook that contains code to download habitat data for Sorghastrum nutans (indiangrass) and process the data into a habitat suitability model. The model uses the following habitat data:
- Soils - silt percentage
- Aspect
- Median precipitation

This model can be adaptated to other species and geographic areas by identifying new habitat characteristics and National Grassland Units to analyze.

### Instructions
1. Identify a species for analysis and identify habitat characteristics for analysis. This code is designed to analyze one soils trait, one topographic trait, and two climate change scenarios.
2. Identify National Grassland Units for analysis. Specify these NGUs in the following code block by replacing the values in the .loc list.
```python
# Specify grassland unit names and create a GDF    
gl_unit_gdf = (
    gpd.read_file(gl_unit_path).set_index('GRASSLANDN')
    .loc[['Thunder Basin National Grassland', 'Pawnee National Grassland']]
    .to_crs(4326)
)
gl_unit_gdf
```
3. Identify the POLARIS soils tiles to be downloaded. This can be done by assessing the extent of the NGU polygons selected for analysis. View the list of available files from the POLARIS website [here](http://hydrology.cee.duke.edu/POLARIS/PROPERTIES/v1.0/). Select the folder for the desired trait and identify the correct tiles to download based on the latitude and longitude previously identified. Add these file names to the soils_list[] in the code.
```python
soil_list = ['lat4344_lon-105-104.tif',
             'lat4344_lon-106-105.tif',
             'lat4445_lon-105-104.tif',
             'lat4445_lon-106-105.tif',
             'lat4041_lon-104-103.tif',
             'lat4041_lon-105-104.tif']
```
4. Modify the MACAv2 download URL based on the parameters selected for analysis. An updated download URL can be found and copy/pasted from the [Northwest Knowledge](https://climate.northwestknowledge.net/MACA/data_catalogs.php) web page. Update the download URL in the code here:
```python
maca_url = (
    'http://thredds.northwestknowledge.net:8080/thredds/ncss'
    '/agg_macav2metdata_pr_CCSM4_r6i1p1_historical_1950_2005_CONUS_monthly.nc'
    '?var=precipitation'
    '&disableLLSubset=on'
    '&disableProjSubset=on'
    '&horizStride=1'
    '&time_start=1990-01-15T00%3A00%3A00Z'
    '&time_end=1990-12-15T00%3A00%3A00Z'
    '&timeStride=1'
    '&accept=netcdf'
)
```
5. Modify the acculutor dictionaries and hv.Layout() objects in the final portion of the code. Adjust names or add/remove accumulators for additional NGUs. Update appropriate plot labels in code.
```python
layout_thunderbasin = hv.Layout()
layout_pawnee = hv.Layout()

tb_accumulator = {}
pawnee_accumulator = {}

harmonizer = 'harmonizer'
if harmonizer in data_accumulator:
    del data_accumulator['harmonizer']

for name, details in gl_unit_gdf.iterrows():
    if name == 'Thunder Basin National Grassland':
        print("Generating plots for " + name)
        for key in data_accumulator:
            da = data_accumulator[key]
            da.rio.write_crs("epsg:4326", inplace=True)
            clipped_da = da.rio.clip_box(*gl_unit_gdf.loc[[name]].total_bounds)
            tb_accumulator[key] = clipped_da
    if name == 'Pawnee National Grassland':
        print("Generating plots for " + name)
        for key in data_accumulator:
            da = data_accumulator[key]
            da.rio.write_crs("epsg:4326", inplace=True)
            clipped_da = da.rio.clip_box(*gl_unit_gdf.loc[[name]].total_bounds)
            pawnee_accumulator[key] = clipped_da
```
6. Run all code cells.

