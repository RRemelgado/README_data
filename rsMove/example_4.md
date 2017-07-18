### Querying remote sensing data in time

<br>

<p align="justify">
In this example, we present the functionalty of the function <i>rsQuery()</i>. This function uses and extends upon the <i>extract()</i> function provided by the raster package by allowing the user to search for data in time. This is a predominant issue when connecting movement and remote sensing data. Movement data can be collected with a very high temporal resolution (e.g. minutes, hours) while remote sensing data from open-access sensors is available with a daily resolution, at most. The function allows for an 'exact' or 'nearest' search. So, what does this mean? To demonstrate the difference between both keywords, let's consider the following example data. First, lets read in the data.
</p> 

<br>

```
# read data
shp <- shapefile("D:/WhiteStork_20150418.shp") # movement data
rsStack <- stack(modis-ndvi_201504) # read remote sensing data
```

<br>

Then, let's extract the acquisition dates of the images using their file names. We will need it to inform <i>rsQuery()</i>.

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
We just read in a shapefile with movement data and a set of MODIS NDVI images. And, as we can see on figure 1, there are a lot of missing pixels due ot cloud cover.
</p> 

<br>

<p align="center"><img width="800" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_Example-4.png"></p>

<p align="center"><sub>Figure 1 - NDVI variability between 2015-03-22 and 2015-05-17.</sub></p>

<br>

<p align="justify">

</p> 










<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
