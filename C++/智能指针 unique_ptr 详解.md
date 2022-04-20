# 智能指针 unique\_ptr 详解



## 一、[智能指针](https://so.csdn.net/so/search?q=%E6%99%BA%E8%83%BD%E6%8C%87%E9%92%88&spm=1001.2101.3001.7020)

### 1.什么是智能指针

简单地说，C++智能指针是包含[重载运算符](https://so.csdn.net/so/search?q=%E9%87%8D%E8%BD%BD%E8%BF%90%E7%AE%97%E7%AC%A6&spm=1001.2101.3001.7020)的类，其行为像常规指针，但智能指针能够及时、妥善地销毁动态分配的数据，并实现了明确的对象生命周期，因此更有价值。

### 2.常规指针存在的问题

C++在内存分配、释放和管理方面向程序猿提供了全面的灵活性。但是这种灵活性是把双刃剑，一方面它使C++成为一种功能强大的语言，另一方面它让程序猿能够制造与内存相关的问题，比如内存泄漏。

例如在堆声明和分配的内存，析构方法是否会自动销毁对象，又或是方法结束后需要一个个释放，方法存在很多返回的地方，每个返回语句都要执行很多相关释放操作，十分繁琐，用智能指针就能实现其自动释放。

### 3.智能指针有何帮助

鉴于使用常规指针存在的问题，当C++程序猿需要管理堆中的数据时，可使用智能指针的方式分配和管理内存。

### 4.智能指针

智能指针类重载了解除引用运算符（\*）和成员选择运算符（->）。

同时为了能够在堆中管理各种类型，几乎所有的智能指针都是模板类，包含其功能的泛型实现。

## 二、unique\_ptr 简介

### 1.概念

unique\_ptr 是从 C++ 11 开始，定义在 <memory> 中的智能指针(smart pointer)。它持有对对象的独有权，即两个 unique\_ptr 不能指向一个对象，不能进行复制操作只能进行移动操作。

unique\_ptr 之所以叫这个名字，是因为它智能指向一个对象，即当它指向其他对象时，之前所指向的对象会被摧毁。其次，当 unique\_ptr 超出作用域时，指向的对象也会被自动摧毁，帮助程序猿实现了自动释放的功能。

unique\_ptr 也可能还未指向对象，这时的状态被称为 empty。

### 2.简单示例

```cpp
std::unique_ptr<int>p1(new int(5));
std::unique_ptr<int>p2=p1;// 编译会出错
std::unique_ptr<int>p3=std::move(p1);// 转移所有权, 现在那块内存归p3所有, p1成为无效的针.
p3.reset();//释放内存.
p1.reset();//无效
```

## 三、unique\_ptr 详解

### 1.定义

unique\_ptr 在 <memory> 中的定义如下：

```cpp
// non-specialized 
template <class T, class D = default_delete<T>> class unique_ptr;
// array specialization   
template <class T, class D> class unique_ptr<T[],D>;v 
```

其中 T 指其管理的对象类型，D 指该对象摧毁时所调用的释放方法，可以使用自定义的删除器，他也有一个默认的实现，即 detele 操作。

### 2.常用方法介绍

**2.1 构造方法 std::unique\_ptr::unique\_ptr**

```cpp
// unique_ptr constructor example
#include <iostream>
#include <memory> 

int main () {
    std::default_delete<int> d;  
    std::unique_ptr<int> u1;  
    std::unique_ptr<int> u2 (nullptr);  
    std::unique_ptr<int> u3 (new int);  
    std::unique_ptr<int> u4 (new int, d);  
    std::unique_ptr<int> u5 (new int, std::default_delete<int>());  
    std::unique_ptr<int> u6 (std::move(u5));  
    std::unique_ptr<int> u7 (std::move(u6));  
    
    std::unique_ptr<int> u8 (std::auto_ptr<int>(new int));   
    std::cout << "u1: " << (u1?"not null":"null") << '\n';  
    std::cout << "u2: " << (u2?"not null":"null") << '\n';  
    std::cout << "u3: " << (u3?"not null":"null") << '\n';  
    std::cout << "u4: " << (u4?"not null":"null") << '\n';  
    std::cout << "u5: " << (u5?"not null":"null") << '\n';  
    std::cout << "u6: " << (u6?"not null":"null") << '\n';  
    std::cout << "u7: " << (u7?"not null":"null") << '\n';  
    std::cout << "u8: " << (u8?"not null":"null") << '\n';   
    
    return 0;
}
```

执行结果为：

```cpp
u1: null
u2: null
u3: not null
u4: not null
u5: null
u6: null
u7: not null
u8: not null
```

**2.2 析构方法 std::unique\_ptr::~unique\_ptr**

```cpp
// unique_ptr destructor example
#include <iostream>
#include <memory> 

int main () { 
    // user-defined deleter  
    auto deleter = [](int*p){    
        delete p;    
        std::cout << "[deleter called]\n";  
    };  
    std::unique_ptr<int,decltype(deleter)> foo (new int,deleter);  
    std::cout << "foo " << (foo?"is not":"is") << " empty\n";  
    
    return 0; // [deleter called]
}
```

执行结果为：



```cpp
foo is not empty
[deleter called]
```

**2.3 释放方法 std::unique\_ptr::release**

注意！注意！注意！这里的释放并不会摧毁其指向的对象，而且将其指向的对象释放出去。

```cpp
// unique_ptr::release example
#include <iostream>
#include <memory> 

int main () {  
    std::unique_ptr<int> auto_pointer (new int); 
    int * manual_pointer;  
    *auto_pointer=10;  
    manual_pointer = auto_pointer.release();  
    // (auto_pointer is now empty)  
    std::cout << "manual_pointer points to " << *manual_pointer << '\n';  
    delete manual_pointer;  
    return 0;
}
```

执行结果为：

```cpp
manual_pointer points to 10
```

**2.4 重置方法 std::unique\_ptr::reset**

```cpp
// unique_ptr::reset example
#include <iostream>
#include <memory> i
nt main () {  
    std::unique_ptr<int> up;  // empty 
    up.reset (new int);       // takes ownership of pointer  
    *up=5;  
    std::cout << *up << '\n';  
    up.reset (new int);       // deletes managed object, acquires new pointer  
    *up=10;  
    std::cout << *up << '\n';  
    up.reset();               // deletes managed object  
    return 0;
}
```

执行结果为：

```
5
10
```

**2.5 交换方法 std::unique\_ptr::swap**

```cpp
// unique_ptr::swap example
#include <iostream>
#include <memory> 
int main () {
    std::unique_ptr<int> foo (new int(10));  
    std::unique_ptr<int> bar (new int(20));  
    foo.swap(bar);  
    std::cout << "foo: " << *foo << '\n';  
    std::cout << "bar: " << *bar << '\n';  
    return 0;
}
```

执行结果为：

```cpp
foo: 20
bar: 10
```

## 四、自定义删除器详解

### 1.为什么要使用自定义的删除器

当你的对象不能仅仅只是依靠 delete 删除时，那么你就需要依靠自定义的删除器了。例如对象中还有其它对象的数组等。

### 2.怎么写

主要就是自己编写一个删除器，指定输入的参数，然后实现相应的释放操作，并将这个删除器传入 unique\_ptr 中。

### 3.实例分析

**3.1 开发中经常会使用到图片，假设有这么一个图片的结构体定义如下**

```cpp
/// 图像格式定义
typedef struct cv_image_t {    
    unsigned char *data;            ///< 图像数据指针    
    cv_pixel_format pixel_format;   ///< 像素格式    
    int width;                      ///< 宽度(以像素为单位)    
    int height;                     ///< 高度(以像素为单位)    
    int stride;                     ///< 跨度, 即每行所占的字节数
} cv_image_t;
```

**3.2 再写一个图片的释放方法**

```cpp
void cv_image_release(cv_image_t* image) {    
    if(!image) {        
        return;    
    }    
    delete[] image->data;    
    delete image;    
    return;
}
```

**3.3 然后我们在代码中就可以使用一个如下的智能指针来智能的控制图片的释放操作了**

```cpp
cv_image_t image_input = {     
    (unsigned char*) image,    
    pixel_format,    
    image_width,    
    image_height,    
    image_stride
};
std::unique_ptr<cv_image_t, decltype(cv_image_release)*> image_guard (image_input ,cv_image_release);
```

**3.4 优化写法**

应用中很多地方都要使用图片，然后都需要这样一个图片的智能指针，不过每次都写这么一长串很麻烦，而且定义也都是一样的，那么我们就可以简单的封装一下，比如可以定义一个 cv\_image\_ptr 专门，如下：

```cpp
struct cv_image_destructor{    
    void operator()(cv_image_t* img){        
        cv_image_release(img);    
    }
}; 
typedef std::unique_ptr<cv_image_t, cv_image_destructor> cv_image_ptr;
```

这样我们再在代码中使用的时候就方便很多了，例如

```cpp
cv_image_ptr image_guard;
image_guard.reset(image_input);
```

而且代码看上去也整洁了很多。

关于 unique\_ptr 更官方和详细的说明，可以参考：[http://www.cplusplus.com/reference/memory/unique\_ptr/](http://www.cplusplus.com/reference/memory/unique_ptr/)