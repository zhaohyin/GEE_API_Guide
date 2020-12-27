# Bug && Debug
List the Bug and Debug ideas encountered in the process of learning GEE

## 1.Display all images in the collection
- Method 1: display the image in raw image collections
``` JavaScript
function addImage(image) { // display each image in collection
  var id = image.id;
  var image = ee.Image(image.id)
  Map.addLayer(image, {bands:["B4", "B3", "B2"], min:0, max:3000},id.substring(14,22),false);
}

img_tsc.evaluate(function(img_tsc) {  // use map on client-side
  img_tsc.features.map(addImage);
})
```
- Method2:Display all images in any collections
``` JavaScript
var listOfImages = cloudness_img_tsc.toList(cloudness_img_tsc.size()); 
var num = cloudness_img_tsc.size()
for(var i = 168; i < 170; i++){
  var image = ee.Image(listOfImages.get(i));
  print(image.getString('system:index'));
  Map.addLayer(image, {min:0, max:0.3, bands:["B4", "B3", "B2"]},image.getString('system:index'),false)
}
```
But there exists a Bug in Method2
``` JavaScript
image.getString('system:index') is Wrong,because it is a ee.String object
The layer name must be a String
```
- Debug:
