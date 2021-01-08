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

