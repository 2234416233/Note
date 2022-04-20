# OJ在线编程常见输入输出技巧与示例



一直在[leetcode](https://so.csdn.net/so/search?q=leetcode&spm=1001.2101.3001.7020)上刷题，发现牛客上答题都需要自己解决输入问题。这里记一下遇到的一些方式。

纯记录，有点乱。

## 1.将一行按字符输入的数据转换成数据

## 下面这个注意输入是负数

```cpp
vector<int> s;	
string str;	
getline(cin, str);	
int temp = 0;	
for (int i = 0; i != str.size(); ++i) {
    if (isdigit(str[i])) {
        temp =temp* 10 + (str[i] - 48);
    }else {
        s.push_back(temp);
        temp = 0;
    }		
    if (i == (str.size() - 1)) {
        s.push_back(temp);
    }	
}
```

## 2.单独输入几个数

```cpp
cin>>n>>a>>b;
```

## 3.第一个数是数字，后面是连续字符，比如第一个输入是为了告诉你有几个数，后面是需要处理的字符串。

```cpp
char s[100500];
int n;
cin>>n>>s+1;
```

## 4.C语言连续多个输入的判断方式：

```cpp
while(~scanf("%d%d",&n,&m)){
    
}
//如果成功，该函数返回成功匹配和赋值的个数。如果到达文件末尾或发生读错误，则返回 EOF。
//所以~scanf表示 只要输入对了就是1.
```

## 5.C语言创建动态数组 输入两行，第一行为一个数组，数字之间用空格隔开，第二行表示数组的长度。

```cpp
#include <stdio.h>
#include<ctype.h>
int main() 
{    
    int N;	
    int num[10000];	
    int j = 0;	
    do {    //因为第一个是数字 第二个才是空格，所以用do 不然会丢失第一个数		
        scanf_s("%d", &num[j++]);	
    } while (getchar() != '\n'); 	
    scanf_s("%d", &N);	
    for (int i = 0; i < j; i++) {
        printf("%d ", num[i]);	
    }	
    return 0;
}
```

一般题目要输入一个长度为n的数组（n需要输入），因为其长度未知，我们常规方法是建立一个足够长的数组，如a\[100\],a\[10000000\]等。  
这样处理的缺点是会消耗大量不必要的内存。  
所以可以用动态分配内存的方式创建数组。

1\. malloc函数，原型：void \*malloc(unsigned int num\_bytes)。是申请一片空间，如int \*p = (int \*)malloc(1\*sizeof(int)),如果[编译器](https://so.csdn.net/so/search?q=%E7%BC%96%E8%AF%91%E5%99%A8&spm=1001.2101.3001.7020)默认int为4字节存储的话，那么计算结果是4Byte，一次申请一个4Byte的连续空间，并将空间基地址强制转换为int类型，赋值给指针p,此时申请的内存值是不确定的。

2\. calloc函数，原型：void \*calloc(size\_t n, size\_t size);   calloc函数会将申请的空间初始化为0。由于给每一个空间都要初始化值，那必然效率较malloc要低.

3.realloc函数，原型：void realloc(void \*ptr, size\_t new\_Size)；与上面两个有本质的区别， 用于对动态内存进行扩容(及已申请的动态空间不够使用，需要进行空间扩容操作)，ptr为指向原来空间基址的指针， new\_size为接下来需要扩充容量的大小。

如果size较小，原来申请的动态内存后面还有空余内存，系统将直接在原内存空间后面扩容，并返回原动态空间基地址；

如果size较大，原来申请的空间后面没有足够大的空间扩容，系统将**重新**申请一块new\_Size\*sizeof(int)的内存，并把原来空间的内容拷贝过去，原来空间free;

如果size较大，原来申请的空间后面没有足够大的空间扩容，系统将**重新**申请一块new\_Size\*sizeof(int)的内存，并把原来空间的内容拷贝过去，原来空间free;

注意：如果扩容后的内存空间较原空间小，将会出现数据丢失，如果直接realloc(p, 0);相当于free(p)

也就是说realloc相当于重新申请了一块new\_Size\*sizeof(int)大小的内存，根据实际情况决定是在原来基础上扩容还是换个基地址。所以申请后数组首地址可能发生改变。

```cpp
#include <stdio.h>
#include "malloc.h"
#include<ctype.h>

int main() 
{	
    int N;	
    int j = 0;	
    int *num = (int *)malloc(1 * sizeof(int)) //数组*stack,malloc(size)是申请一个空间 	
        do{		
            scanf_s("%d", &num[j++]);
            num = (int *)realloc(num, sizeof(int)*(j+1)); //realloc(address,size)向address上面追加size个空间 	
        } while (getchar() != '\n');	
    scanf_s("%d", &N);	
    for (int i = 0; i < j; i++) {		
        printf("%d ", num[i]);	
    }
    return 0;
}
```

# 6.OJ在线编程常见输入输出练习场

## 6.1 计算a+b(多组数据)

> 计算a+b
>
> 打开以下链接可以查看正确的代码
>
> **输入描述:**
>
> 输入包括两个正整数a,b(1 <= a, b <= 10^9),输入数据包括多组。
>
> **输出描述:**
>
> 输出a+b的结果
>
> **输入例子1:**
>
> 1 5
> 10 20
>
> **输出例子1:**
>
> 6
> 30

```cpp
#include <iostream>
using namespace std; 

int main()
{    
    int a;    
    int b;    
    while(cin>>a>>b) {        
        cout<<a+b<<endl;    
    }        
    return 0;
}
```

## 6.2 计算a+b(输入包括数组个数)

> 计算a+b
>
> **输入描述:**
>
> 输入第一行包括一个数据组数t(1 <= t <= 100)
> 接下来每行包括两个正整数a,b(1 <= a, b <= 10^9)
>
> **输出描述:**
>
> 输出a+b的结果
>
> **输入例子1:**
>
> 2
> 1 5
> 10 20
>
> **输出例子1:**
>
> 6
> 30

```cpp
#include<iostream> 
using namespace std; 
int main()
{    
    int t;    
    cin>>t;        
    int a,b;    
    while(t--) {        
        cin>>a>>b;        
        cout<<a+b<<endl;    
    }    
    return 0;
}
```

## 6.3 计算a+b(有结束标志)

> 计算a+b
>
> **输入描述:**
>
> 输入包括两个正整数a,b(1 <= a, b <= 10^9),输入数据有多组, 如果输入为0 0则结束输入
>
> **输出描述:**
>
> 输出a+b的结果
>
> **输入例子1:**
>
> 1 5
> 10 20
> 0 0
>
> **输出例子1:**
>
> 6
> 30

```cpp
#include<bits/stdc++.h>
using namespace std;

int main()
{    
    int a = 0, b = 0;    
    while(1){
        cin >> a >> b;        
        if(!(a || b)) break;        
        cout << a + b << endl;    
    }    
    return 0;
}
```

## 6.4 计算一系列数的和(有结束标志)

> 计算一系列数的和
>
> **输入描述:**
>
> 输入数据包括多组。
> 每组数据一行,每行的第一个整数为整数的个数n(1 <= n <= 100), n为0的时候结束输入。
> 接下来n个正整数,即需要求和的每个正整数。
>
> **输出描述:**
>
> 每组数据输出求和的结果
>
> **输入例子1:**
>
> 4 1 2 3 4
> 5 1 2 3 4 5
> 0
>
> **输出例子1:**
>
> 10
> 15

```cpp
#include<bits/stdc++.h>
using namespace std;

int main()
{    
    int input = 0, output = 0, N = 0;    
    while(1){        
        cin >> N;        
        if(N == 0) break;        
        output = 0;        
        while(N--){            
            cin >> input;            
            output += input;        
        }        
        cout << output << endl;    
    }    
    return 0;
}
```

## **6.5 计算一系列数的和**

> 计算一系列数的和
>
> **输入描述:**
>
> 输入的第一行包括一个正整数t(1 <= t <= 100), 表示数据组数。
> 接下来t行, 每行一组数据。
> 每行的第一个整数为整数的个数n(1 <= n <= 100)。
> 接下来n个正整数, 即需要求和的每个正整数。
>
> **输出描述:**
>
> 每组数据输出求和的结果
>
> **输入例子1:**
>
> 2
> 4 1 2 3 4
> 5 1 2 3 4 5
>
> **输出例子1:**
>
> 10
> 15

```cpp
#include<bits/stdc++.h>
using namespace std;
int main()
{    
    int N = 0, input = 0, output = 0, n = 0;    
    cin >> N;    
    while(N--){        
        cin >> n;        
        output = 0;        
        while(n--){            
            cin >> input;            
            output += input;        
        }        
        cout << output << endl;    
    }    
    return 0;
}
```

## 6.6 计算一系列数的和

> 计算一系列数的和
>
> **输入描述:**
>
> 输入数据有多组, 每行表示一组输入数据。
> 每行的第一个整数为整数的个数n(1 <= n <= 100)。
> 接下来n个正整数, 即需要求和的每个正整数。
>
> **输出描述:**
>
> 每组数据输出求和的结果
>
> **输入例子1:**
>
> 4 1 2 3 4
> 5 1 2 3 4 5
>
> **输出例子1:**
>
> 10
> 15

```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){    
    int N = 0, input = 0, output = 0;    
    while(cin >> N){       
        output = 0;        
        while(N--){            
            cin >> input;            
            output += input;        
        }        
        cout << output << endl;    
    }    
    return 0;
}
```

## 6.7 计算一系列数的和

> 计算一系列数的和
>
> **输入描述:**
>
> 输入数据有多组, 每行表示一组输入数据。
>
> 每行不定有n个整数，空格隔开。(1 <= n <= 100)。
>
> **输出描述:**
>
> 每组数据输出求和的结果
>
> **输入例子1:**
>
> 1 2 3
> 4 5
> 0 0 0 0 0
>
> **输出例子1:**
>
> 6
> 9
> 0

```cpp
#include<bits/stdc++.h>
using namespace std;

int main()
{    
    int input = 0, output = 0;    
    while(cin >> input){        
        output += input;        
        if(getchar() == '\n'){            
            cout << output << endl;            
            output = 0;        
        }    
    }    
    return 0;
}
```

## 6.8 对输入的字符串进行排序后输出

> 对输入的字符串进行排序后输出
>
> **输入描述:**
>
> 输入有两行，第一行n
>
> 第二行是n个空格隔开的字符串
>
> **输出描述:**
>
> 输出一行排序后的字符串，空格隔开，无结尾空格
>
> **输入例子1:**
>
> 5
> c d a bb e
>
> **输出例子1:**
>
> a bb c d e

```cpp
#include<bits/stdc++.h>
using namespace std;

int main()
{    
    int N = 0;    
    string str;    
    vector<string> res;    
    cin >> N;    
    while(N--){        
        cin >> str;        
        res.push_back(str);    
    }    
    sort(res.begin(), res.end());    
    for(auto& s : res)        
        cout << s << " ";    
    cout << endl;    
    return 0;
}
```

## 6.9 对输入的字符串进行排序后输出

> 对输入的字符串进行排序后输出
>
> **输入描述:**
>
> 多个测试用例，每个测试用例一行。
>
> 每行通过空格隔开，有n个字符，n＜100
>
> **输出描述:**
>
> 对于每组测试用例，输出一行排序过的字符串，每个字符串通过空格隔开
>
> **输入例子1:**
>
> a c bb
> f dddd
> nowcoder
>
> **输出例子1:**
>
> a bb c
> dddd f
> nowcoder

```cpp
#include<bits/stdc++.h>
using namespace std;

int main()
{    
    string str;    
    vector<string> res;    
    while(cin >> str){        
        res.push_back(str);        
        if(getchar() == '\n'){            
            sort(res.begin(), res.end());            
            for(auto& s:res)                
                cout << s << " ";            
            cout << endl;            
            vector<string>().swap(res);        
        }    
    }    
    return 0;
}
```

## 6.10 对输入的字符串进行排序后输出

> 对输入的字符串进行排序后输出
>
> **输入描述:**
>
> 多个测试用例，每个测试用例一行。
> 每行通过,隔开，有n个字符，n＜100
>
> **输出描述:**
>
> 对于每组用例输出一行排序后的字符串，用','隔开，无结尾空格
>
> **输入例子1:**
>
> a,c,bb
> f,dddd
> nowcoder
>
> **输出例子1:**
>
> a,bb,c
> dddd,f
> nowcoder

```cpp
#include<bits/stdc++.h>
using namespace std;

int main()
{   	
    vector<string> res;	
    string input, str;	
    while (getline(cin, input)) {
        for (int i = 0; i < input.size(); i++) {			
            if (input[i] == ',') {				
                res.push_back(str);				
                string().swap(str);			
            }			
            else				
                str += input[i];		
        }		
        if (!str.empty()) {			
            res.push_back(str);			
            string().swap(str);		
        }			
        sort(res.begin(), res.end());			
        for (int i = 0; i < res.size(); i++) {				
            if (i != res.size() - 1)					
                cout << res[i] << ",";				
            else					
                cout << res[i] << endl;			
        }			
        vector<string>().swap(res);		
    }    
    return 0;
}
```

## 6.11 输出每组数的和

> **输入描述:**
>
> 输入有多组测试用例，每组空格隔开两个整数
>
> **输出描述:**
>
> 对于每组数据输出一行两个整数的和
>
> **输入例子1:**
>
> 1 1
>
> **输出例子1:**
>
> 2

```cpp
#include <iostream>
using namespace std;

int main()
{    
    long a,b;   
    while(cin>>a>>b){        
        cout<<a+b<<endl;    
    }    
    return 0;
}
```