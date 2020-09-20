# Opencv-Python：图像尺寸、图像的读取、显示、保存与复制

在使用opencv的方法时，首先必须导入opencv包。新的opencv导入cv2，这里也和cv做了一个对比

```python
import cv2
```

***\*一、图像尺寸\****

图像的大小可以通过其shape属性来获取，shape返回的是一个tuple元组，第一个元素表示图像的高度，第二个表示图像的宽度，第三个表示像素的通道数。

示例：

```python
if __name__ == '__main__':
    dirfile = 'dataset/data/TrainData/001.bmp'
    img = cv2.imread(dirfile)
    size = img.shape
    print size
```

运行结果：（800,645,3）

在cv中，是通过size = cv2.GetSize(i)的GetSize（）函数来获取的

***\*二、读取图像\****

在python中不需要声明变量，知道图像的具体位置就可以通过imread（）直接读取；目前opencv支持读取bmp、jpg、png等常用的一些格式，更详细的内容请参考opencv的参考文档。读取：



```python
image = cv2.imread('F:/001.nmp')
```

cv对应的方法是grey_image = cv2.CreateImage(size, 8, 1)

***\*三、显示图像\****

首先创建一个新的窗口用于显示图像：

```python
cv2.namedWindow('showimage')
```

cv对应的方法是cv.NamedWindow("Shape Model", cv.CV_WINDOW_AUTOSIZE)



然后在窗口中显示图像：



```python
cv2.imshow("Image",i)
cv2.waitKey(0)
```

如果不加cv2.waitKey(0)，你们在IDLE中的执行窗口直接无响应，在命令行中执行的时候就会一闪而过。最后一句加上cv2.destroyALLWindows（）的话，就能释放窗口了。

cv中cv.ShowImage("Image",i)显示图像，cv.WaitKey()

***\*四、保存图像\****

```python
cv2.imwrite(F:/images',image,[int(cv2.IMWRITE_JPEG_QUALITY),5])三个参数分别对应保存的路径及文件名、图像矩阵、指定格式（对于JPEG，其表示的是图像的质量，
用0-100的整数表示，默认为95。 注意，cv2.IMWRITE_JPEG_QUALITY类型为Long，必须转换成int；对于PNG，第三个参数表示的是压缩级别。cv2.IMWRITE_PNG_COMPRESSION，
从0到9,压缩级别越高，图像尺寸越小。这个是可选参数）
```

***\*
\****

***\*五、复制图像\****

在原来的opencv中可以直接使用cv.CreateImage（）创建图像，但cv2就需要使用numpy的函数了

```python
import numpy as np
image = np.zeros(img.shape, np.uint8)
```

图像使用numpy数组的属性来表示图像的尺寸和提通道信息。

当然也可以直接复制原来的图像到一张新的图像上：

```python
image = img.copy()
```

也可以用cvtColor获取原图像的副本：

```python
image = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
```