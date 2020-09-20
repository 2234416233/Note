# 8、Canny边缘检测

## 原型 

OpenCV-Python中Canny函数的原型为：

```python
edge = cv2.Canny(image, threshold1, threshold2[, edges[, apertureSize[, L2gradient ]]]) 
```

必要参数：

- 第一个参数是需要处理的原图像，该图像必须为单通道的灰度图；
- 第二个参数是阈值1；
- 第三个参数是阈值2。

其中较大的阈值2用于检测图像中明显的边缘，但一般情况下检测的效果不会那么完美，边缘检测出来是断断续续的。所以这时候用较小的第一个阈值用于将这些间断的边缘连接起来。

可选参数中apertureSize就是Sobel算子的大小。而L2gradient参数是一个布尔值，如果为真，则使用更精确的L2范数进行计算（即两个方向的倒数的平方和再开放），否则使用L1范数（直接将两个方向导数的绝对值相加）。

具体的算法可参见清华大学出版社的《图像处理与计算机视觉算法及应用(第2版) 》第二章，其中有Canny算法的详细描述及实现。

函数返回一副二值图，其中包含检测出的边缘。

## 使用

Canny函数的使用很简单，只需指定最大和最小阈值即可。如下：

```python
#coding=utf-8
import cv2
import numpy as np  

img = cv2.imread("D:/lion.jpg", 0)
img = cv2.GaussianBlur(img,(3,3),0)
canny = cv2.Canny(img, 50, 150)
cv2.imshow('Canny', canny)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

首先，由于Canny只能处理灰度图，所以将读取的图像转成灰度图。

用高斯平滑处理原图像降噪。

调用Canny函数，指定最大和最小阈值，其中apertureSize默认为3。

处理结果如下：

![img](https://img-blog.csdn.net/20130630174008515?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VubnkyMDM4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## 更多

这个程序只是静态的，在github上有一个可以在运行时调整阈值大小的程序。其代码如下：

```python
import cv2
import numpy as np

def CannyThreshold(lowThreshold):
    detected_edges = cv2.GaussianBlur(gray,(3,3),0)
    detected_edges = cv2.Canny(detected_edges,lowThreshold,lowThreshold*ratio,apertureSize = kernel_size)
    cv2.imshow('canny demo',dst)
lowThreshold = 0
max_lowThreshold = 100
ratio = 3
kernel_size = 3
img = cv2.imread('D:/lion.jpg')
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
cv2.namedWindow('canny demo')
cv2.createTrackbar('Min threshold','canny demo',lowThreshold, max_lowThreshold, CannyThreshold)
CannyThreshold(0)  # initialization
if cv2.waitKey(0) == 27:
    cv2.destroyAllWindows()
```

原地址[在此](https://github.com/abidrahmank/OpenCV2-Python/blob/master/Official_Tutorial_Python_Codes/3_imgproc/canny.py)，其中还有其他的初级图像处理的代码，大伙可以去看看。后续文章将介绍更多的OpenCV的函数使用，以及视频的处理。