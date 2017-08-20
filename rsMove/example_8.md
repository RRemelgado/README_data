### Accessing remote sensing data

<p align="justify">
<i>rsMove</i> provides access to a series of remote sensing products. It allows the user to download, crop, mask and re-project this data easing the process of introducing it within ecological studies. We developed two functions to achieve this: <i>getEnv()</i> and <i<proSat()</i>. The functions perform the same pre-processing steps. However, they provide access to different kinds of that. So, when to use one or the other?
</p>

<br>

### 1. GetEnv()

<p align="justify">
This function provides access to "static" remote sensing products. In other words, it focus on products which are derived on a yearly basis (or with no predicted update). This includes da from:
</p>

* Yale University EarthEnv (<a href="http://www.earthenv.org/">EarthEnv</a>)
* ESA's Climate Change Initiative (<a href="http://cci.esa.int/">CCI</a>)
* JRC's Global Human Settlements (<a href="http://ghsl.jrc.ec.europa.eu/">GHS</a>)
* Maryland University Global Forest Change (<a href="https://earthenginepartners.appspot.com/science-2013-global-forest">GFC</a>)
* JRC's Global Surface Water dynamics (<a href="https://global-surface-water.appspot.com/">GSW</a>)

<br>

<p align="justify">
As each of the sources provides a wide range of datasets, we decided provide its list through the function rather than trough the standard R documentation. To access this information, you only need to specify the data source when calling the function.
</p>

```R
getEnv(d.source="EarthEnv") # EarthEnv
getEnv(d.source="EarthEnv") # CCI
getEnv(d.source="EarthEnv") # GHS
getEnv(d.source="EarthEnv") # GFC
getEnv(d.source="EarthEnv") # GSW
```


<p align="center"><img width="414" height="260" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure_3.gif"></p>
 
<p align="center"><sub>Figure 3 - Animation of White Stork movement tracks (Radofzell, Germany) narrow.</sub></p>

<br>

<p align="justify">
As a consequence, before we can use this data to model the suitability of environmental resources, we first need to identify samples that represent areas of interest and exclude random movements. <i>sampleMove()</i> offers a simple appproach to achieve this goal. Looking at continuous movements, the function evaluates the distance between consecutive points to identify the start of a stop. If the distance between consecutive points is lesser than the predefined error (in this case 7.2 meters, which is two times the error of the GPS sensor) the first points is set as a pointer. Then, the following observations are compared against this pointer and added to the segment if the distance between them is lower than the error. If not, the segment is closed and the function provides an average for the coordinates and timestamps as well as an estimate of the elapsed time within it. Below you can see how to read in the data and use <i>sampleMove()</i>:
</p>

```R

# read movement data
shp <- shapefile("./WhiteStork_Germany.shp")

# Extract time information and convert to date format
obs.time <- as.Date(shp@data$date)

# Derive unique identifiers
ui <- unique(shp@data$ind)

# sample for each unique identifier
os <- vector('list', length(ui))
for (i in 1:length(ui)) {
    ind = which(shp@data$ind==ui[i])
    os[[i]] <- sampleMove(xy=shp[ind,], ot=obs.time[ind], error=7.2, method="deg")
}

```

<p align="justify">
The final sample set, which consists of the samples collected for each individual separately, corresponds to 13% of the initial sample set (Figure 4). Each sample represents locations where the animal spend at least 5 minutes. <i>sampleMove()</i> also reports on the amount of data points used to define each of the new samples.
</p>

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure_4.jpg"></p>
 
<p align="center"><sub>Figure 4 - Comparison between original samples (in black) and the samples selected by <i>sampleMove()</i> (in red).</sub></p>

<br>

<p align="justify">
After obtaining a filtered sample set, we can use them to identify hotspots using <i>hotMove()</i>. This  function uses a grid based approach to identify sample agglomerations. It conversts all the samples to unique pixel positions and searches connected groups of pixels using a 8-neighboor connected component labelling algorithm. The code example below shows how define unique regions for the output of <i>sampleMove()</i> using a grid with a resolution of 0.2 degrees:
</p>

```R

# derive shapefile from samples of each unique ID
x <- unlist(sapply(os, function(x){x@coords[,1]}))
y <- unlist(sapply(os, function(x){x@coords[,2]}))
t <- unlist(sapply(os, function(x){x@data$timestamp}))
s <- SpatialPointsDataFrame(cbind(x,y), data.frame(x=x, y=y, time=t), proj4string=crs(shp))

# identify sample regions
hm <- hotMove(xy=s, pxr=0.2, shp=T)

```

<p align="justify">
As <i>shp</i> was set to TRUE, the functions returns polygons for the sample regions (Figure 5). These provide us with a good idea of where to start looking for answers highlighting some larger connected areas where the storks spend much of their time. However, we still have splenty of areas to look at with a total of 34 regions. If we want to test the use of different remote sensing datasets with the movement data, it can be troublesome to deal with such a large amount of test sites. For example, if we are dealing with Landsat data, the selected regions would account for 26 tiles. Considering that we would only be interested on the year of 2013 (when the animals were tracked), downloading all the available data sum up to ~600 Gb. While such amount of data is surely managable, processing and visually checking the results of our analyzis would surely become time consuming. So, how can we narrow down our focus?
</p>

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure_5.jpg"></p>
 
<p align="center"><sub>Figure 5 - Polygons of different colors represent unique sample regions identified with <i>hotMove()</i> (in red).</sub></p>

<br>

<p align="justify">
The function <i>hotMoveStats()</i> supports this choice by performing a statistical evaluation of the movement data that composes each of the sample regions acquired with <i>hotMove()</i>. The base output of this function is a count of samples per region. However, if timestamps are provided, the function reports evaluates the time spent within each region. So let's apply the function using:
</p>

```R
hm.stats <- hotMoveStats(rid=hm, time=as.Date(moveData@data$timestamp))
```

Then, using the output data frame, we can look at how much time was spent within each region and the amount of samples assigned to each of them (Figure 6). Based on this analysis, we see that region 34 (Figure 7) stands out with the biggest amount of time (76 days) and the biggest number of samples (15000). Now we can focus on this region as a test site for our remote sensing based methodologies. This region covers only two two Landsat tiles amounting to a storage requirement of ~46 Gb to cover 2013.
Due to user restrictions, this data used in this example is not provided in the CRAN version of the package and will not be re-distributed. If you wish to consult the original data, please click <a href="https://www.datarepository.movebank.org/handle/10255/move.417">here</a>. If you wish to see the product of this analyzis click <a href="https://github.com/RRemelgado/README_data/raw/master/rsMove/rsMove_example-output.zip">here</a>.
<br>

<p align="center"><img width="600" height="300" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure_6.png"></p>
 
<p align="center"><sub>Figure 6 - Amount of time and number of samples per region. narrow.</sub></p>

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure_7.jpg"></p>
 
<p align="center"><sub>Figure 7 - Location of region 34 (in blue) and a zoom in of the same region (bottom right) showing the samples that compose this region (in red).</sub></p>

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
