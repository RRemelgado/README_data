### Gazing at the clouds: which dates to download RS data for?

<br>

<p align="justify">
Independentely of the temporal resolution of the chosen satellite sensor, optical remote sensing data will always be affected by atmospheric effects such as clouds. These lead to a loss of spatial and temporal information (figure 1) and, more than often, a loss of time. Search for good quality pixels forces us to download large amounts of data in order to compensate for those pesky clouds. So, how can we make this selection of remote sensing information for efficient? For this, we developed <i>moveCloud()</i>. 
</p> 

<br>

<p align="center"><img width="600" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/MODAL2_M_CLD_FR.gif"></p>

<p align="center"><sub>Figure 1 - Variability of monthly cloud cover fraction between February 2000 and July 2017. This product was derived with MODIS data (credits: <a href="https://earthobservatory.nasa.gov/GlobalMaps/view.php?d1=MODAL2_M_CLD_FR">NASA's Earth Observations</a>)</sub></p>

<br>

<p align="justify">
<i>moveCloud()</i> uses MODIS based 10km daily fractional cloud cover information provided through <a href="https://neo.sci.gsfc.nasa.gov/view.php?datasetId=MODAL2_D_CLD_FR&date=2017-06-01">NASA's Earth Observations (NEO)</a> to evaluate the cloud cover over a movement track. For each GPS point, the function extract the cloud percent cover for the corresponding acquisition date providing information on sample specific cloud cover. Additionaly, the function alows the user to define a temporal buffer which will report on the variability of the percent cloud cover. Keep in mind that due to the scale at which this product is provided there might still be some useful pixels within cloudy images. However, atmosheric effects such as clouds affect the surface at a regional scale. This means that if the cloud cover is high (e.g. > 50%) we can expect that apparantely clear pixels might be contaminated by missed clouds (figure 2) or other related effects such as haze.
</p> 

<br>

<p align="center"><img width="600" height="300" src="https://haoliangyu.github.io/2015/01/18/Making-masks-with-Landsat-8-Quality-Assessment-band-using-Python/maskresult.png"></p>

<p align="center"><sub>Figure 2 - Example of a landsat cloud mask based on its native quality information. The image shows that while dense "puffy" clouds are captured, low altitude, shadowed and thin cloud cover is missed (credits: <a href="https://haoliangyu.github.io/2015/01/18/Making-masks-with-Landsat-8-Quality-Assessment-band-using-Python/">Hao Liang Yu</a>)</sub></p>

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
o.dates <- as.Date(moveData@data$date)
```
<br>

<p align="justify">
Now lets run <i>moveCloud()</i>. We will use a buffer of 7 days. The function allows us to adjust the buffer size so that the user can prioritize images acquired in the past or in the future. In this example, we will use a equal sized buffer for both directions.
</p> 

<br>

```R
c.cover <- moveCloud(xy=moveData, o.time=o.dates, d.path=".", b.size=c(7,7))
```

<br>

<p align="justify">
The function informs us that the tracking day was fully cloud covered. Looking at the period with the temporal buffer, it also informs us on the cloud cover for the considered dates (figure 3) and on the dates with the lowest cloud cover within the 15 day window. Based on the output, we can see that the closest days before and after the observation date with a cloud cover of 0% were, respectively, 2013-08-01 and 2013-08-05. Figure 4 shows us the graphical output provided by the function which describes the temporal variability of cloud cover within this window.
</p> 

<br>

<p align="center"><img width="600" height="280" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-3_Example-7.png"></p>

<p align="center"><sub>Figure 3 - Table output of <i>moveCloud()</i> showing the cloud cover for the sample observation date (day.cover) and the date in the past (p.date.before) and in the future (p.date.after) with the lowest cloud cover for the specified temporal window (p.cove.before and p.cover.after).</sub></p>

<br>

<p align="center"><img width="600" height="350" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-4_example-7.png"></p>

<p align="center"><sub>Figure 4 - Temporal variability of percent cloud cover within a 7 day buffer of the GPS tracking date</sub></p>

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
