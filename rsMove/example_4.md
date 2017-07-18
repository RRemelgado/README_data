### Querying remote sensing data in time

<br>

<p align="justify">
In this example, we present the functionalty of the function <i>rsQuery()</i>. This function uses and extends upon the <i>extract()</i> function provided by the <i>raster</i> package by allowing the user to search for data in time. This is a predominant issue when connecting movement and remote sensing data. Movement data can be collected with a very high temporal resolution (e.g. minutes, hours) while remote sensing data from open-access sensors is available with a daily resolution, at most. The function allows for an <b>exact</b> or </b>nearest</b> search. So, what are the implications of using each keyword? To demonstrate the difference between both keywords, let's consider the following example data. It consists of movement data from a White Stork and a MODIS NDVI time series.
</p> 

<br>

```
# read data
shp <- shapefile("./WhiteStork_20150418.shp") # movement data
rsStack <- stack(./modis-ndvi_201504) # read NDVI data
```

<br>

The movement data was recorded in 2015-04-18. But when are the images from? let's extract the dates of the images using their file names. We will need it to inform <i>rsQuery()</i>.

<br>

```
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
At this points, we see that the dates are not in order (figure 2). This is because the data comes from both AQUA and TERRA sensors.
</p>

<p align="center"><img width="605" height="100" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_example-4.png"></p>

<p align="center"><sub>Figure 2 - R printout showing the order of dates in <i>r.dates</i>.</sub></p>


<p align="justify">
As we can see on figure 1, there are a lot of missing pixels due to cloud cover including pixels were GPS points were recorded. Additionaly, we see that the nearest acquisition date to the in relation to the tracking date is 2015-04-23. As a result, if we use and <b>exact</b> query we will not find any data. This is because this keyword would only extract data if referent to the tracking date (<b>NOTE</b>: For addressing this particular issue see point-based interpolation). So, what happens when we prompt the keyword <b>nearest</b>?
</p> 

<br>

<p align="center"><img width="800" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_Example-4.png"></p>

<p align="center"><sub>Figure 1 - NDVI variability between 2015-03-22 and 2015-05-17.</sub></p>

<br>

<p align="justify">
The <b>nearest</b> keyword search for the closest data in time to the observation date. As we saw, the nearest date is 2015-04-23. However, as we see in figure 1, this acquisition also contains a few missing values. When this occurs, <i>rsQuery()</i> will consider the next closest acquisition where no missing values were observed. The function will evaluate each pixel separately. to exemplify, let's apply the following code
</p> 










<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
