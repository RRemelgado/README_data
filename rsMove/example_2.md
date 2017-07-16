### Tracking land cover changes in space and time

<b>

<p align="justify">
In this section we present an example on the applicability of <i>moveSeg()</i>. This function offers a simple segmentation approach based on the comparison between environmental conditions associated to consecutive GPS points providing a quick assessment of how an animal uses the landscape. In this example, we used movement data from one White Storks. It was recorded in 2013-08-02 with a temporal resolution of 5 minutes over Radofzell, Germany (DOI:). According to the <a href="http://land.copernicus.eu/local/urban-atlas/urban-atlas-2012/view">Urban Atlas (UA)</a> this site is extensively managed with 44% of land dedicated to agriculture (figure 1).
</p> 

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_example-2.png"></p>

<p align="center">Figure 1 - Land cover composition of the study site overlaped with the movement track (in black) of one White Stork during the 2nd of August of 2013.</p>

<br>

<p align="justify">
First, we read our movement data (as a <i>SpatialPoints</i> or <i>SpatialPointsDataFrame</i> object) and a <i>RasterLayer</i> containing the land cover information. As we are dealing with a categorical variable, multi-layered objects (i.e. <i>RasterStack</i> or <i>RasterBrick</i>) will no be accepted.
</p> 

```R
# read data
shp <- shapefile("./WhiteStork_20130802.shp") # movement
lc <- raster("./DE054L1_KONSTANZ_UA2012.tif") # land cover
```

<p align="justify">
Then, we apply <i>moveSeg()</i> setting the <i>type</i> keyword to "cat". This will ignore any additional variables. The function will return us a shapefile containing containing the segment ID's associated to each point (figure 2) as well as a report with the raster value associated to each segment and the corresponding amount of points.
</p>

```R
# apply function and split output
move.seg <- moveSeg(xy=shp, img=lc, type="cat")
os <- move.seg$endpoints # shapefile with segment ID's
or <- move.seg$report # report with corresponding class
```
<br>

<p align="center"><img width="567" height="567" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_example-2.png"></p>

<p>Figure 2 - Segmented movement data based on changes in land cover. Different colors represent different segments.</p>

<br>

<p align="justify">
Then for each segment, we can estimate the time the animal spent within it. Since we know which points are assigned to each segment, we can use the timestamps provided by the original shapefile and update the report from the <i>moveSeg()</i>.
</p>

```R
# estimate time spent within each segment
f <- function(x) {
  ind <- which(os@data$sid==x)
  et <- difftime(shp@data$timestamp[ind[length(ind)]], 
                 shp@data$timestamp[ind[1]], units="mins")}
time.spent <- sapply(1:max(os@data$sid), f)
or$time <- time.spent # add new info to report
```

<p align="justify">
Now we know how much time the animal spent at each segment and we have its correspondent raster value. But which classes are these values referring to? To know that, we built a table containing the class values (as they appear in the raster) and their correspondent labels. Then, we merged this new table with the updated report.
</p>

```R
# read land cover classes and add them to the output report
class = read.table('./lc_classes.txt', sep=';', stringsAsFactors=F)
or <- merge(or, class, by.x="value", by.y="Cvalue") # add class info to segMove() output
```

<p align="justify">
Finally, we can plot this information. For this example we decided to use the <i>ggplot2</i> package to build a bar plot of the segments against the elapsed time and to color each bar based on the corresponding land cover class (figure 3). The output shows us that the animal spent most of its time within discontinuous urban fabric. The largest segment was found within this class with a total of 180 minutes. Forom these results, we can also have a rough idea of when the animal was feeding given the predisposition of the White Stork to search for prey on Agricultural land. Our results show that the animal visited pastures in tree separate ocasions. The first one (segment 16) lasted from 07:46 and 08:00 (~14 minutes) and was followed by a second passage (segment 23) between 09:26 and 10:04 (~34 minutes). The last one was registed at 10:50 (segment 25) and conrresponds to a single point. As a result, the elapsed time was returned as zero.
</p>

```R
# plot time spent per segment and theur correspondent land cover class
ggplot(df, aes(x=as.factor(sid), y=time, fill=class)) + geom_bar(stat='identity') + xlab('segment ID') + 
  ylab('Time Spent (minutes') + theme(axis.text.x=element_text(size=8, hjust=1), 
  axis.text.y=element_text(size=10), axis.title.x=element_text(size=14), 
  axis.title.y=element_text(size=14), legend.justification = c(1, 1), 
  legend.position = c(1, 1), legend.text=element_text(size=14))
```

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-3_example-2.png"></p>

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
