# Python各类图像库的图片读写方式总结



最近在研究深度学习视觉相关的东西，经常需要写python代码搭建深度学习模型。比如写CNN模型相关代码时，我们需要借助python图像库来读取图像并进行一系列的图像处理工作。我最常用的图像库当然是opencv，很强大很好用，但是也有一些坑，不注意的话也会搞出大麻烦。近期我也在看一些别人写的代码，因为个人习惯不一样，他们在做深度学习时用于图片读取的图像库各不相同，从opencv到PIL再到skimage等等各种库都有，有些库读进来的图片存储方式也不太一样，如果不好好总结这些主流图像读写库特点的话，以后看代码写代码都会遇坑无数。这篇文章就总结了以下主流Python图像库的一些基本使用方法和需要注意的地方：

1. opencv
2. PIL(pillow)
3. matplotlib.image
4. scipy.misc
5. skimage

# opencv: cv2.imread

opencv作为我最常用的图像处理库，当然第一个介绍，并且介绍得比较全面。毋庸置疑，opencv是今天介绍得所有图像库中最全面也最强大的库，如果我们只想掌握一个图像库，我觉得opencv库肯定是最适合不过了。

### 图片读取操作

```python
import cv2
import numpy as np
 
#读入图片：默认彩色图，cv2.IMREAD_GRAYSCALE灰度图，cv2.IMREAD_UNCHANGED包含alpha通道
img = cv2.imread('1.jpg')
cv2.imshow('src',img)
print(img.shape) # (h,w,c)
print(img.size) # 像素总数目
print(img.dtype)
print(img)
cv2.waitKey()
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162416597-1187071438.png)

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162428332-1552270406.png)

值得注意的是，opencv读进来的图片已经是一个numpy矩阵了，彩色图片维度是（高度，宽度，通道数）。数据类型是uint8。

```python
#gray = cv2.imread('1.jpg',cv2.IMREAD_GRAYSCALE) #灰度图
#cv2.imshow('gray',gray)
#也可以这么写，先读入彩色图，再转灰度图
src = cv2.imread('1.jpg')
gray = cv2.cvtColor(src,cv2.COLOR_BGR2GRAY)
cv2.imshow('gray',gray)
print(gray.shape)
print(gray.size)
print(gray)
cv2.waitKey()
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162441879-1940687059.png)

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162451551-496685557.png)

上面提到了两种获取灰度图的方式，读进来的灰度图的矩阵格式是（高度，宽度）。

```python
#注意，计算图片路径是错的，Opencv也不会提醒你，但print img时得到的结果是None
img2 = cv2.imread('2.jpg')
print(img2)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162505379-2006923778.png)

```python
#如何解决“读到的图片不存在的问题”？ #加入判断语句，如果为空，做异常处理
img2 = cv2.imread('2.jpg')
if img2 == None:
    print('fail to load image!')
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162514816-100569045.png)

### 图片矩阵变换

opencv读入图片的矩阵格式是：（height,width,channels）。而在深度学习中，因为要对不同通道应用卷积，所以会采取另一种方式：（channels,height,width）。为了应对该要求，我们可以这么做

```python
#注意到，opencv读入的图片的彩色图是一个channel last的三维矩阵（h,w,c），即（高度，宽度，通道）
#有时候在深度学习中用到的的图片矩阵形式可能是channel first，那我们可以这样转一下
print(img.shape)
img = img.transpose(2,0,1)
print(img.shape)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162526176-336774898.png)

在深度学习搭建CNN时，往往要做相应的图像数据处理，比如图像要扩展维度，比如扩展成（batch_size,channels,height,width）。

对于这种要求，我们可以这么做。

```python
#有时候还要扩展维度，比如有时候我们需要预测单张图片，要在要加一列做图片的个数，可以这么做
img = np.expand_dims(img, axis=0)
print(img.shape)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162537254-34582152.png)

上面提到的是预测阶段时预测单张图片的扩展维度的操作，如果是训练阶段，构建batch，即得到这种形式：（batch_size,channels,height,width）。我一般喜欢这么做

```python
data_list = [] 
loop:
    im = cv2.imread('xxx.png')
    data_list.append(im)
data_arr = np.array(data_list)
```

这样子就能构造成我们想要的形式了。

### 图片归一化

```python
#因为opencv读入的图片矩阵数值是0到255，有时我们需要对其进行归一化为0~1
img3 = cv2.imread('1.jpg')
img3 = img3.astype("float") / 255.0  #注意需要先转化数据类型为float
print(img3.dtype)
print(img3)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162551894-707266354.png)

### 存储图片

```python
#存储图片
cv2.imwrite('test1.jpg',img3) #得到的是全黑的图片，因为我们把它归一化了
#所以要得到可视化的图，需要先*255还原
img3 = img3 * 255
cv2.imwrite('test2.jpg',img3)  #这样就可以看到彩色原图了
```

### opencv大坑之BGR

opencv对于读进来的图片的通道排列是BGR，而不是主流的RGB！谨记！

```python
#opencv读入的矩阵是BGR，如果想转为RGB，可以这么转
img4 = cv2.imread('1.jpg')
img4 = cv2.cvtColor(img4,cv2.COLOR_BGR2RGB)
```

### 访问像素

```python
#访问像素
print(img4[10,10])  #3channels
print(gray[10,10]) #1channel
img4[10,10] = [255,255,255]
gray[10,10] = 255
print(img4[10,10])  #3channels
print(gray[10,10]) #1channel
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162605457-639220205.png)

### ROI操作

```python
#roi操作
roi = img4[200:550,100:450,:]
cv2.imshow('roi',roi)
cv2.waitKey()
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162617707-1094393133.png)

### 通道操作

```python
#分离通道
img5 = cv2.imread('1.jpg')
b,g,r = cv2.split(img5)
#合并通道
img5 = cv2.merge((b,g,r))
#也可以不拆分
img5[:,:,2] = 0  #将红色通道值全部设0
```

# PIL：PIL.Image.open

### 图片读取

```python
from PIL import Image
import numpy as np
```

PIL即Python Imaging Library，也即为我们所称的Pillow，是一个很流行的图像库，它比opencv更为轻巧，正因如此，它深受大众的喜爱。

### 图像读写

PIL读进来的图像是一个对象，而不是我们所熟知的numpy 矩阵。

```python
img = Image.open('1.jpg')
print(img.format) 
print(img.size) #注意，省略了通道 (w，h)
print(img.mode)  #L为灰度图，RGB为真彩色,RGBA为加了透明通道
img.show() # 显示图片
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162629754-688516019.png)

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162639988-1912686683.png)

灰度图的获取

```python
gray = Image.open('1.jpg').convert('L')
gray.show()
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162650613-1055743686.png)

```python
#读取不到图片会抛出异常IOError，我们可以捕捉它，做异常处理
try:
    img2 = Image.open('2.jpg')
except IOError:
    print('fail to load image!')
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162700816-1964050198.png)

```python
#pillow读进来的图片不是矩阵，我们将图片转矩阵,channel last
arr = np.array(img3)
print(arr.shape)
print(arr.dtype)
print(arr)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162712097-1627266466.png)

灰度图的转化与彩图转化一样

```python
arr_gray = np.array(gray)
print(arr_gray.shape)
print(arr_gray.dtype)
print(arr_gray)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162722801-446892141.png)

### 存储图片

```python
#矩阵再转为图像
new_im = Image.fromarray(arr)
new_im.save('3.png')
```

### 图像操作

```python
#分离合并通道

r, g, b = img.split()
img = Image.merge("RGB", (b, g, r))
img = img.copy() #复制图像
```

### ROI获取

```python
img3 = Image.open('1.jpg')
roi = img3.crop((0,0,300,300)) #(左上x，左上y，右下x，右下y)坐标
roi.show()
```

# matplotlib：matplotlib.image.imread

matplotlib是一个科学绘图神器，用的人非常多。

```python
import matplotlib.pyplot as plt
import numpy as np

image = plt.imread('1.jpg')
plt.imshow(image)
plt.show()
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162758504-112628206.png)

```python
#也可以关闭显示x，y轴上的数字

image = plt.imread('1.jpg')
plt.imshow(image)
plt.axis('off')
plt.show()
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162809410-23169087.png)

```python
#plt.imread读入的就是一个矩阵，跟opencv一样，但彩图读进的是RGB，与opencv有区别

print(image.shape) # (h,w,c)
print(image.size)
print(image.dtype) 
print(image)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162818301-2014383322.png)

```python
im_r = image[:,:,0] #红色通道
plt.imshow(im_r)
plt.show()
#此时会发现显示的是热量图，不是我们预想的灰度图，可以添加 cmap 参数解决
plt.imshow(im_r,cmap='Greys_r')
plt.show()
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162827957-1687928630.png)

```python
#与opencv结合使用
import cv2

im2 = cv2.imread('1.jpg')
plt.imshow(im2)
plt.axis('off')
plt.show()
#发现图像颜色怪怪的，原因当然是我们前面提到的RGB顺序不同的原因啦,转一下就好
im2 = cv2.cvtColor(im2,cv2.COLOR_BGR2RGB)
plt.imshow(im2)
plt.axis('off')
plt.show()
#所以无论用什么库读进图片，只要把图片改为矩阵，那么matplotlib就可以处理了
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162839097-782167147.png)

```python
#再试一试pillow和matplotlib结合
from PIL import Image

im3 = Image.open('1.jpg')
im3 = np.array(im3)
plt.figure(1)
plt.imshow(im3)
plt.axis('off')
#存储图像，注意，必须在show之前savefig，否则存储的图片一片空白
plt.savefig('timo.jpg')
plt.show()
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162850207-1091029005.png)

```python
#最后以一个综合例子总结matplotlib最基本的图片显示技巧吧
im_lol1 =  plt.imread('lol.jpg')
im_lol2 =  plt.imread('1.jpg')
figure = plt.figure(figsize=(20,10)) # 调整显示图片的大小
'''
figsize参数：指定绘图对象的宽度和高度，单位为英寸；dpi参数指定绘图对象的分辨率，
即每英寸多少个像素，缺省值为80。因此本例中所创建的图表窗口的宽度为8*80 = 640像素

'''
plt.axis("off")#不显示刻度 
ax = figure.add_subplot(121) # 图片以1行2列的形式显示
plt.axis('off')
ax.imshow(im_lol1) #第一张图
ax.set_title('lol image 1')#给图片加titile 
ax = figure.add_subplot(122) 
plt.axis('off')
ax.imshow(im_lol2) 
ax.set_title('lol image 2')#给图片加titile 
plt.savefig('twp.jpg')
plt.show()
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162902097-653779743.png)

# scipy.misc：scipy.misc.imread

```python
from scipy import misc
import matplotlib.pyplot as plt

im = misc.imread('1.jpg')
print(im.dtype)
print(im.size)
print(im.shape)
misc.imsave('misc1.png',im)
plt.imshow(im)
plt.show()
print(im)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162917176-371969416.png)

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162926707-895974117.png)

可以看到，有warining，提示我们imread和imsave在后来的版本将会被弃用，叫我们使用imageio.imread和imageio.imwrite。

我们根据她的提示，使用imageio模块进行图片读写，warning也就没有了。

```
import imageio
im2 = imageio.imread('1.jpg')
print(im2.dtype)
print(im2.size)
print(im2.shape)
plt.imshow(im)
plt.show()
print(im2)
imageio.imsave('imageio.png',im2)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162938926-321008588.png)

# skimage：skimage.io.imread

```
from skimage import io
im = io.imread('1.jpg')
print(im.shape) # numpy矩阵，(h,w,c)
print(im.dtype)
print(im.size)
io.imshow(im)
io.imsave('sk.png',im)
print(im)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112162950613-1562833314.png)

图像也是以numpy array形式读入。

灰度图的获取方式：

```python
im2 = io.imread('1.jpg',as_grey=True)  #读入灰度图
print(im2.dtype)
print(im2.size)
print(im2.shape)
io.imshow(im2)
io.imsave('sk_gray.png',im2)
io.show()
print(im2)
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112163001644-1622144466.png)

可以看到，灰度图像的矩阵的值被归一化了，注意注意！

也可以以这种方式获得灰度图：

```python
from skimage import color
im3 = io.imread('1.jpg')
im3 = color.rgb2grey(im3)
print(im3.dtype)
print(im3.size)
print(im3.shape)
io.imshow(im3)
io.show()

'''
skimage.color.rgb2grey(rgb)
skimage.color.rgb2hsv(rgb)
skimage.color.rgb2lab(rgb)
skimage.color.gray2rgb(image)
skimage.color.hsv2rgb(hsv)
skimage.color.lab2rgb(lab)
'''
```

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112163013301-1065514242.png)

# 总结

1. 除了opencv读入的彩色图片以BGR顺序存储外，其他所有图像库读入彩色图片都以RGB存储。
2. 除了PIL读入的图片是img类之外，其他库读进来的图片都是以numpy 矩阵。
3. 各大图像库的性能，老大哥当属opencv，无论是速度还是图片操作的全面性，都属于碾压的存在，毕竟他是一个巨大的cv专用库。下面那张图就是我从知乎盗来的一张关于各个主流图像库的一些性能比较图，从测试结果看来，opencv确实胜出太多了。

![img](https://images2017.cnblogs.com/blog/1093303/201801/1093303-20180112163030926-747111239.jpg)