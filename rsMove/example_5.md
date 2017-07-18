### Point-based interpolation

<br>

<p align="justify">
More than often, the date in which satellite imagery is acquired does not match the date in which an animal was tracked. However, when dealing with sensors with a high temporal resolution such as MODIS; we can use images acquired close in time to fill this gap. While interpolation has its downfalls, it is a good way to get a feeling on of our data before advancing for more robust analysis. In this context, we will show how to use <i>imgInt()</i>. This function performs a simple linear interpolation of missing data in a time series of raster images using existing temporal information. First, lets read the example data and retrieve the acquisition dates from their file name.
</p> 

<br>

```R
# read data
rs.stack <- stack(list.files('.', '.tif$') # read NDVI data
```

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
This contains a time series of MODIS NDVI images acquired between 2015-03-22 and 2015-05-17. This time series was based on MYD13Q1 and MOD13Q1 data and was masked using the correspondent products pixel reliability layer. As we can see in figure 1, this data has a lot of gaps as a result of cloud cover. In this example, we will attempt to fill the gaps in the image of 2015-05-01 (second from the bottom row) using the remaining images.
</p> 

<br>

<p align="center"><img width="800" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_Example-5.png"></p>

<p align="center"><sub>Figure 1 - NDVI time series between 2105-03-22 and 2015-05-17.</sub></p>

<br>

<p align="justify">
We will consider a temporal buffer of 60 days - define by the keyword <b>bs</b> - so that we consider all available images.
</p> 

<br>

```R
i.img <- imgInt(img=rs.stack, rd=r.dates, td=as.Date("2015-05-01"), bs=60)
```

<br>

<p align="justify">
The output (figure 2) shows that most of the pixels were filled. Pixels which were not filled were related to snow over mountainous terrain.
</p> 

<br>


<p align="center"><img width="500" height="300" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_Example-5.png"></p>

<p align="center"><sub>Figure 2 - Original image from 2015-05-01 (on the left) and its interpolation (on the right).</sub></p>

<br>

<p align="justify">
<b>Note</b>: If a point shapefile using the keyword <b>xy</b>, the function will only process the pixels the corresponding pixels.
</p> 

<br>

The data for this example can be found <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/Example_4.zip">here</a>.

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
