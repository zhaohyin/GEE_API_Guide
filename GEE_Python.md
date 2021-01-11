<p align=right><img src="https://code.earthengine.google.com/images/GoogleEarthEngine_v1.png" width = '200px' /> </p>

# GEE_Python
若想要使用GEE_Python,必须要***翻墙***。设置terminal为全局代理模式，如下所示：
```python
### 2020年小麦时间序列纹理曲线 ###
# 设定终端全局变量进行科学上网
import os
os.environ['HTTP_PROXY'] = 'http://127.0.0.1:1087'
os.environ['HTTPS_PROXY'] = 'http://127.0.0.1:1087'
# Import the Earth Engine Python Package
import ee
ee.Initialize()
```
---

<p align=right><button>Fri Jan 8</button></p>

## 0. 扩展插件的下载与安装
***<font size=4 color=red> Question </font>***：GEE中函数有很多端口的属性是未知的，需要插件来查看函数的属性值。
  
  - 安装jupyter lab的插件

    > Error: nodejs >= 10.0.0 

    Debug1: ***<font color=red>404</font>***
    
    > 使用conda环境(gdal-python)进入\
    > conda install -c conda-forge jupyterlab 

    Debug2:  ***<font color=blue> Succeed </font>***

``` zsh
node -v  #查看nodejs的版本号 
whcih node  #查看nodejs的位置 
# 下载最新的 nodejs版本 即可解决问题
conda install -c conda-forge nodejs
conda install -c conda-forge/label/gcc7 nodejs
conda install -c conda-forge/label/cf201901 nodejs
conda install -c conda-forge/label/cf202003 nodejs
```
问题解决 ！！

## 1. 获取GEE中的pixel的DN值--以Sentinel-2为例
***<font size=4 color=red> Question </font>***：GEE使用java来初步观察数据，然后利用python来分析数据是最为快速的方法，其中的关键问题就是如何利用python获取遥感影像中的pixel数据。
以下为可直接使用的源码.\
<img src="https://img-blog.csdnimg.cn/20201018163303809.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNzE4MzU3,size_16,color_FFFFFF,t_70" height=230>
<img src="https://img-blog.csdnimg.cn/20190505130856717.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNzE4MzU3,size_16,color_FFFFFF,t_70" height=230>\
&emsp;因为与植被密切相关的就是B、G、R，Red Edge、NIR波段，其中与土壤背景密切相关的是短波红外（SWIR）。因此B2-B8，B10，B11纳入考虑！！！

```python
def getPdDataFrameDN(collection,roi,drop_labels=[]):
    '''
    collection-Sentinel-2 Raw Images
    
    '''
    def getDnValues(collection,geometry,band,reducer='mean',side = 'client'):
        data = tools.imagecollection.getValues(
            collection=collection,
            geometry=geometry,
            reducer = reducer,
            side = side
        )
        # convert dict to pandas
        df = pd.concat({k: pd.Series(v) for k, v in data.items()}).reset_index()
        df.columns = list(['S2-Time','Band',band])
        df = df.drop(columns=['Band'])
        return df
    #
    dn_swir2 = getDnValues(collection = collection.select('B11'),
                      geometry = roi,band = 'B11').drop(labels=drop_labels)
    dn_swir1 = getDnValues(collection = collection.select('B10'),
                      geometry = roi,band = 'B10').drop(labels=drop_labels)
    dn_nir = getDnValues(collection = collection.select('B8'),
                      geometry = roi,band = 'B8').drop(labels=drop_labels)
    dn_redge3 = getDnValues(collection = collection.select('B7'),
                      geometry = roi,band = 'B7').drop(labels=drop_labels)
    dn_redge2 = getDnValues(collection =collection.select('B6'),
                      geometry = roi,band = 'B6').drop(labels=drop_labels)
    dn_redge1 = getDnValues(collection = collection.select('B5'),
                      geometry = roi,band = 'B5').drop(labels=drop_labels)
    dn_r = getDnValues(collection = collection.select('B4'),
                      geometry = roi,band = 'B4').drop(labels=drop_labels)
    dn_g = getDnValues(collection = collection.select('B3'),
                      geometry = roi,band = 'B3').drop(labels=drop_labels)
    dn_b = getDnValues(collection = collection.select('B2'),
                      geometry = roi,band = 'B2').drop(labels=drop_labels)
    # 建时间序列数据库df_all
    doy = []
    for hh in dn_r['S2-Time'].tolist():
        doy.append(hh[:8])
    #
    df_all = pd.DataFrame(columns = ['S2-Time'])
    df_all['S2-Time'] = pd.to_datetime(doy)
    df_all['B2'] = dn_b['B2'].tolist()
    df_all['B3'] = dn_g['B3'].tolist()
    df_all['B4'] = dn_r['B4'].tolist()
    df_all['B5'] = dn_redge1['B5'].tolist()
    df_all['B6'] = dn_redge2['B6'].tolist()
    df_all['B7'] = dn_redge3['B7'].tolist()
    df_all['B8'] = dn_nir['B8'].tolist()
    df_all['B10'] = dn_swir1['B10'].tolist()
    df_all['B11'] = dn_swir2['B11'].tolist()
    #
    return df_all
```

## 

---
<center>
<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/3.0/cn/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-nd/3.0/cn/88x31.png" /></a><br />本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/3.0/cn/">知识共享署名-非商业性使用-禁止演绎 3.0 中国大陆许可协议</a>进行许可。
</center>
