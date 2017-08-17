### Directional Analysis of Environmental Change

<br>

<p align="justify">
As an animals moves through the landscape it experiences changing environmental conditions and decides on its next steps based on this knowledge. In order to capture these environmental changes we developed <i>spaceDir()</i> and <i>timeDir()</i>. In this example we will present the aplicability of these two functions and try to demonstrate when to use one or the other. For this purpose, as done in the example <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/example_2.md">"Tracking land cover changes in space and time"</a>, we will use data from one individual recorded in 2013-08-02 over Radofzell, Germany (Figure 1). So, when should we use each function? See the examples below.
</p> 

<br>

<p align="center"><img width="800" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_example-2.png"></p>

<p align="center"><sub>Figure 1 - Land cover composition of the study site overlapped with the movement track (in black) of one White Stork during the 2nd of August of 2013.</sub></p>

<br>

#### 1. Temporal analysis

<p align="justify">
If we want to understand if the temporal evolution of the landscape influences, e.g., the decision of an animal to stop <i>timeDir()</i> is the way to go. To demonstrate this, we used Normalized Different Vegetation Index Data (NDVI) from the Moderate Resolution Spectroradiometer (MODIS) which was previously masked for clouds and interpolated with the function <i>imgInt()</i> on a daily basis between 2013-07-18 and 2013-08-17 (<b>NOTE</b>: for an example on <i>imgInt()</i> consult <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/example_5.md">Point-Based Interpolation</a>). Now let's read all the necessary data.
</p>

<br>

```R
# read data
shp <- shapefile(./) # movement data
o.dates <- strptime(shp@data$timestamp, format="%Y-%m-%d %H:%M:%S") # observation dates
r.stack <- stack(list.files('.', 'ndvi.tif')) # build data stack
r.dates <- as.Date(colnames(ndvi)) # interpolation dates
```

<br>

<p align="justify">
Before advacing with the temporal analysis, the function starts by summarizing the coordinate pairs into pixel positions using the NDVI raster as a basis. Then, looking at the sequence of points, the function segments them based on the corresponding pixel positions. If a set of sequential points are located within the same pixel (figure 2) they are aggregated into a single record. This step avoids the pseudo-replication of observations while preserving periodic movement patterns. The output returns mean values for the coordinates and timestamps and estimates the elapsed time at each segment.
</p> 

<br>


<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_Example-3.png"></p>

<p align="center"><sub>Figure 3 - Several points contained within the same pixel (in black) are aggregated into a single point (in red) which corresponds to the center of the pixel for a reference raster.</sub></p>


<br>

<p align="justify">
Then, for each data points, the function selects the closest pixels in time within a temporal buffer for a chosen direction. In this example, we will perform a backward sampling with a buffer size of 15 days and set the slope as a statistical metric. In other words, we will prompt the function to look 15 days back in time and, for each point, determine in which direction the NDVI evolved (i.e. increasing or decreasing) until the observation date.
</p>

<br>

```R
of <- function(x,y) {lm(y~x)$coefficients[2]} # function to estimate the slope (x=time and y=NDVI)
t.sample <- timeDirSample(xy=shp, ot=o.date, img=ndvi, rt=r.date, mws=15, dir="bwd", fun=of)
```

<br>

This will update the shapefile with the slope for each sample. From this we can plot the values in time (figure 3) and observe how the locations where the animal was observed are evolving. Additionaly, we can add information on how much time the animal spent at these locations.

<br>


<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-3_Example-3.png"></p>

<p align="center"><sub>Figure 3 - Several points contained within the same pixel (in black) are aggregated into a single point (in red) which corresponds to the center of the pixel for a reference raster.</sub></p>

<br>

#### 2. Spatial analysis

<p align="justify">
If you wish to understand the influence of a changing landscape over the movement of an animal <i>spaceDir()</i> is the way to go. First, as done in <i>timeDir()</i>, the removes replicated observations while preserving periodic movements. Then, using a moving window of one, this function looks either backwards or forward (or in both directions) and applies a estimates a statistical metric between the observed point and its imediate neighboor to quantify how the landscape changed between them. More than a simple moving window, this function consideres the space in between the two points. Using the input environmental raster as a reference, the function retrieves its resolution and interpotes the space between the two coordinate pairs to extract the pixels between them. So let's consider the example data we used with <i>timeDir()</i> and apply <i>spaceDir()</i> with a backward sampling and estimate the slope. As environmental data we will use the NDVI image for the observation date. </p>

<br>

```R
s.sample <- spaceDir(xy=shp, img=ndvi[[which(rd==o.date[1]))]], dir="bwd", of=of)
```

<br>

The output will consist of the endpoints and lines of the segments defined by consecutive points (figure 4).

<br>


<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-4_Example-3.png"></p>

<p align="center"><sub>Figure 4 - Several points contained within the same pixel (in black) are aggregated into a single point (in red) which corresponds to the center of the pixel for a reference raster.</sub></p>


<br>

The data for this example can be found <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/Example_3.zip">here</a>.

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
