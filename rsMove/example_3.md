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
If we want to understand if the temporal evolution of the landscape influences, e.g., the decision of an animal to stop <i>timeDir()</i> is the way to go. To demonstrate this, we used Normalized Different Vegetation Index Data (NDVI) from the Moderate Resolution Spectroradiometer (MODIS) which was previously masked for clouds and interpolated with the function <i>imgInt()</i> on a daily basis between 2013-07-28 and 2013-08-11 (<b>NOTE</b>: for an example on <i>imgInt()</i> consult <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/example_5.md">Point-Based Interpolation</a>). The data was downloaded with the <i>proSat()</i> function. Now let's read all the necessary data.
</p>

<br>

```R
# read data
shp <- shapefile(./) # movement data
o.dates <- strptime(shp@data$timestamp, format="%Y-%m-%d %H:%M:%S") # observation dates
t.dates <- seq.Date(as.Date("2013-07-28"), as.Date("2013-08-11"), 1) # interpolation dates
```

<br>

<p align="justify">
For each data point, the function selects the closest pixels in time within a temporal buffer for a chosen direction. In this example, we will perform a backward sampling with a buffer size of 15 days and set the slope as a statistical metric. In other words, we will prompt the function to look 15 days back in time and, for each point, determine in which direction the NDVI evolved (i.e. increasing or decreasing) until the observation date.
</p>

<br>

```R
of <- function(x,y) {lm(y~x)$coefficients[2]} # function to estimate the slope (x=time and y=NDVI)
time.env <- timeDir(xy=moveData, ot=as.Date(ot), rt=t.dates, edata=as.data.frame(int.ndvi), dir="both", mws=10, fun=of)
```

<br>

The output is a list containing the predicted values and a plot with their spatial distribution, seen in figure 2. Looking at the results, we can see that there are two poles: one describing low slope values (on top) and one describing relatively higher slope values (on the bottom). Comparing the results with figure 1, we see that the area characterized by lower slpe values corresponds to the urban area (where the animal is nesting) and the higher values correspond to the agricultural fields outside of the city. We can also see that, outside of the city, there differences between the fields which are captured by the NDVI based anlysis.

<br>

<p align="center"><img width="605" height="415" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_example-3.png"></p>

<p align="center"><sub>Figure 2 - Several points contained within the same pixel (in black) are aggregated into a single point (in red) which corresponds to the center of the pixel for a reference raster.</sub></p>

<br>

#### 2. Spatial analysis

<p align="justify">
If you wish to understand the influence of a changing landscape over the movement of an animal <i>spaceDir()</i> is the way to go. Using a moving window of one, this function looks either backwards or forward (or in both directions) in space and estimates a statistical metric for each segment quantifying the landscape changes experienced by an animal. More than a simple moving window, the approach adopted in function consideres the space between two endpoints. Using the input environmental raster as a reference, the function interpolates the space between the two coordinate pairs if the distance is bigger than the raster resolution. So let's consider the example data we used with <i>timeDir()</i> and apply <i>spaceDir()</i> with a backward sampling and estimate the slope. As environmental data we will use the NDVI image for the observation date.
</p>

<br>

```R
of <- function(x,y) {lm(y~c(1:length(y)))$coefficients[2]} # function to estimate the slope (x=time and y=NDVI)
space.env <- spaceDir(xy=shp, img=ndvi[[which(r.dates==as.Date("2013-08-04"))]], dir="bwd", of=of)
```

<br>

The output will consist of the endpoints and lines of the segments defined by consecutive points (figure 4).

<br>


<p align="center"><img width="605" height="415" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-3_example-3.png"></p>

<p align="center"><sub>Figure 3 - Several points contained within the same pixel (in black) are aggregated into a single point (in red) which corresponds to the center of the pixel for a reference raster.</sub></p>

<br>

The data for this example can be found <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/Example_3.zip">here</a>.

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
