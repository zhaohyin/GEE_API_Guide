<p align=right><img src="https://code.earthengine.google.com/images/GoogleEarthEngine_v1.png" width = '200px' /> </p>

# GEE_Java_Bug
List the Bug and Debug ideas encountered in the process of learning GEE



## 1. 读图：显示collection中所有影像


***<font size=4 color=blue>Method 1</font>***: *display the image in raw image collections*


``` JavaScript
// for the display of raw image (Method 1)
// display result in uniform format
var visParams = {
  bands: ['B4', 'B3', 'B2'],
  min: 0,
  max: 3000,
  gamma: 1.4,
};

function addImage(image) { // display each image in collection
  var id = image.id;
  var image = ee.Image(image.id)
  Map.addLayer(image,visParams,id.substring(14,22),false);
}

img_tsc.evaluate(function(img_tsc) {  // use map on client-side
  img_tsc.features.map(addImage)
})

```


***<font size=4 color=blue>Method2</font>***: *Display all images in any collections*
显示任意的数据集


``` JavaScript
// for the display of image collection (Method 2)
// 转换为list
var listOfImages = cloudness_img_tsc.toList(cloudness_img_tsc.size());  //ee.List
var num = cloudness_img_tsc.size()  //235
for(var i = 168; i < 170; i++){
  var image = ee.Image(listOfImages.get(i));
  print(image.getString('system:index'));  //ee.String
  Map.addLayer(image, {min:0, max:0.3, bands:["B4", "B3", "B2"]},image.getString('system:index'),false)
}
```




***<font size=4 color=blue>But there exists a Bug in Method2</font>***
``` JavaScript
image.getString('system:index') is Wrong,because it is a ee.String object
The layer name must be a String
```
  > ~~Debug2-1~~: 使用getInfo()来获取值，相当于遍历\
  > ***<font size=5 color=red>404</font>***

``` JavaScript
/*
Map.addLayer(image, {min:0, max:0.3, bands:["B4", "B3", "B2"]},image.getString('system:index'),false)
*/
Map.addLayer(image, {min:0, max:0.3, bands:["B4", "B3", "B2"]},image.getString('system:index').getInfo(),false)  
//getInfo() waste time,thus should be changed into another method
```



> ~~Debug2-2~~: 将 CString对象转换为String对象\
> ***<font size=5 color=red>404, Give Up This Method</font>***

- [ ] ~~Idea: how to get the value of ee.String. or how to convert~~
    - [ ] ~~ee.String to String.~~
    - [ ] ~~ee.Object to String~~
- [ ] ~~result:It's too difficult, i need to ask for help tomorrow.~~

``` JavaScript
// try-1-404
ee.Image(listOfImages.get(i)).get('system:index')  // also obtain a ee.String 
```





---
<p align=right><button> Fri Jan8</button></p>

~~***思考1:对于GEE内部的数据结构还不是很清楚，CString如何检索出结果，令人费解。***~~

> 搬运自知乎blog：https://zhuanlan.zhihu.com/p/111417466\
> 太浪费时间了，需要解决问题，没时间学习了。。




***思考2:直接换用异步来进行处理，边读取影像边获取时间***


> Debug 2-3: 知乎大神的方法 \
> https://www.zhihu.com/search?type=content&q=GEE%20evaluate   

***<font size=5 color=blue>Succeed,But Slowly</font>***

```javaScript
// 深度解析下面的算法，弄清楚这里为什么就可以使用id.substring
// reduceColumns()??
// ee.Reducer.tolist()??
var computedIds = cloudness_img_tsc.reduceColumns(ee.Reducer.toList(), ['system:index'])
                        .get('list');

// evaluate的原理，异步是怎么一回事儿？？？                      
computedIds.evaluate(function(ids) {
  print("computedIds ", ids);
  for (var i=0; i<ids.length; i++) {
    var key = ids[i];
    var image = ee.Image(cloudness_img_tsc.filter(ee.Filter.eq("system:index", key)).first());
    Map.addLayer(image, {bands:["B4", "B3", "B2"], min:0, max:0.3}, key.substring(0,8),false);
  }
})
Map.centerObject(soybean_000, 13);
```




思考3: ***知乎大神还提供了另外一种解法***\
大致思路：通过ui界面来设置是否添加和删除影像，从而完成影像的选取。

***<font size=5 color=blue>Succeed,But Can't Search</font>***
> Debug 2-3: 知乎大神的方法 \
> https://www.zhihu.com/search?type=content&q=GEE%20evaluate   

```javascript
var rawLayer = null;
var computedIds = cloudness_img_tsc.reduceColumns(ee.Reducer.toList(), ['system:index'])
                        .get('list');
                        
computedIds.evaluate(function(ids) {
  print("computedIds ", ids);
  var total = ids.length;
  var showTitle = ui.Label("", {fontWeight: 'bold'});
  var curIndex = 0;
  var bPlus = ui.Button("+", function() {
    curIndex += 1;
    if (curIndex >= total) {
      curIndex = 0;
    }
    showTitle.setValue(ids[curIndex]);
    showSelectRawImage(ids[curIndex]);
  });
  var bReduce = ui.Button("-", function() {
    curIndex -= 1;
    if (curIndex < 0) {
      curIndex = total - 1;
    }
    showTitle.setValue(ids[curIndex]);
    showSelectRawImage(ids[curIndex]);
  });
  showTitle.setValue(ids[curIndex]);
  showSelectRawImage(ids[curIndex]);
  var main = ui.Panel({
    widgets: [
      ui.Label('click "+" or "-" to move time window', {fontWeight: 'bold'}),
      bPlus, bReduce,
      ui.Label("select date: ", {fontWeight: 'bold'}),
      showTitle
    ],
    style: {width: '200px', padding: '8px'}
  });
  ui.root.insert(0, main);
 
});

function showSelectRawImage(key) {
  if (rawLayer !== null) {
    Map.remove(rawLayer);
  }
  print("show raw image id is: " + key);
  var image = ee.Image(cloudness_img_tsc.filter(ee.Filter.eq("system:index", key)).first());
  rawLayer = Map.addLayer(image, {bands:["B4", "B3", "B2"], min:0, max:0.3}, key);
}

Map.centerObject(soybean_000, 13);
```


思考4: ***改进：添加搜索框***\
大致思路：通过ID来确定时间，从而在该时间前后进行搜索！！
***<font size=5 color=blue>OHHHHHHHHHHHHHHHH!!!!,Succeed!!!!</font>***

```javaScript
var rawLayer = null;
var computedIds = cloudness_img_tsc.reduceColumns(ee.Reducer.toList(), ['system:index'])
                        .get('list');
                        
computedIds.evaluate(function(ids) {
  print("computedIds ", ids);
  var total = ids.length;
  var showTitle = ui.Label("", {fontWeight: 'bold'});
  var curIndex = 0;
  var bPlus = ui.Button("+", function() {
    curIndex += 1;
    if (curIndex >= total) {
      curIndex = 0;
    }
    showTitle.setValue(ids[curIndex]);
    showSelectRawImage(ids[curIndex]);
  });
  var bReduce = ui.Button("-", function() {
    curIndex -= 1;
    if (curIndex < 0) {
      curIndex = total - 1;
    }
    showTitle.setValue(ids[curIndex]);
    showSelectRawImage(ids[curIndex]);
  });
  // --------------------------------------------
  var posLine = ui.Textbox('Insert ID');
  var posButton = ui.Button('Serch',function(){
    curIndex = posLine.getValue();
    showTitle.setValue(ids[curIndex]);
    showSelectRawImage(ids[curIndex]);
  });
  //---------------------------------------------
  showTitle.setValue(ids[curIndex]);
  showSelectRawImage(ids[curIndex]);
  var main = ui.Panel({
    widgets: [
      ui.Label('click "+" or "-" to move time window', {fontWeight: 'bold'}),
      bPlus, bReduce,posLine,posButton,
      ui.Label("select date: ", {fontWeight: 'bold'}),
      showTitle
    ],
    style: {width: '500px', padding: '8px'}
  });
  ui.root.insert(0, main);
 
});

function showSelectRawImage(key) {
  if (rawLayer !== null) {
    Map.remove(rawLayer);
  }
  print("show raw image id is: " + key);
  var image = ee.Image(cloudness_img_tsc.filter(ee.Filter.eq("system:index", key)).first());
  rawLayer = Map.addLayer(image, {bands:["B4", "B3", "B2"], min:0, max:0.3}, key);
}

Map.centerObject(soybean_000, 13);
//
```












## 附录
基本数据结构如下图所示：
<details>
    <summary> <font size=3 color=blue*>GEE基本数据结构</font> </summary>
<img src='https://pic4.zhimg.com/v2-ae28a8f46fb88e445e771db57a286737_r.jpg' >
</details>

---
<center>
<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/3.0/cn/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-nd/3.0/cn/88x31.png" /></a><br />本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/3.0/cn/">知识共享署名-非商业性使用-禁止演绎 3.0 中国大陆许可协议</a>进行许可。
</center>