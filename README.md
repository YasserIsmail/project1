### Let's see what we have data
```txt
X;Y;SVCAREA;DOW
-98.4328445939999;29.49504318600009;North;Tue
-98.57816862999994;29.53342609600008;Prue;Tue
-98.56455515499994;29.54512571400005;Prue;Tue
```


| column      | Description |
| ----------- | ----------- |
|X| Longitude of the point|
|Y| Latitude of the point|
|SVCAREA|The burglary area|
|DOW|Day of burglary "Day of week"|



> Spatial data georeferenced using `WGS84`




### Reading a Data form *.txt file*
```python
import pandas as pd
data = pd.read_csv("Data.txt",sep=";")
```
###  Write function to create geometry column based on " X "and " Y "columns
```python
from shapely.geometry import Point
def to_shp(row,x_src,y_src,output):
    row[output] = Point(row[x_src],row[y_src])
    return row
data["geometry"]=None
data = data.apply(to_shp,x_src="X",y_src="Y",output="geometry",axis=1)
```
```python
data.geometry.head()
Out[6]: 
0    POINT (-98.4328445939999 29.49504318600009)
1    POINT (-98.57816862999994 29.53342609600008)
2    POINT (-98.56455515499994 29.54512571400005)
3    POINT (-98.52772582499996 29.54220655800009)
4    POINT (-98.37219915599992 29.46290521600008)
Name: geometry, dtype: object
```
### Create GeoDataFrame
```python
import geopandas as gpd
from fiona.crs import from_epsg
Gdf = gpd.GeoDataFrame({"geometry":data.geometry,
                            "SVCAREA":data.SVCAREA,
                            "DOW":data.DOW},
                            crs = from_epsg(4326))
```
```python
Gdf.head()
Out[7]: 
                     geometry SVCAREA  DOW
0  POINT (-98.43284 29.49504)   North  Tue
1  POINT (-98.57817 29.53343)    Prue  Tue
2  POINT (-98.56456 29.54513)    Prue  Tue
3  POINT (-98.52773 29.54221)    Prue  Tue
4  POINT (-98.37220 29.46291)    East  Tue
```
###  Save GeoDataFrame as a Shapefile Format
```python
Gdf.to_file(r"Outputs\data.shp")
```
