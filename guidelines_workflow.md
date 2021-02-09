# Guidelines to assess trends in NDVI (1999-2020) time series

### Objectives
The overarching objective of this block is to create a map that shows the trend of photosynthetic activity of Sierra Nevada vegetation. This will be done using remote sensing techinques. More specifically, we will work with [NDVI](https://en.wikipedia.org/wiki/Normalized_difference_vegetation_index) (Normalized Difference Vegetation Index). This index is considered as a good surrogate of primary production.

Besides, we have two types of learning objectives:

 + Ecological: To improve our knolwedge on the functional aspects of terrestrial ecosystems. To connect with the importance of long term series to assess the impact of global change
+ Informatics:
  + To discover the huge potential of Google Earth Engine to process remote sensing data. 
  + To learn two different ways of executing the same workflow. This will elicit the multifunctionality of ecoinformatic techniques.



### General workflow
The figure below shows the general workflow to achieve our objectives. The two threads depict the two different approaches that will follow to create a map showing the NDVI trend from 1999 to 2020. This document deals with the thread on your left (the one which uses GEE and R). 









created during the ecoinformatic season 2021. It is a dynamic document created with [draw.io] (https://about.draw.io/). The thread describing how to compute NDVI trends is shown with an orange background.

The following sections describe both parts this workflow.

<div class="mxgraph" style="max-width:100%;border:1px solid transparent;" data-mxgraph="{&quot;highlight&quot;:&quot;#0000ff&quot;,&quot;nav&quot;:true,&quot;resize&quot;:true,&quot;page&quot;:0,&quot;toolbar&quot;:&quot;pages zoom layers lightbox&quot;,&quot;edit&quot;:&quot;_blank&quot;,&quot;url&quot;:&quot;https://drive.google.com/a/go.ugr.es/uc?id=1BBVPDd-Vn8IEteS6XUITHbm7YLO5uWnA&amp;export=download&quot;}"></div>
<script type="text/javascript" src="https://www.draw.io/embed2.js?&fetch=https%3A%2F%2Fdrive.google.com%2Fa%2Fgo.ugr.es%2Fuc%3Fid%3D1BBVPDd-Vn8IEteS6XUITHbm7YLO5uWnA%26export%3Ddownload"></script>



### Step by step (Google Earth Engine). Load Landsat 7 collection. Create yearly NDVI composite.

- (0) This wokflow is also described in [this] (https://www.youtube.com/watch?v=QkD8kDF2vkg&t=14s) video (Spanish). 
- (1) Open [Google Earth Engine code editor] (https://code.earthengine.google.com/) and login with your user.
- (2) Create a new repository: Scripts -> New -> Repository. Name: ecoinformatics.
- (3) Create a new file: New-> file. Name: create_ yearly_NDVI
- (4) Since GEE contains information for the whole planet, we firstly define the area of interest. The example below considers that the area of interest is Sierra Nevada protected area. But, you should use a specific area of that region in order to overcome the limits of our internet connection.

 - (4.1) Import the limit of the area of interest (AOI). Each of you will use a different region. Find below the links for those areas:

     -  [Sierra Nevada protected area] (https://drive.google.com/file/d/1NoQ9YUj7zrKWlHGqkcSW2_-1NvP0dn6F/view?usp=sharing)
     -  [Zona_1] (https://drive.google.com/open?id=1hMDUctg5FoE1yn4nYTlOmY1XwV980MVV)
     -  [Zona_2] (https://drive.google.com/open?id=1H2hW4KQiJm8HCBTTEonAzJNXt0f1ZuMg)
     -  [Zona_3] (https://drive.google.com/open?id=1nyar_STMbC03dHbwXJy2NBGW2QfFiSWu)
     -  [Zona_4] (https://drive.google.com/file/d/1VRCCGalH8zhzkGOls9B_kX6Q-_tTY0iD/view?usp=sharing)
     -  [Zona_5] (https://drive.google.com/open?id=1UEw26msrL-Mp51ilh8l6mTTB0Ua9LYFg)
     -  [Zona_6] (https://drive.google.com/open?id=1mdiALIIm7UFQbDs9nxD1sF5prz-yKUZu)
     -  [Zona_7] (https://drive.google.com/open?id=1fe7YDHtWrzjIIjWo_4WgXm-Vx-7CTlfV)

     
     
      I order to import any of the above described shapefiles, just follow these steps: Assets-> New -> Table upload -> Shapefiles. Select all files called **pnatural_23030.**. You can also upload a _.zip_ file containing all the files belonging to the shapefile. 

 - (4.2) Add a name for the newly created asset: **pnatural_23030**

 - (4.3) Press _Upload_. After some seconds, a new asset will be created.

- (5) Add **pnatural_23030** to the script. Click [here](https://drive.google.com/file/d/1NoQ9YUj7zrKWlHGqkcSW2_-1NvP0dn6F/view?usp=sharing) to download the Shapefile.

 + [_ee.FeatureCollection_] (https://developers.google.com/earth-engine/feature_collections) can contain geometrical objects. In this case, we have used it to contain the limits of Sierra Nevada protected area.

 + _Map.centerObject_ put the focus on our area of interest. We will see Sierra Nevada when executing this line. 

 Copy and paste the code below in your script. You have to change the path to your folder within GEE. Run the script to see the results 

```javascript
var zona = ee.FeatureCollection("users/fjbonet/pnatural_23030");
Map.centerObject(zona,11);
```
- (6) Load Landsat 7 collection. Filter by "zona" and year.
 + We will use the function [_ee.ImageCollection_] (https://developers.google.com/earth-engine/ic_creating) to access all the time series pertaining Landsat 7. More specifically, we will use the product called [LANDSAT/LE07/C01/T1_SR] (https://developers.google.com/earth-engine/datasets/catalog/LANDSAT_LE07_C01_T1_SR) to compute NDVI.
 + All the images existing in the above mentioned datasets will be stored in the variable called _l7collection_.
 + We will also filter all the images by area (using _filterBounds_) and by year (using _filterDate_). Since we will create a yearly NDVI image, we will have to change the year several times (21 times, from 1999 to 2019).

 Copy and paste the code below in your script. Run the script to see the results. Nothing happens? You have not asked GEE to show the results... Just add _print(l7collection);_ to the script. Run again and you will see a list showing the description of all the images.

```javascript
// Load landsat 7 dataset. Filter by "zona" and time.

var l7collection = ee.ImageCollection('LANDSAT/LE07/C01/T1_SR')
          .filterBounds(zona)
          .filterDate('2003-01-01', '2003-12-31');
```

- (7) Calculate NDVI for each image. 
 + Since NDVI is a very common index, GEE has implemented a function that calculates it in a very straightfoward way. _normalizedDifference_ computes the following operation for bands _B3_ (red) and _B4_ (infrared): _(B4-B3)/(B4+B3)_ 
 + The obtained result is stored in a new band called _NDVI_
 + This process is "stored" in a function called _getNDVI_

 Copy and paste the code below in your script. Run the script to see the results. Nothing new happens? We have just create a new function (_getNDVI_), but we have not used it yet.

```javascript
// Calculate NDVI
var getNDVI = function(img){
  return img.addBands(img.normalizedDifference(['B4','B3']).rename('NDVI'));
};
```
- (8) Use a loop to calculate NDVI for each image within the collection. 
 + The funciton _map()_ is a very simple way to create loops in GEE. It allows to "project" a function (_getNDVI_) over a collection (_l7collection_). The result is stored in a variable called _l7ndvi_
 + When using this function, GEE add a new band to each image called _NDVI_.

 Copy and paste the code below in your script. Check if the new band is created.

 ```javascript
 // map over image collection. Calculates NDVI for each image in the collection
var l7ndvi = l7collection.map(getNDVI);
print(l7ndvi);
 ```

- (9) Create a composite (a single image from many others) containing the maximum NDVI value per pixel in the selected year.
 + [Compositing] (https://developers.google.com/earth-engine/ic_composite_mosaic) is a very common process in remote sensing that refers to the process of combining spatially overlapping images into a single one based on an aggregation function. In this case, we will creates a single image that contains the maximum NDVI value per pixel. I.e. The maximum value of NDVI in the pixel _A_ could occurr in the date _x_, while that maximum value in pixel _B_ could occurr in date _y_. This means that the date of the image would be a composite of all the combined images.
 + The function _qualityMosaic_ is in charge of create the above mentioned composite. It operates over the NDVI collection (_l7ndvi_) and stores the result in a variable called _composite_. 

 Copy and paste the code below in your script. Check if the new image is created.

```javascript
var composite = l7ndvi.qualityMosaic('NDVI').clip(zona);
print(composite);
```

- (10) Visualize the obtained image. We will use the function _Map.addLayer_ to view the NDVI composite created above. 
 + We will first create a variable called _ndviPalette_ that contains 17 hexadecimal numbers. Each of them means a specific [color] (https://www.color-hex.com/). This is a [palette] (https://developers.google.com/earth-engine/tutorial_api_02): comma delimited list of color strings which are alinarly interplated between the maximum and minimum values in the visualization parameters.
 + Then, we will use the function _Map.addLayer_ to add the band _NDVI_ of the combined image (_composite_) using the above created palette (_ndviPalette_). 

 Copy and paste the code below in your script. You will see a nice image showing the maximum NDVI value per pixel of the selected year.

```javascript
//Visualize NDVI
var ndviPalette = ['FFFFFF', 'CE7E45', 'DF923D', 'F1B555', 'FCD163', '99B718',
               '74A901', '66A000', '529400', '3E8601', '207401', '056201',
               '004C00', '023B01', '012E01', '011D01', '011301'];
Map.addLayer(composite.select('NDVI'),
            {min:0, max: 1, palette: ndviPalette}, 'ndvi');
     
```

- (11) Export the composite image to file. The last step of this workflow within GEE is to export the image to a _.tif_ file. 
 + The function [_Export.image.toDrive_] (https://developers.google.com/earth-engine/exporting) allows us to export the band _NDVI_ from the image _composite_ to a _.tif_ file called _ndvi-1999_. 
 + It is possible to specify the file format, the coordinate system and other properties of the exported image.
 + When excecuting this function, a new task will appear in the corresponding tab. We must click on "run". A new box will appear that lets us change the name of the image and the folder of our Drive account where it will be saved.

 Copy and paste the code below and click Run on the script. We are almost done!

```javascript
// Export to image
Export.image.toDrive({
  image: composite.select('NDVI'),
  description: 'ndvi_2004',
  scale: 30,
  region: zona,
  fileFormat: 'GeoTIFF',
  crs: 'EPSG:23030'
});
```

- (12) I am sure that you have realized that this whole process could be improved by creating a loop that interates the years. You are right. We are working on it. In the meantime, find [here] (https://drive.google.com/open?id=13Nbf8uxrrIUeCDjMe6WijtIICEjD4_A4) a heavy (156MB) _.zip_ file containing all the NDVI composites from the time series.


### Step by step (RStudio). Compute Mann Kendall time series analysis on NDVI yearly maps.

- (0) This wokflow is also described in [this] (https://youtu.be/22dlKcNa_SI) video (Spanish).
- (1) We first define the working directory. You just have to change the path to your own one.

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
knitr::opts_knit$set(root.dir = "/Users/fjbonet_trabajo/Google Drive/4_docencia/ugr/master_ugr_cons_bio/curso_2019_2020/ecoinf_2019_2020/material_2019_2020/05_NDVI/material_profes/ndvi/pinarr")

library(raster)
library(Kendall)

```


- (2) Create a stack containing all _.tiff_ files created by GEE. A [RasterStack] (https://www.rdocumentation.org/packages/raster/versions/3.0-12/topics/stack) is a collection of Raster Layers with the same spatial extent and resolution. It can be created from raster files.

 + We first create a list containing the names of all _.tif_ files that will be included in the stack.
 + Then, we use the above mentioned function (_Stack_) to create a multi-layer object.
 + Once we applied _Stack_ function, we will use _Brick_ function over it. A [Brick] (https://www.rdocumentation.org/packages/raster/versions/2.9-23/topics/brick) is a multi-layer raster object. It is similar to _Stack_, but its processing time should be shorter.
 + Finally, we will export the created object to a _.tif_ file. We will use this multi-layer file to create trends graphs in Quatum GIS.

 

 ```r
# Create a list containing the names of all .tif files.
list_images <- list.files(pattern='*.tif', full.names=TRUE)
# Then, we create a stack with all of them
ndvis <- brick(stack(list_images))
plot(ndvis)
# Export the stack to .tif as a multiband file. It will be usedul to create trend graphs.
writeRaster(ndvis, filename="ndvi_1999_2019_pp.tif", format="GTiff", overwrite=TRUE)
 ```

- (3) Run [Mann Kendall] (https://www.statisticshowto.datasciencecentral.com/mann-kendall-trend-test/) test over all "bands" within the multi-layer object. This test is very useful to analyze data collected over time for consistently increasing or decreasing trends. Since it is a non-parameetric test, it can be used for all distributions. 

 + We will "embed" [Mann Kendall] (https://www.rdocumentation.org/packages/Kendall/versions/2.2/topics/MannKendall) test within an R function called _fun_k_
 + Then, we will create a new object called _kendal_result_ that contains the results of applying Kendall function over the multi-layer object (_ndvi_)
 + Mann Kendall test yield another spatial object containing five bands. We will pay attention to the first two ones:
   + _tau_= Kendall's _tau_ statistic. This layer contain the trends that Kendall has found in our time series. Negative values means negative trends and viceversa. 0 means that there is no trend.
   + _sl_= Kendall's _sl_ statistic. The signification value of the two-sided p-value. It indicates the statistical significance of the trend. 
 + Finally, we export the _tau_ band to a _.tif_ image called _tau.tif_

 ```r
# We first create a function containing the MannKendall test
fun_k <-function(x){return(unlist(MannKendall(x)))}
# That function is applied over the above created stack.
kendal_result <-calc(ndvis, fun_k)
# The obtained results has several "bands". We will export "layer.1" which depicts "tau" (Kendall's tau statistic)
writeRaster(kendal_result$tau, filename="tau_p.tif", format="GTiff", overwrite=TRUE)
plot(kendal_result$tau)
 ```

- (4) It is also interesting to reclassify the _tau_ layer, in order to obtain a shorter list of classes.  By using the function _reclassify_, we will create the following classes in the new layer:

| From old value | To old value | New value|
|----------------|:------------:|---------:|
|-1|-0.25| -1|
| -0.25| 0.25|0|
|0.25|1|1 

 + This process requires the creation of a R numeric string (called _m_) containing the reclassify table. 
 + This string is converted to a matrix called _rclmat_ using the function _matrix_
 + A new raster object is created (_rc_ _ _tau_) when applying the _reclassify_ function to the original raster (_kendal_result$tau_)
 + We finally create a new raster with the reclassified information.

```r
m <- c(-1, -0.25, -1, -0.25, 0.25, 0, 0.25, 1, 1)
rclmat <- matrix(m, ncol=3, byrow=TRUE)
rc_tau <- reclassify(kendal_result$tau, rclmat)
writeRaster(rc_tau, filename="rc_tau.tif", format="GTiff", overwrite=TRUE)

```

 



