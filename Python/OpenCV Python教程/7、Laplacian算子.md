# 7、Laplacian算子

## Laplacian算子

图像中的边缘区域，像素值会发生“跳跃”，对这些像素求导，在其一阶导数在边缘位置为极值，这就是Sobel算子使用的原理——极值处就是边缘。如下图（**下图来自OpenCV官方文档**）：

![img](https://img-blog.csdn.net/20130628171706750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VubnkyMDM4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

如果对像素值求二阶导数，会发现边缘处的导数值为0。如下（**下图来自OpenCV官方文档**）：

![img](https://img-blog.csdn.net/20130628171743593?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VubnkyMDM4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

Laplace函数实现的方法是先用Sobel 算子计算二阶x和y导数，再求和：（CSDN，你打水印，让我的公式怎么办？）

![img](https://img-blog.csdn.net/20130628165100984?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VubnkyMDM4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## 函数原型

在OpenCV-Python中，Laplace算子的函数原型如下：

```python
dst = cv2.Laplacian(src, ddepth[, dst[, ksize[, scale[, delta[, borderType]]]]])
```

如果看了上一篇Sobel算子的介绍，这里的参数应该不难理解。



前两个是必须的参数：

- 第一个参数是需要处理的图像；
- 第二个参数是图像的深度，-1表示采用的是与原图像相同的深度。目标图像的深度必须大于等于原图像的深度；

其后是可选的参数：

- dst不用解释了；
- ksize是算子的大小，必须为1、3、5、7。默认为1。
- scale是缩放导数的比例常数，默认情况下没有伸缩系数；
- delta是一个可选的增量，将会加到最终的dst中，同样，默认情况下没有额外的值加到dst中；
- borderType是判断图像边界的模式。这个参数默认值为cv2.BORDER_DEFAULT。

## 使用

这里还是以Sobel一文中的石狮作为测试图像，下面是测试代码：

```python
#coding=utf-8
import cv2
import numpy as np  

img = cv2.imread("D:/lion.jpg", 0)
gray_lap = cv2.Laplacian(img,cv2.CV_16S,ksize = 3)
dst = cv2.convertScaleAbs(gray_lap)
cv2.imshow('laplacian',dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

为了让结果更清晰，这里的ksize设为3，效果图如下：

![img](https://img-blog.csdn.net/20130628170607187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VubnkyMDM4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

有点像粉笔画，是吧。这是因为原图像未经过去噪就直接处理了。可以通过[滤波一文](http://blog.csdn.net/sunny2038/article/details/9155893)中，使用低通滤波一节中高斯模糊来先处理一下再用拉普拉斯函数。