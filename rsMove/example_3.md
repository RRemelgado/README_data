### Understanding Movement with Directional Sampling

<br>

<p align="justify">
As an animals moves through the landscape it experiences changing environmental conditions and decides on its next steps based on this knowledge. In order to capture these environmental changes we developed <i>spaceDirSample</i> and <i>timeDirSample</i>. In this example we will present the aplicability of these two functions and try to demonstrate when to use one or the other. For this purpose, as done in the example <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/example_2.md">"Tracking land cover changes in space and time"</a>, we will use data from one individual recorded in 2013-08-02 over Radofzell, Germany (Figure 1).
</p> 

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-1_example-2.png"></p>

<p align="center"><sub>Figure 1 - Land cover composition of the study site overlapped with the movement track (in black) of one White Stork during the 2nd of August of 2013.</sub></p>

<br>

<p align="justify">
Before advacing with their analysis, both functions start by summarizing the provided coordinate pair into pixel coordinates using a reference raster as a basis. Then, looking at the sequence of points, it segments them based on the assigned pixel positions and aggregates points if they are sequential and are located within the same pixel (figure 2). This step avoids the pseudo-replication of observation while preserving periodic movement patterns related to revisits. The output returns mean values for the coordinates and for the timestamps and provides an estimate of the elapsed time each point segment.
</p> 

<br>

<p align="center"><img width="605" height="315" src="https://github.com/RRemelgado/README_data/blob/master/rsMove/Figure-2_example-3.png"></p>

<p align="center"><sub>Figure 2 - Several points contained within the same pixel (in black) are aggregated into a single point (in red) which corresponds to the center of the pixel for a reference raster.</sub></p>

<br>

So, where is the difference between both functions? Well, if we want to understand if the temporal evolution of the landscape has an influence in, e.g., the decision of an animal to stop in a given location <i>timeDirSample()</i> is the way to go. To demonstrate this, we used Normalized Different Vegetation Index Data (NDVI) from the Moderate Resolution Spectroradiometer (MODIS) which was previously interpolated with the function <i>imgInt()</i>.





the influence of a changing landscape over the movement of an animal <i>spaceDirSample</i> is the way to go. Using a moving window of one, this function looks either backwards or forward (or in both directions) and applies a given function between the observed point and its imediate neighboor to evaluate how the landscape changes between them. More than a simple moving window, this function consideres the space in between the two points when applying the function. It queries the input environmental layer to determine its resolution and them interpotes the space between the two coordinate pairs to extract the pixels between them. So lets


<br>

The data for this example can be found <a href="https://github.com/RRemelgado/README_data/blob/master/rsMove/Example_3.zip">here</a>.

<br>

Click  <a href="https://github.com/RRemelgado/rsMove/">here</a> to get back to rsMove.

<br>
