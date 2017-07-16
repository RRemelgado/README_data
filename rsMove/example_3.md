### Understanding Movement with Directional Sampling

<br>

<p align="justify">
As an animals moves through the landscape it experiences changing environmental conditions and decides on its next steps based on this knowledge. In order to capture these environmental changes we developed <i>spaceDirSample</i> and <i>timeDirSample</i>. In this example we will present the aplicability of these two functions and try to demonstrate when to use one or the other. For this purpose, as done in the example <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/example_2.md">"Tracking land cover changes in space and time"</a>, we will use data from one individual recorded in 2013-08-02 over Radofzell, Germany (Figure 1).
</p> 

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_example-2.png"></p>

<p align="center"><sub>Figure 1 - Land cover composition of the study site overlapped with the movement track (in black) of one White Stork during the 2nd of August of 2013.</sub></p>

<br>

So, when should we use each function? Well, if we want to understand if the temporal evolution of the landscape influences, e.g., the decision of an animal to stop <i>timeDirSample()</i> is the way to go. To demonstrate this, we used Normalized Different Vegetation Index Data (NDVI) from the Moderate Resolution Spectroradiometer (MODIS) which was previously masked for clouds and interpolated with the function <i>imgInt()</i> on a 16-day interval.

```R
# read data
shp <- shapefile() # movement data
img.ls <- list.files('./', 'ndvi.tif')
ndvi <- stack(img.ls) # rs data
```

As we will need to know when the images were acquired, we will extract this information form the image name.

```R
# function to extract date (in Date format)
function(x) {
  tmp <- strplit(x, '[.]')[[1]][2]
  y <- substr()
  m <- substr()
  d <- substr()
  return(as.Date(paste0(y, '-', m, '-', d))
}

r.dates <- sapply(img.ls, sf)
```

Then, for each data points, the function selects the closest pixels in time within a temporal buffer for a chosen direction. In this example, we will perform a backward sampling with a buffer size of 30 days and set the slope as a statistical metric. In other words, we will prompt the function to look 30 days back in time (considering the timestamp of each point) and determine in which direction is the NDVI evolving (increasing or decreasing).

```R
of <- function(x,y) {lm(y~x)$coefficients[2]} # function to estimate the slope
t.sample <- timeDirSample(xy=shp, ot=strptime(shp@data$timestamp), img=ndvi, rt=r.date, mws=30, dir="bwd", fun=of)
```

This will update the provided point shapefile with the field <i>stat</i> (figure 3) providing the slope for each sample.






From this, we can plot the values in time (figure 4) and observe how the locations visitied by the animal are evolving. Additionaly, we can add information on how much time the animal spent at these locations.







<p align="justify">
This happens becasuse, before advacing with the temporal analysis, the function starts by summarizing the provided coordinate pairs into pixel coordinates using the NDVI raster as a basis. Looking at the sequence of points, the function segments them based on the corresponding pixel positions. If a set of sequential points are located within the same pixel (figure 2) they are aggregated into a single record. This step avoids the pseudo-replication of observation while preserving periodic movement patterns. The output returns mean values for the coordinates and timestamps and estimates the elapsed time at each segment.
</p> 

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-3_example-3.png"></p>

<p align="center"><sub>Figure 3 - Several points contained within the same pixel (in black) are aggregated into a single point (in red) which corresponds to the center of the pixel for a reference raster.</sub></p>

<br>





the influence of a changing landscape over the movement of an animal <i>spaceDirSample</i> is the way to go. Using a moving window of one, this function looks either backwards or forward (or in both directions) and applies a given function between the observed point and its imediate neighboor to evaluate how the landscape changes between them. More than a simple moving window, this function consideres the space in between the two points when applying the function. It queries the input environmental layer to determine its resolution and them interpotes the space between the two coordinate pairs to extract the pixels between them. So lets


<br>

The data for this example can be found <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/Example_3.zip">here</a>.

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
