## 关于Opencv 内存泄漏的一些总结

## 1、内存泄露

内存泄露是说没有释放已经不能使用的内存，这里一般指堆的内存才需要显示的释放。比如用malloc，calloc，realloc，new分配的内存是在堆上的，需要用free，delete显示的回收。内存泄露最明显的一是程序很慢，在运行程序时你可以启动任务管理器，会看到程序占用的内存一直“砰砰砰”的往上涨：最后直接崩溃，或者你关闭程序的时候也会异常退出

1）除了new的对象我们知道要delete。

OpenCV中使用cvCreateImage()新建一个IplImage*，以及使用cvCreateMat()新建一个CvMat*，都需要cvReleaseImage()  cvReleaseMat()显示的释放

```c++
IplImage* subImg=cvCreateImage( cvSize((img->width)*scale,(img->height)*scale), 8, 3 ); 
CvMat *tempMat=cvCreateMat((img->width)*scale,(maxFace->height)*scale,CV_MAKETYPE(image->depth,image->nChannels)); 
cvReleaseImage(&subImg); 
cvReleaseMat(&tempMat); 
```



```cpp
IplImage* subImg=cvCreateImage( cvSize((img->width)*scale,(img->height)*scale), 8, 3 );
CvMat *tempMat=cvCreateMat((img->width)*scale,(maxFace->height)*scale,CV_MAKETYPE(image->depth,image->nChannels));
cvReleaseImage(&subImg);
cvReleaseMat(&tempMat);123
```

另外一些函数要用到 CvSeq*来存放结果（通常这些都要用cvCreateMemStorage()事先分配一块内存CvMemStorage*），都要是释放掉相应的内存，这是很难找的。

比如从二值图像中寻找轮廓的函数cvFindContours():

```C++
CvMemStorage* m_storage=cvCreateMemStorage(0); 
CvSeq * m_contour=0; 
cvFindContours( img, m_storage, &m_contour, **sizeof**(CvContour), CV_RETR_LIST, CV_CHAIN_APPROX_SIMPLE, cvPoint(0,0)); 
//释放内存 
cvReleaseMemStorage(&m_storage); 
```

```cpp
CvMemStorage* m_storage=cvCreateMemStorage(0);
CvSeq * m_contour=0;
cvFindContours( img, m_storage, &m_contour, sizeof(CvContour), CV_RETR_LIST, CV_CHAIN_APPROX_SIMPLE, cvPoint(0,0));
//释放内存
cvReleaseMemStorage(&m_storage);1234
```

以及人脸识别中检测人脸的函数：

```cpp
CvMemStorage* m_storage=cvCreateMemStorage(0); 
CvHaarClassifierCascade* cascade = (CvHaarClassifierCascade*)cvLoad( cascade_name, 0, 0, 0 ); 
CvSeq* faces = cvHaarDetectObjects( img, cascade, m_storage,1.1, 2, 0,cvSize(30, 30) ); 
//释放内存 
<span style=”BACKGROUND-COLOR: #ff99ff”>cvReleaseMemStorage( &faces->storage); 
cvReleaseHaarClassifierCascade( &cascade );</span> 
```



```cpp
CvMemStorage* m_storage=cvCreateMemStorage(0);
CvHaarClassifierCascade* cascade = (CvHaarClassifierCascade*)cvLoad( cascade_name, 0, 0, 0 );
CvSeq* faces = cvHaarDetectObjects( img, cascade, m_storage,1.1, 2, 0,cvSize(30, 30) );
//释放内存
<span style="BACKGROUND-COLOR: #ff99ff">cvReleaseMemStorage( &faces->storage);
cvReleaseHaarClassifierCascade( &cascade );</span>12345
```

注意这里我们可以使用
cvReleaseMemStorage( &faces->storage);
来释放m_storate，也可以使用：
cvReleaseMemStorage(&m_storage); // 释放内存，这是等效的，但一定不要用两次！！

 

**2、关于cvvImage内存泄露**

 

opencv中的CvvImage类多用在MFC中，因为有Show()这个成员函数，在MFC显示图像比IplImage要方便很多。但是同时也有容易疏忽导致内存泄漏的地方。

CvvImage::CopyOf



```C++
void CvvImage::CopyOf(CvvImage& img, int desired_color);
void CvvImage::CopyOf(IplImage* img, int desired_color);
```

从img复制图像到当前的对象中。

img
要复制的图像。
desired_color
为复制后图像的通道数， 复制后图像的像素深度为8bit。

CopyOf()函数会开辟一个新的空间存放图像，不会自动释放。

因此**在使用CopyOf()函数后，必须要使用Destroy()函数手动释放内存**

**如：** 

```C++
**CvvImage cimg;
cimg.CopyOf(img,3);           //复制为CvvImage结构
cimg.DrawToHDC(hDC,&rect);  // 写到HDC中，大小为rect
cimg.Destroy();** 
```

 

**3、CDC是Windows绘图设备的基类
**

其他函数中要使用CDC类，需使用GetDC函数和ReleaseDC函数，实现申请CDC类得指针和释放CDC类的指针。

CDC的使用：OnDraw（CDC\* pDC），因此OnDraw中可以直接使用CDC类编写代码。

**如：**

```c++
CDC *pdc = GetDC();
ReleaseDC(pdc);
```

## ==================================================================

OpenCV中的内存泄露问题（有些按网上说的效果好像不起作用，内存还是在慢慢长）

1、网上说的 cvLoadImage函数：使用CvvImage类代替。并且使用CvvImage类的Load函数。（我测试后感觉没有什么改善）

使用过程大概如下：

//变量定义：

CvvImage pSrcImg;

IplImag *pSrcImgCopy ;//使用IplImag变量做个拷贝。毕竟IplImag 类处理方便。

//获取图像：

pSrcImg.Load(str);//str为Cstring类型的图像文件名
pSrcImgCopy = pSrcImg.GetImage();//拷贝出pSrcImg的图像数据。

//释放内存

pSrcImg变量不需要每次释放，因为每次Load时是覆盖以前的内存区域。pSrcImgCopy 同样。

不过在程序结束时要释放，以免产生内存泄露或者别人以为你忘了。

cvReleaseImage(&pSrcImgCopy );
pSrcImg.Destroy();

不过要正确释放pSrcImgCopy 时，声明时必须create下：

pSrcImgCopy = cvCreateImage(cvSize(IMGWIDHT,IMGHEIGHT),IPL_DEPTH_8U, 3);

//IMGWIDHT,IMGHEIGHT为图像宽和高。

cvCloneImage函数：用cvCopy函数代替。。（我测试后感觉没有什么改善）

cvCopy(pSrcImg,pImg,NULL);//代替 pImg = cvCloneImage(pSrcImg);

pImg初始化时必须分配空间，否则上述函数不能执行。

pImg = cvCreateImage(cvSize(IMGWIDHT,IMGHEIGHT),IPL_DEPTH_8U, 3);

 

**2. cvGetCols()、cvGetRows()**

  ……

  CvMat *srcMat = cvCreateMat(width, height, CV_8UC3); // 创建一个三通道无符号整数类型的矩阵
  cvGetCols(frame,srcMat,0,width);

  这里出现内存泄露，因为cvGetCols()、cvGetRows() 是为目标矩阵分配一块新的数据内存区域，如果目标矩阵的数据区域之前已经分配了内存，则会原始数据内存区域将变成内存碎片，造成内存泄露。解决方法如下：

​    CvMat *srcMat = cvCreateMat(width, height, CV_8UC3); // 创建一个三通道无符号整数类型的矩阵

​    cvReleaseData(srcMat);  // 先释放目标矩阵的数据区

​    cvGetCols(frame,srcMat,0,width);

  或者

​     cvCreateMatHeader(width, height, CV_8UC3);

​     cvGetCols(frame,srcMat,0,width);

 

  **3. IplImag\*、CvMat\*、CvHistogram\* 等结构体指针在使用后要释放。**

​     IplImage *frame= cvCreateImage(cvSize(width,height),8,1);

​     CvMat *srcMat = cvCreateMat(width, height, CV_8U);

​     ……

​     CvHistogram *hist = cvCreateHist(1,&histSize,CV_HIST_ARRAY,ranges,1);

​     ……

​     cvReleaseMat(&srcMat);
​     cvReleaseImage(&frame); 
​     cvReleaseHist(&hist);