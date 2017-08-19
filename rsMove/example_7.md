### Gazing at the clouds: which dates to download RS data for?

<br>

<p align="justify">
Independentely of the temporal resolution of the chosen satellite sensor, optical remote sensing data will always be affected by atmospheric effects such as clouds. These lead to a loss of spatial and temporal information (figure 1) and, more than often, a loss of time. Search for good quality pixels forces us to download large amounts of data in order to compensate for those pesky clouds. So, how can we make this selection of remote sensing information for efficient? For this, we developed <i>moveCloud()</i>. 
</p> 

<br>

<p align="center"><img width="600" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/MODAL2_M_CLD_FR.gif"></p>

<p align="center"><sub>Figure 1 - Variability of monthly cloud cover fraction between February 2000 and July 2017. This product was derived with MODIS data (credits: NASA's <a href="https://earthobservatory.nasa.gov/GlobalMaps/view.php?d1=MODAL2_M_CLD_FR">EarthObservation</a></sub></p>

<br>

<p align="justify">
<i>moveCloud()</i> uses MODIS based 10km daily fractional cloud cover information provided through <a href="https://neo.sci.gsfc.nasa.gov/view.php?datasetId=MODAL2_D_CLD_FR&date=2017-06-01">NASA's Earth Observations (NEO)</a> to evaluate the cloud cover over a movement track. For each GPS point, the function extract the cloud percent cover for the corresponding acquisition date providing information on sample specific cloud cover. Additionaly, the function alows the user to define a temporal buffer which will report on the variability of the percent cloud cover. Keep in mind that due to the scale at which this product is provided there might still be some useful pixels within cloudy images. However, atmosheric effects such as clouds affect the surface at a regional scale. This means that if the cloud cover is high (e.g. > 50%) we can expect that apparantely clear pixels might be contaminated by missed clouds (figure 2) or other related effects such as haze.
</p> 

<br>

<p align="center"><img width="600" height="300" src="https://haoliangyu.github.io/2015/01/18/Making-masks-with-Landsat-8-Quality-Assessment-band-using-Python/maskresult.png"></p>

<p align="center"><sub>Figure 2 - Example of a landsat cloud mask based on its native quality information. The image shows that while dense "puffy" clouds are captured, low altitude, shadowed and thin cloud cover is missed (credits: <a href="https://haoliangyu.github.io/2015/01/18/Making-masks-with-Landsat-8-Quality-Assessment-band-using-Python/">Hao Liang Yu</a></sub></p>)

<br>

<p align="justify">
To demonstrate the applicability of <i>moveCloud()</i> lets use the example data provided by the package. We will use the example data for 2013-08-04. In addition, we will read one of the example images. This will be used to define the spatial projection of the movement data.
</p> 

<br>

```R
# read raster data
r <- raster(system.file('extdata', 'tcb_1.tif', package="rsMove"))

# read movement data
moveData <- read.csv(system.file('extdata', 'konstanz_20130804.csv', package="rsMove"))
moveData <- SpatialPointsDataFrame(moveData[,1:2], moveData, proj4string=crs(r))

# define observation dates
d <- as.Date(moveData@data$date)
```

<br>

<p align="justify">
Now lets run <i>moveCloud()</i>
</p> 

<br>

<p align="center"><img width="600" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_Example-6.png"></p>

<p align="center"><sub>Figure 1 - Example movement data</b>.</sub></p>

<br>

<p align="justify">
Now, let's consider that we require open access sensors from which the Normalized Difference Vegetation Index (NDVI) can be estimated. For the purpose of this example, we'll look at Spot (5 m), Sentinel 2 (10 m), Landsat (30 m), MODIS (250 m) and Sentinel 3 (300 m). Now, let's call <i>sMoveRes()</i> specifying the resolution of these sensors.
</p> 

<br>

```R
# apply function
a.res <- sMoveRes(xy=shp, pxr=c(5, 10, 30, 250, 300))
```
<br>


<p align="justify">
The function returns a data frame containing information on the amount of unique pixels and the amount unique groups of connected pixels. Additionaly, it plots this information using <i>ggplot</i> (figure 2). Looking at the output, we can see hw the number of unique samples gradually decreases with the decrease in resolution. by 300, we have only 51 unique samples but they are dispersed across 32 unique regions. As a result, Sentinel 3 data might be adequate for analyzing the temporal variability of the pixels crossed by the animal while allowing their comparison.
</p> 

<br>

<p align="center"><img width="600" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_Example-6.png"></p>

<p align="center"><sub>Figure 2 - Number of samples available per spatial resolution and the correspondent number of unique regions (provided by default by <i>sMoveRes()</i>).</sub></p>

<br>

The data for this example can be found <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/Example_4.zip">here</a>.

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
