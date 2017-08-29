# EarthEngine
Code for code.earthengine.google.com

https://code.earthengine.google.com/538ad4f9ef86cc5f9ae8467d700b2cdd

//The script calcualtes gain in impervious surfaces (>50%) in Houston, TX from 2001 to 2011.
//Adam D. Griffith  28 AUG 2017   @321adam    adamdgriffith@gmail.com

//Import perimeter of City of Asheville from fusion table
var Houston = ee.FeatureCollection('ft:1KAd28guzkxyIe3HlmPwpA3ypLWoOUEshhQQdgELk');

//Load National Land Cover Database layers from 2001, 2006, and 2011 and clip to Houston
var NLCD2001 = ee.Image("USGS/NLCD/NLCD2001").clip(Houston);
var NLCD2006 = ee.Image("USGS/NLCD/NLCD2006").clip(Houston);
var NLCD2011 = ee.Image("USGS/NLCD/NLCD2011").clip(Houston);

//Create variables Imp20xx for 2001, 2006, and 2011 pixels with imperviousness of greater than 0.50
var Imp2001 = NLCD2001.select('impervious').gte(50);
var Imp2006 = NLCD2006.select('impervious').gte(50);
var Imp2011 = NLCD2011.select('impervious').gte(50);

//Set some visualization parameters and add layers
var vizParams = {
//  bands: ['ImpDiff'],
  min: 1,
  max: .4,
  opacity: 0.9,
};
Map.addLayer(Imp2001, vizParams);
Map.setCenter(-95.37, 29.76, 11)

var impervious_area = ee.Image.pixelArea().divide(1000 * 1000).addBands(Imp2011)
 .reduceRegion({reducer: ee.Reducer.sum().group(1),
    geometry: Houston,scale: 30,});
print('impervious_area (km2)',  impervious_area)
