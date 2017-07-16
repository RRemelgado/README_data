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
time.spent <- sapply(1:max(os@data$sid), f)
or$time <- time.spent # add new info to report
```

```R
# read land cover classes and add them to the output report
class = read.table('./land-cover-classes_2012.txt', sep=';', stringsAsFactors=F)
df <- merge(or, class, by.x="value", by.y="Code1") # add class info to segMove() output
```

```R
# read land cover classes and add them to the output report
class = read.table('./UA-classes_2012.txt', sep=';', stringsAsFactors=F)
df <- merge(or, class, by.x="value", by.y="value") # add class info to segMove() output
```

```R
# extract class labels and colors
uc = unique(df$Class1) # unique classes
cc = paste0('#', unique(df$Hex)) # color codes

```

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure_2.jpg"></p>


<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
