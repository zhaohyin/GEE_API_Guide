<p align=right><img src="https://encrypted-tbn2.gstatic.com/images?q=tbn:ANd9GcRnVCX6sxkThiZDS4Ol_Ekwm1tI9C0BTU834dhJ8i9vkEOoGURC" width =100></p>

# Image Texture Analysis
## 纹理算法理解
&emsp; 时间，空间，各式各样的物体都具有纹理，正如世界上没有任何一片雪花是相同的。不同的个体之间都存在着纹理上的差异，这是模式识别的基础。同一种植被在不同的生长状态下，时间序列上的纹理肯定是存在差异的，纹理的识别也要有尺度上面的考量。因此纹理的研究意义深远。

>博客1:纹理的理论基础。https://zhuanlan.zhihu.com/p/146475408

&emsp; 对于纹理的检测不可能仅仅使用 $x$ 和 $y$ 两个纬度的信息，通常情况下会使用 $d$ 个滤波器，由此就产生了 $d$ 维特征向量。常用的卷积核组如下，完成对边缘、bars和spots的纹理检测。
<img src="https://pic3.zhimg.com/v2-9d300636acbed4087f4764258c5cba9a_r.jpg">

&emsp; 在时间序列中，特别是获取时间点间隔不一定的遥感影像时间序列中。多变量的时间序列本身可以作为一个纹理。在进行分析之前需要进行合理的归一化

## 归一化
&emsp; 归一化，共有三种方法：

1. min-max: 将区间统一到[0,1]，极端值的影响会很大，且无法避免。
    >$X'= (X-minX)/(maxX-minX)$
2. meanStd：标准化，消除量纲，仅从数据分布的角度来看待不同的变量。寻找变量之间的内部联系
    >$X'= (X-meanX)/stdX$
3. meanZero: 使得均值为0，但是无法消除量纲。比如说：[100,200,300]和[1,2,3]
    >$X'= X-meanX$

一般情况下是用标准化是比较好的，使得变量之间可以进行比较。
```python
# 归一化策略
def normalize_minMax(values):  # min_max
    minV = min(values)
    maxV = max(values)
    n_values = []
    for i in range(len(values)):
        n_value = (values[i] - minV)/(maxV-minV)
        n_values.append(n_value)
    return n_values

def normalize_meanStd(values):  # mean_std
    meanV = np.mean(values)
    stdV = np.std(values)
    n_values = []
    for i in range(len(values)):
        n_value= (values[i] - meanV) / stdV
        n_values.append(n_value)
    return n_values

def normalized_meanZero(values): # meanZero
    meanV = np.mean(values)
    n_values = []
    for i in range(len(values)):
        n_value= values[i] - meanV
        n_values.append(n_value)
    return n_values
```

## LBP纹理
**<p align=right>Edit by zhyin, Mon Jan 11</p>**
LBP（Local Binary Pattern，局部二值模式）是一种用来描述图像局部纹理特征的算子；它具有旋转不变性和灰度不变性等显著的优点。它是首先由T. Ojala, M.Pietikäinen, 和D. Harwood 在1994年提出，用于纹理特征提取。而且，提取的特征是图像的局部的纹理特征；主要是旋转不变、光照不变、
尺度不变三大特性。

<details>
    <summary> <font size=3 color=blue*>LBP源代码</font> </summary>

```python
# 合理的归一化
# LBP算法
# 在圆形选取框基础上，加入旋转不变操作
import sys
import numpy as np
import cv2 as cv

def rotation_invariant_LBP(img, radius=1, neighbors=8):
    h,w=np.shape(img)
    print(h,w)
    dst = np.zeros((h-2*radius, w-2*radius),dtype=np.uint8)  # radius =1, [3，3]->[1,1]
    for i in range(radius,h-radius):
        for j in range(radius,w-radius):
            # 获得中心像素点的灰度值
            center = img[i,j]
            for k in range(neighbors):
                # 计算采样点对于中心点坐标的偏移量rx，ry
                rx = radius * np.cos(2.0 * np.pi * k / neighbors)
                ry = -(radius * np.sin(2.0 * np.pi * k / neighbors))
                # print(f'rx-{rx},ry-{ry}')
                # 为双线性插值做准备
                # 对采样点偏移量分别进行上下取整
                x1 = int(np.floor(rx))
                x2 = int(np.ceil(rx))
                y1 = int(np.floor(ry))
                y2 = int(np.ceil(ry))
                # print(f'x1-{x1},x2-{x2},y1-{y1},y2-{y2}')
                # 将坐标偏移量映射到0-1之间
                tx = rx - x1
                ty = ry - y1
                # print(f'tx-{tx},ty-{ty}')
                # 根据0-1之间的x，y的权重计算公式计算权重，权重与坐标具体位置无关，与坐标间的差值有关
                w1 = (1-tx) * (1-ty)
                w2 =    tx  * (1-ty)
                w3 = (1-tx) *    ty
                w4 =    tx  *    ty
                # print(f'w1-{w1},w2-{w2},w3-{w3},w4-{w4}')
                # 根据双线性插值公式计算第k个采样点的灰度值
                neighbor = img[i+y1,j+x1] * w1 + img[i+y2,j+x1] *w2 + img[i+y1,j+x2] *  w3 +img[i+y2,j+x2] *w4
                # print(f'neighbor-{neighbor},center-{center}')
                # LBP特征图像的每个邻居的LBP值累加，累加通过与操作完成，对应的LBP值通过移位取得
                # print(f'{(neighbor>center)  <<  (np.uint8)(neighbors-k-1)}')
                dst[i-radius,j-radius] |= (neighbor>center)  <<  (np.uint8)(neighbors-k-1)
    # 进行旋转不变处理
    for i in range(dst.shape[0]):
        for j in range(dst.shape[1]):
            currentValue = dst[i,j]
            minValue = currentValue
            for k in range(1, neighbors):
                # 对二进制编码进行循环左移，意思即选取移动过程中二进制码最小的那个作为最终值
                temp = (np.uint8)(currentValue>>(neighbors-k)) |  (np.uint8)(currentValue<<k)
                if temp < minValue:
                    minValue = temp
            dst[i,j] = minValue

    return dst
```
</details>


## 水平纹理提取
仅仅是一个想法，需要进一步确定。因为LBP能够获取45度方向上的变化信息。然而实际使用中，若时间序列的变量之间并不相关，其实就只需要水平方向的纹理，然后直接堆叠起来形成新的纹理。实际操作需要进一步确定，



---
<center>
<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/3.0/cn/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-nd/3.0/cn/88x31.png" /></a><br />本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/3.0/cn/">知识共享署名-非商业性使用-禁止演绎 3.0 中国大陆许可协议</a>进行许可。
</center>