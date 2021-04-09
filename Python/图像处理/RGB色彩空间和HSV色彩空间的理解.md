# RGB色彩空间和HSV色彩空间的理解

![img](https://csdnimg.cn/release/blogv2/dist/pc/img/original.png)

[两鬓已不能斑白](https://me.csdn.net/u010429424) 2017-08-02 15:40:10 ![img](https://csdnimg.cn/release/blogv2/dist/pc/img/articleReadEyes.png) 15421 ![img](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollectionActive.png) 已收藏 12

分类专栏： [OpenCV](https://blog.csdn.net/u010429424/category_6333860.html) [OpenCV学习笔记](https://blog.csdn.net/u010429424/category_9269260.html)

版权

## RGB色彩空间和HSV色彩空间的理解

**本文的结构如下：**
1、RGB色彩空间
2、HSV色彩空间（附HSV颜色分量范围表）
3、RGB到HSV的转换的Demo
  使用OpenCV实现RGB转HSV，并通过滑动条动态设定HSV阈值
  自己写程序，实现RGB转HSL



### 1、RGB色彩空间

RGB色彩空间源于使用阴极射线管的彩色电视，RGB分别代表三个基色（R-红色、G-绿色、B-蓝色），具体的色彩值由三个基色叠加而成。在图像处理中，我们往往使用向量表示色彩的值，如(0,0,0)表示黑色、(255, 255, 255)表示白色。其中，255表示色彩空间被量化成255个数，最高亮度值为255（255 = 2^8 - 1，即每个色彩通道用8位表示）。在这个色彩空间中，有256*256*256种颜色。RGB色彩空间如下图所示（图片来自百度百科）。是一个包含Red、Green、Blue的三维空间。

![这里写图片描述](https://img-blog.csdn.net/20170802152416064?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

> 注：
> 1、在OpenCV中，RGB色彩空间的顺序是BGR，千万不要弄错了
> 2、在Java的Bitmap中，RGB色彩空间被表示为ARGB，其中A代表透明度

### 2、HSV色彩空间

HSV色彩空间（Hue-色调、Saturation-饱和度、Value-值）将亮度从色彩中分解出来，在图像增强算法中用途很广。在我本人接触的图像处理项目中，经常将图像从RGB色彩空间转换到了HSV色彩空间，以便更好地感知图像颜色，利用HSV分量从图像中提取感兴趣的区域。

> HSV色彩空间也被称为HSB（色调、饱和度、亮度），在PS中常被用到。

HSV色彩空间如下图所示，用一个倒圆锥体表示整个色彩空间：

![这里写图片描述](https://img-blog.csdn.net/20170802152437782?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

> 注：
> 1、H的范围是[0，360），S和V的范围是[0，1]。但是在OpenCV 中，HSV好像被规范化到了[0, 255]，此处求大神指导。
> 2、另外，网上有人总结了HSV颜色对应RGB的分量范围，见下面的表格。参考自：
> http://www.cnblogs.com/wangyblzu/p/5710715.html
> http://blog.csdn.net/taily_duan/article/details/51506776

![这里写图片描述](https://img-blog.csdn.net/20170802162038699?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 3、RGB到HSV的转换的Demo

**(1) 使用OpenCV的cv2.cvtColor(src, cv2.COLOR_BGR2HSV)**

OpenCV为我们提供了现成的函数cvtColor()，帮助我们将图像从BGR转换到HSV。

```python
# -*- coding:utf-8 -*-

import cv2

"""
功能：读取一张图片，显示出来，并转化为HSV色彩空间
"""
image = cv2.imread('images/my_wife2.jpg') # 根据路径读取一张图片
cv2.imshow("BGR", image) # 显示图片


# 转化图片到HSV色彩空间
dst = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
cv2.imshow("HSV", dst) # 显示图片
cv2.waitKey(0) # 等待键盘触发事件，释放窗口123456789101112131415
```

结果如下：

![这里写图片描述](https://img-blog.csdn.net/20170802155042346?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

挺好看的美女，结果被弄成了这个样子。一些初学者包括我自己有时会问，为什么要把好端端的图片转成HSV色彩空间呢。其实这样做大有用处，比如我们要提取美女的头发区域，就可以通过设置HSV色彩空间的高低阈值来做。

```python
# -*- coding:utf-8 -*-

import cv2
import numpy as np   # ------------------改变1

"""
功能：读取一张图片，显示出来，并转化为HSV色彩空间
"""
image = cv2.imread('images/my_wife2.jpg') # 根据路径读取一张图片
cv2.imshow("BGR", image) # 显示图片


# 转化图片到HSV色彩空间
dst = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
cv2.imshow("HSV", dst) # 显示图片


# 根据HSV提取头发 --------------------------------改变2
low_hsv = np.array([0, 0, 46])
high_hsv = np.array([200, 40, 220])
dst = cv2.inRange(dst, low_hsv, high_hsv)
cv2.imshow("result", dst) # 显示图片

cv2.waitKey(0) # 等待键盘触发事件，释放窗口123456789101112131415161718192021222324
```

程序的运行效果如下。我们看到，头发区域显示为白色，这样我们就初步地提取出了头发区域。当然这个效果并不理想，是因为我们设定的阈值不好，如果能动态地设定阈值就好了。

![这里写图片描述](https://img-blog.csdn.net/20170802155906564?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

我们可以利用滑块动态地设置阈值，一边拖动滑块，一边观察图像。OpenCV提供了createTrackbar()，用于创建滑块。代码如下：

```
# -*- coding:utf-8 -*-

import cv2
import numpy as np

"""
功能：读取一张图片，显示出来，转化为HSV色彩空间
     并通过滑块调节HSV阈值，实时显示
"""

image = cv2.imread('images/my_wife2.jpg') # 根据路径读取一张图片
cv2.imshow("BGR", image) # 显示图片

hsv_low = np.array([0, 0, 0])
hsv_high = np.array([0, 0, 0])

# 下面几个函数，写得有点冗余

def h_low(value):
    hsv_low[0] = value

def h_high(value):
    hsv_high[0] = value

def s_low(value):
    hsv_low[1] = value

def s_high(value):
    hsv_high[1] = value

def v_low(value):
    hsv_low[2] = value

def v_high(value):
    hsv_high[2] = value

cv2.namedWindow('image')
cv2.createTrackbar('H low', 'image', 0, 255, h_low) 
cv2.createTrackbar('H high', 'image', 0, 255, h_high)
cv2.createTrackbar('S low', 'image', 0, 255, s_low)
cv2.createTrackbar('S high', 'image', 0, 255, s_high)
cv2.createTrackbar('V low', 'image', 0, 255, v_low)
cv2.createTrackbar('V high', 'image', 0, 255, v_high)

while True:
    dst = cv2.cvtColor(image, cv2.COLOR_BGR2HSV) # BGR转HSV
    dst = cv2.inRange(dst, hsv_low, hsv_high) # 通过HSV的高低阈值，提取图像部分区域
    cv2.imshow('dst', dst)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
cv2.destroyAllWindows()123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051
```

程序运行的效果如下，我们拉动滑块会自动改变HSV的高低阈值，进而根据高低阈值提取的图像区域也会改变：

![这里写图片描述](https://img-blog.csdn.net/20170802161355037?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**(2) 自己编写程序实现RGB转HSL**

> 根据维基百科，RGB转HSV的公式如下： 设 (r, g,b)分别是一个颜色的红、绿和蓝坐标，它们的值是在0到1之间的实数。设max等价于r,g和b中的最大者。设min等于这些值中的最小者。要找到在HSL空间中的 (h, s, l)值，这里的h ∈ [0,360）度是角度的色相角，而s, l ∈ [0,1]是饱和度和亮度，计算为：

![这里写图片描述](https://img-blog.csdn.net/20170802164323153?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

> （**如果要转化为HSV**）h的值通常规范化到位于0到360°之间。而h = 0用于max = min的（就是灰色）时候而不是留下h未定义。HSL和HSV有同样的色相定义，但是其他分量不同。HSV颜色的s和v的值定义如下：

![这里写图片描述](https://img-blog.csdn.net/20170802164330682?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

根据以上公式，我们可以写出RGB转HSV的代码：

```python
def rgb2hsv(r, g, b):
    r, g, b = r / 255.0, g / 255.0, b / 255.0
    mx = max(r, g, b)
    mn = min(r, g, b)
    diff = mx - mn

    # 计算H
    if mx == mn:
        h = 0
    elif mx == r:
        if g >= b:
            h = 60 * ((g - b) / diff) + 0
        else:
            h = 60 * ((g - b) / diff) + 360
    elif mx == g:
        h = 60 * ((b - r) / diff) + 120
    elif mx == b:
        h = 60 * ((r - g) / diff) + 240

    # 先计算L
    l = (mx + mn) / 2.0

    # 再计算S
    if mx == min:
        s = 0
    elif l > 0 and l <= 0.5:
        s = (diff / l) / 2.0
    elif l > 0.5:
        s = (diff / (1 - l)) / 2.0

    return h, s, l

# 测试
h,s,l = rgb2hsv(200, 221, 221)
print('h=%f s=%f l=%f' % (h, s, l))1234567891011121314151617181920212223242526272829303132333435
```

程序运行结果如下：
![这里写图片描述](https://img-blog.csdn.net/20170802171822095?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

可以根据[颜色代码表](http://www.360doc.com/content/12/0229/16/605353_190576827.shtml)，判断我们的结果是否正确

![这里写图片描述](https://img-blog.csdn.net/20170802171846359?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**附：**

1、 HSV转RGB的公式

> 类似的，给定在HSV中 (h, s,
> v)值定义的一个颜色，带有如上的h，和分别表示饱和度和明度的s和v变化于0到1之间，在RGB空间中对应的 (r, g,
> b)三原色可以计算为（R,G,B变化于0到1之间）：

![这里写图片描述](https://img-blog.csdn.net/20170802164620195?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

> 对于每个颜色向量 (r, g, b),

![这里写图片描述](https://img-blog.csdn.net/20170802164626801?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQyOTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（参考自维基百科）

2、RGB转GRAY的公式

请见：http://blog.csdn.net/xdrt81y/article/details/8289963

**总结：**
本文包括以下几个部分，
1、RGB和HSV色彩空间的理解
2、通过OpenCV实现了RGB到HSV的转换，包含一个使用滑动条的小例子
3、根据RGB转HSL的公式，自己编程实现了RGB转HSL的程序。