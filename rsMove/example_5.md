### Point-based interpolation

<br>

<p align="justify">
In this example, we present the use of <i>imgInt()</i>. This function performs a simple linear interpolation of missing data in a time series of raster images using existing temporal information. First, lets read the example data.
</p> 

<br>

```R
# read data
rs.stack <- stack(list.files('.', '.tif$') # read NDVI data
```

this contains a time series of NDVI images 

<p align="center"><img width="800" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_Example-5.png"></p>


<p align="center"><sub>Figure 1 - R printout showing the order of dates in <i>r.dates</i>.</sub></p>



<b>Note</b>: Provided a point shapefile, the function will only process the pixels the corresponding pixels.


<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
