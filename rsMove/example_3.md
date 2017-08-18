### Directional Analysis of Environmental Change

<br>

<p align="justify">
As an animals moves through the landscape it experiences changing environmental conditions and decides on its next steps based on this knowledge. In order to capture these environmental changes we developed <i>spaceDir()</i> and <i>timeDir()</i>. In this example we will present the applicability of these two functions and try to demonstrate when to use one or the other. For this purpose, as done in the example <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/example_2.md">"Tracking land cover changes in space and time"</a>, we will use data from one individual recorded in 2013-08-02 over Radofzell, Germany (Figure 1). So, when should we use each function? See the examples below.
</p> 

<br>

<p align="center"><img width="450" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_example-3.png"></p>

<p align="center"><sub>Figure 1 - Google image of the study site showing the example track (in black) and its GPS points (in red) for one White Stork during the 2nd of August of 2013.</sub></p>

<br>

#### 1. Temporal analysis

<p align="justify">
If we want to understand if the temporal evolution of the landscape influences e.g. the decision of an animal to stop at a given location <i>timeDir()</i> is the way to go. To demonstrate this, we used Normalized Different Vegetation Index Data (NDVI) from the Moderate Resolution Spectroradiometer (MODIS) which was previously masked for clouds and interpolated with the function <i>imgInt()</i> on a daily basis between 2013-07-28 and 2013-08-11 (<b>NOTE</b>: for an example on <i>imgInt()</i> consult <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/example_5.md">Point-Based Interpolation</a>). The data was downloaded with the <i>proSat()</i> function. Now let's read all the necessary data.
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
For each data point, the function selects the closest pixels in time within a temporal buffer that focuses on the evolution of environmental conditions from a given point in the past (backward sampling), until a given point in the future (forward sampling) or both. In this example, we will see the NDVI evolved around the observation data considering both past and future images. We used a temporal buffer of 10 days and estimated the slope. The slope will inform us if the NDVI is decreasing (negative), increasing (positive) or if it remained stable (close to zero).
</p>

<br>

```R
of <- function(x,y) {lm(y~x)$coefficients[2]} # function to estimate the slope (x=time and y=NDVI)
time.env <- timeDir(xy=moveData, ot=as.Date(ot), rt=t.dates, edata=as.data.frame(int.ndvi), dir="both", mws=10, fun=of)
```

<br>

The output is a list containing the predicted values and a plot with their spatial distribution, seen in figure 2. Looking at the results, we can see that there are two poles: one describing low slope values (on top) and one describing relatively higher slope values (on the bottom). Comparing the results with figure 1, we see that the area characterized by lower slope values corresponds to the urban area (where the animal is nesting) and the higher values correspond to the agricultural fields outside of the city. We can also see that, outside of the city, there differences between the fields which are captured by the NDVI based analysis.

<br>

<p align="center"><img width="605" height="415" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_example-3.png"></p>

<p align="center"><sub>Figure 2 - Direction of NDVI changes in time.</sub></p>

<br>

#### 2. Spatial analysis

<p align="justify">
If you wish to understand the influence of a changing landscape over the movement of an animal <i>spaceDir()</i> is the way to go. Using a moving window of one, this function looks either backwards or forward (or in both directions) in space and estimates a statistical metric for each segment quantifying the landscape changes experienced by an animal. More than a simple moving window, the approach adopted in function considers the space between two endpoints. Using the input environmental raster as a reference, the function interpolates the space between the two coordinate pairs if the distance is bigger than the raster resolution. So let's consider the example data we used with <i>timeDir()</i> and apply <i>spaceDir()</i> considering the NDVI in both directions and estimate the slope. As environmental data we will use the NDVI image for the observation date.
</p>

<br>

```R
of <- function(x,y) {lm(y~c(1:length(y)))$coefficients[2]} # function to estimate the slope (x=time and y=NDVI)
space.env <- spaceDir(xy=shp, img=ndvi[[which(r.dates==as.Date("2013-08-04"))]], dir="bwd", of=of)
```

<br>

Figure 3 shows us one of the outputs of <i>spaceDir()</i> describing the spatial variability of the NDVI. We can see that That lower NDVI values (< 0.6) represent the nesting area and that higher NDVI values (> 0.8) represents the fields where the animal was likely feeding. We can also see that the path between the nesting area and the potential feeding site is distinguished by in-between NDVI values.

<br>

<p align="center"><img width="605" height="415" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-3_example-3.png"></p>

<p align="center"><sub>Figure 3 - Direction of NDVI changes in space.</sub></p>

<br>

The data for this example can be found <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/Example_3_2.zip">here</a>.

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
