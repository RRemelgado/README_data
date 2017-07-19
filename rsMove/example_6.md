### Which Satellite Data to Use? On Pixel Resolution and Movement

<br>

<p align="justify">
When dealing with environmental remote sensing, we often need to carefully consider which satellite data to use. If we want to observe temporal patterns over a given landscape, sensors such as the Moderate Resolution Spectroradiometer (MODIS) and Sentinel 3 can seem like a good choice. But if we want to capture small scale patterns associated to, e.g., fragmented landscapes sensors such as Landsat and Sentinel 2 might be more adequate. However, when connectiving remote sensing and movement data we have to ask ourselves: <b>is the resolution high enough to preserve movement and/or occupancy patterns</b>? Sensors with a very high temporal resolution might seem appealing due to the dynamic nature of animal movements. But this often leads to the use of data with a low spatial resolution which might obscure the observed movement patterns and the environmental differences that guide them. Additionally, as the resolution decreases, we face issues such as pseudo-replication which reduces the effective number of samples we can split between training and validation when deriving predictive models. In order to address this issue we present <i>moveRes()</i>. This function provides a simple and quick test to quantify the impact of our choice in spatial resolution in our data. To demonstrate, let's first read our example data.
</p> 

<br>

```R
shp <- shapefile("./WhiteStork_20150418.shp") # movement data
```

<br>

<p align="justify">
This data provides a movement track for a White Stork in 2015-04-18 (Figure 1) and its extent occupies an area of 788 km<sup>2.
</p> 

<br>

<p align="center"><img width="600" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_Example-6.png"></p>

<p align="center"><sub>Figure 1 - Example movement data</b>.</sub></p>

<br>

<p align="justify">
Now, let's consider that we require open access sensors from which the Normalized Difference Vegetation Index (NDVI) can be estimated. For the purpose of this example, we'll look at Spot (5 m), Sentinel 2 (10 m), Landsat (30 m), MODIS (250 m) and Sentinel 3 (300 m). Now, let's call <i>moveRes()</i> specifying the resolution of these sensors.
</p> 

<br>

```R
# apply function
a.res <- moveRes(xy=shp, pxr=c(5, 10, 30, 250, 300))
```
<br>


<p align="justify">
The function returns a data frame containing information on the amount of unique pixels and the amount unique groups of connected pixels. Additionaly, it plots this information using <i>ggplot</i> (figure 2). Looking at the output, we can see that we loose roughly 5 pixels every 5 m and that by 300 m (Sentinel 3 resolution) we have only 1 region, which means effectively that all the available samples are aggregated. Given the size of the area, Spot or Sentinel 2 seem to be a good option.
</p> 

<br>

<p align="center"><img width="600" height="400" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_Example-6.png"></p>

<p align="center"><sub>Figure 2 - Number of samples available per spatial resolution and the correspondent number of unique regions (provided by default by <i>moveRes()</i>).</sub></p>

<br>

The data for this example can be found <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/Example_4.zip">here</a>.

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
