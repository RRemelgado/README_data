### Accessing remote sensing data

<p align="justify">
<i>rsMove</i> provides access to a series of remote sensing products. It allows the user to <b>download</b>, <b>crop</b>, <b>mask</b> and <b>re-project</b> this data easing the process of introducing it within ecological studies. We developed two functions to achieve this: <i>getEnv()</i> and <i<proSat()</i>. The functions perform the same pre-processing steps. However, they provide access to different kinds of that. So, when to use one or the other?
</p>

<br>

### 1. GetEnv()

<p align="justify">
This function provides access to "static" remote sensing products. In other words, it focus on products which are derived on a yearly basis or with no predicted update. This includes da from:
</p>

* Yale University EarthEnv (<a href="http://www.earthenv.org/">EarthEnv</a>)
* ESA's Climate Change Initiative (<a href="http://cci.esa.int/">CCI</a>)
* JRC's Global Human Settlements (<a href="http://ghsl.jrc.ec.europa.eu/">GHS</a>)
* Maryland University Global Forest Change (<a href="https://earthenginepartners.appspot.com/science-2013-global-forest">GFC</a>)
* JRC's Global Surface Water dynamics (<a href="https://global-surface-water.appspot.com/">GSW</a>)

<br>

<p align="justify">
As each of the sources provides a wide range of datasets, we decided provide its list through the function rather than trough the standard R documentation. To access this information, you only need to specify the data source when calling the function (figure 1).
</p>

<br>

```R
getEnv(d.source="EarthEnv") # EarthEnv
getEnv(d.source="EarthEnv") # CCI
getEnv(d.source="EarthEnv") # GHS
getEnv(d.source="EarthEnv") # GFC
getEnv(d.source="EarthEnv") # GSW
```

<p align="center"><img width="200" height="600" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_example-8.png"></p>
 
 <p align="center"><sub>Figure 1 - Example output of <i>getEnv()</i> for "CCI" data.</sub></p>

<br>

### proSat()

<p align="justify">
This function provides access to time series of remote sensing data. For the time being, we decided to focus on MODIS products due to their variety and temporal/spatial consistency which allows for their interoperability. As done for <i>getEnv()</i>, we "hid" the product list within the package. We can access it by calling the function without defining any variable (figure 2).
</p>

```R
proSat() # return all variables provived by the function
```

<p align="center"><img width="600" height="260" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_example-8.png"></p>
 
 <p align="center"><sub>Figure 1 - Example output of <i>getEnv()</i> for "CCI" data.</sub></p>

<br>

<p align="justify">
The table provided by <i>proSat()</i> will return the product resolution (spatial and temporal), its sensor of origin (e.g. MODIS) and inform the user if there is a need to provide credentials by specifying their source. e.g. MODIS snos/ice products required EarthData user credentials in order to acccess the online archive. To do so, specify the usrname and password as shown by the example below.
</p>

```R
# download sea ice data
proSat(t.var="ice", xy=movement.data, o.time=movement.date, d.path=".", user.cred=c("username", "password")
```

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
