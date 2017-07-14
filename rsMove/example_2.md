### Finding the hotspots! Ojective study site selection.

<p align="justify">
Within this section, we provide an example on the combine use of the functions <i>sampleMove()</i>,  <i>hotMove()</i> and <i>hotMoveStats()</i> for the identification of areas of interest that can serve as test sites. This is particularly important when dealing with migratory species. Such movement data often provides a large spatial coverage (e.g. continental, global) that can be difficult to analyze with remote sensing due to a high demand of data. Nowadays, processing big data is at the reach of our fingertips. Platforms such as <a href="https://earthengine.google.com/google/">Google Earth Engine</a> have made it simple and accessible creating a new era of global remote sensing products. However, developing new analytical approaches require an iterative process of trial and error that often demands a careful visual assessment of its results. As a consequence, carefully selecting representative test sites is essential for an efficient time management and for a careful assessment of the consistency of new methodologies. To demonstrate how <i>rsMove</i> can help with this issue, we used White Stork movement data collected by the Max Planck Institute for Ornithologie (MPIo). We focused on data collected between June and December of 2013 which refers to the first migration of 13 juveniles between Radofzell, in Germany, and the Gibraltar narrow, at the coast of Spain (Figure 2).
</p>

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure_2.jpg"></p>
 
<p align="center"><sub>Figure 2 - Movement track from 13 Juvenile white Storks between Germany and the Gribraltar narrow.</sub></p>

<br>

<p align="justify">
This data was collected with a temporal resolution of 5 minutes and an acquisition error of 3.6 meters. Due to its fine resolution, it provides valuable information on the behavior of this species allowing us to understand how it interacts with its environment during different behavior stages. Figure 3 shows the movement of the White Storks analyzed within this example while they were nesting in Germany. This GIF, derived with the <a href="https://github.com/cran/moveVis">moveVis</a> package, shows that the animals spent most of their time in small density urban areas where their nests were located. But we also see how they move towards the outskirts of the urban fabric and into agricultural land when in search for food. As a result, this data gives us an insight into the differences in resource selection by the White Stork when it's resting and when it's feeding. But it also includes a lot of random information related to the travels between the nesting and feeding sites. Additionally, we can see some exploratory flights where the animals don't seem to focus on a particular area. While this info can be relevant to understand how the animal moves, from a remote sensing perspective, these samples account for noise that hinders our ability to accurately map relevant resources.
</p>

<br>

<p align="center"><img width="414" height="260" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure_3.gif"></p>
 
<p align="center"><sub>Figure 3 - Animation of White Stork movement tracks (Radofzell, Germany) narrow.</sub></p>

<br>

<p align="justify">
As a consequence, before we can use this data to model the suitability of environmental resources, we first need to indetify samples that represent areas of interest and exclude random movements. <i>sampleMove()</i> offers a simple appproach to achieve this goal. Looking at continuous movements, the function evaluates the distance between consecutive points to identify the start of a stop. If the distance between consecutive points is lesser than the predefined error (in this case 7.2 meters, which is two times the error of the GPS sensor) the first points is set as a pointer. Then, the following observations are compared against this pointer and added to the segment if the distance between them is lower than the error. If not, the segment is closed and the function provides an average for the coordinates and timestamps as well as an estimate of the elapsed time within it. Below you can see how to read in the data and use <i>sampleMove()</i>:
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
Due to user restrictions, this data used in this example is not provided in the CRAN version of the package and will not be re-distributed. If you wish to consult the original data, please click <a href="10.5441/001/1.78152p3q">here</a>. If you wish to see the product of this analyzis click <a href="https://github.com/RRemelgado/README_data/raw/master/rsMove/rsMove_example-output.zip">here</a>.
<br>

<p align="center"><img width="600" height="300" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure_6.png"></p>
 
<p align="center"><sub>Figure 6 - Amount of time and number of samples per region. narrow.</sub></p>

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure_7.jpg"></p>
 
<p align="center"><sub>Figure 7 - Location of region 34 (in blue) and a zoom in of the same region (bottom right) showing the samples that compose this region (in red).</sub></p>

<br>

### Bug reports & contact

For bug reports, please use <a href="https://github.com/RRemelgado/rsMove/tree/master">https://github.com/RRemelgado/rsMove/issues</a>. Feature requests and other contributions are also welcome.

<br>
