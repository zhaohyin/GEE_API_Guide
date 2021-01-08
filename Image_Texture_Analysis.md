<p align=right><img src="https://encrypted-tbn2.gstatic.com/images?q=tbn:ANd9GcRnVCX6sxkThiZDS4Ol_Ekwm1tI9C0BTU834dhJ8i9vkEOoGURC" width =100></p>

# Image Texture Analysis
## 纹理算法理解
&emsp; 时间，空间，各式各样的物体都具有纹理，正如世界上没有任何一片雪花是相同的。不同的个体之间都存在着纹理上的差异，这是模式识别的基础。同一种植被在不同的生长状态下，时间序列上的纹理肯定是存在差异的，纹理的识别也要有尺度上面的考量。因此纹理的研究意义深远。

>博客1:纹理的理论基础。https://zhuanlan.zhihu.com/p/146475408

&emsp; 对于纹理的检测不可能仅仅使用 $x$ 和 $y$ 两个纬度的信息，通常情况下会使用 $d$ 个滤波器，由此就产生了 $d$ 维特征向量。常用的卷积核组如下，完成对边缘、bars和spots的纹理检测。
<img src="https://pic3.zhimg.com/v2-9d300636acbed4087f4764258c5cba9a_r.jpg">

&emsp; 在时间序列中，特别是获取时间点间隔不一定的遥感影像时间序列中。多变量的时间序列本身可以作为一个纹理。





<center>
<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/3.0/cn/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-nd/3.0/cn/88x31.png" /></a><br />本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/3.0/cn/">知识共享署名-非商业性使用-禁止演绎 3.0 中国大陆许可协议</a>进行许可。
</center>