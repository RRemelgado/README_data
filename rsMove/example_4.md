### Temporally Dynamic Data Query

<br>

<p align="justify">
In this example, we present the functionality of the function <i>dataQuery()</i>. This function uses and extends upon the <i>extract()</i> function provided by the <i>raster</i> package by allowing the user to search for data in time. This is a predominant issue when connecting movement and remote sensing data. Movement data can be collected with a very high temporal resolution (e.g. minutes, hours) while remote sensing data from open-access sensors is available with a daily resolution, at most. To demonstrate the use of this function, let's consider the following example data. It consists of movement data from a White Stork and a interpolated MODIS NDVI time series (see <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/example_5.md">Point-Based Interpolation</a> for details).
</p> 

<br>

```R
# read data
shp <- shapefile("./WhiteStork_20150418.shp") # movement data
rs.stack <- stack(list.files('.', '.tif$') # read NDVI data
```

<br>

The movement data was recorded in 2015-04-18. But when are the images from? Let’s extract the dates of the images using their file names. We will need it to inform <i>dataQuery()</i>.

<br>

```R
# function to extract date from file name (in Date format)
sf <- function(x) {
    adate <- (sapply(x, function(x) {substr(strsplit(basename(x), '[.]')[[1]][2], 2, 9)})) # aq. date (1)
    adate <- as.Date(paste0(substr(adate, 1, 4), '-01-01')) + (as.numeric(substr(adate, 5, 8))-1) # aq. date (2)
    return(adate)
}

# apply function (returns a vector of dates)
r.dates <- do.call('c', lapply(vi.ls, sf))
```

<br>

<p align="justify">
At this points, we see that the dates are not in order (figure 1). This is because the data comes from both AQUA and TERRA sensors.
</p>

<br>

<p align="center"><img width="605" height="100" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_example-4.png"></p>


<p align="center"><sub>Figure 1 - R printout showing the order of dates in <i>r.dates</i>.</sub></p>

<br>

<p align="justify">
If this occurs, <i>dataQuery()</i> will not be affected. However, for visualization purposes, lets first order order the stack and the dates. 
</p> 

<br>

```R
rs.stack <- rs.stack[[order(r.dates)]]
r.dates <- order(r.dates)
```

<br>

<p align="justify">
Now that our data is properly sorted, let's see plot it. As we can see on figure 2, there are a lot of missing pixels due to cloud cover including pixels were GPS points were recorded. As a result, we might expect that one image might not be sufficient to obtain information for all data points. In these circunstances, we are forced to query the remote sensing data in time in order to find usable data.
</p> 

<br>

<p align="center"><img width="800" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_Example-4.png"></p>

<p align="center"><sub>Figure 2 - NDVI variability between 2015-03-22 and 2015-05-17.</sub></p>

<br>

<p align="justify">
For each sample, if a missing value is found for the target date, <i>dataQuery()</i> will search for the nearest non-NA value in time. The search is constrained by a pre-defined window size. This window can be adjusted to limit the search in the or future by providing two values. In other words, if the user is mostly interested in past values within e.g. 30 days, <b>tb</b> can be set as <i>c(30,0)</i> prompting the function to ignore future pixels.
For the purpose of this example, let's apply an equal buffer size (to the past and future) of 30 days using the following code.
</p> 

<br>

```R
query <- dataQuery(xy=shp, img=rs.stack, rt=r.dates, ot=as.Date(shp@data$timestamp), tb=c(30,30))
```

<br>

<p align="justify">
The output is a shapefile based on the original xy coordinates (figure 3) containing the extracted values and the date of the image that was used by each point. Looking at the output, we can see that the function required more than one date to provide data for all the coordinates. It used data from 2015-04-07 and from 2015-04-15.
</p> 

<br>

<p align="center"><img width="400" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-5_example-4.png"></p>

<p align="center"><sub>Figure 3 - NDVI values per point and their correspondent dates based on the output of <i>dataQuery()</i>.</sub></p>

<br>

The data for this example can be found <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/Example_4.zip">here</a>.

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
