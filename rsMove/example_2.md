### Tracking land cover changes in space and time

<b>

<p align="center">
In this section we present an example on the applicability of <i>moveSeg()</i>. This function offers a simple segmentation approach based on the comparison between environmental conditions associated to consecutive GPS points providing a quick assessment of how an animal uses the landscape. In this example, we used movement data from one White Storks. It was recorded in 2013-08-02 with a temporal resolution of 5 minutes over Radofzell, Germany (DOI:). According to the <a href="http://land.copernicus.eu/local/urban-atlas/urban-atlas-2012/view">Urban Atlas (UA)</a> this site is extensively managed with 44% of land dedicated to agriculture (figure 1).
</p> 

<br>

ADD MAP OF MOVEMENT DATA OVERLAID ON TOP OF CORINE

<br>

<p align="center">

</p> 

```R
# read data
lc <- raster("./DE054L1_KONSTANZ_UA2012.tif") # land cover
shp <- shapefile("./WhiteStork_20130802.shp") # movement
```

```R
# apply function and split output
move.seg <- moveSeg(xy=shp, img=lc, type="cat")
os <- move.seg$endpoints # shapefile with segment ID's
or <- move.seg$report # report with corresponding class
```

```R
# estimate time spent within each segment
f <- function(x) {
  ind <- which(os@data$sid==x)
  et <- difftime(shp@data$timestamp[ind[length(ind)]], 
                 shp@data$timestamp[ind[1]], units="mins")}
time.spent <- sapply(1:max(os@data$sid)), f)
```

```R
# read land cover classes and add them to the output report

```



<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>