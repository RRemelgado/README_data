### Querying remote sensing data in time

<br>

<p align="justify">
In this example, we present the functionalty of the function <i>rsQuery()</i>. This function uses and extends upon the <i>extract()</i> function provided by the raster package by allowing the user to search for data in time. This is a predominant issue when connecting movement and remote sensing data. Movement data can be collected with a very high temporal resolution (e.g. minutes, hours) while remote sensing data from open-access sensors is available with a daily resolution, at most. The function allows for an 'exact' or 'nearest' search. So, what does this mean? To demonstrate the difference between both keywords, let's consider the following example data.
</p> 

<br>

```
# read data
moveData <- shapefile() # movement data
rsStack <- stack() # read remote sensing data
```

<br>

<p align="justify">
We just read in a shapefile with movement data for the date of 2013-08-02 and a set of MODIS NDVI images acquired between 2013-07-01 and 2013-08-01 (Figure 1).
</p> 

<br>





<br>

<p align="justify">
We just read in a shapefile with movement data for the date of 2013-08-02 and a set of MODIS NDVI images acquired between 2013-07-01 and 2013-08-01 (Figure 1).
</p> 










<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
