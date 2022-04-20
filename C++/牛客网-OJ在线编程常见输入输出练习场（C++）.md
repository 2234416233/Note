# 牛客网-OJ在线编程常见输入输出练习场（C++）



**牛客网-OJ在线编程常见输入输出练习场（C++）**

主要分成数字和字符串输入输出两个部分，只选取几个有代表性的，详细可以去\[牛客网\](https://ac.nowcoder.com/acm/contest/5647?from=hr\_test#question) 。

# 1 数字输入输出

## 1.1 使用cin

G题：计算a+b

**输入描述:**

> 输入包括两个正整数a,b(1 <= a, b <= 10^9),输入数据包括多组。

**输出描述:**

> 输出a+b的结果

**输入例子1:**

> 1 5  
> 10 20

**输出例子1:**

> 6  
> 30

**代码：**

```c++
#include <iostream>
using namespace std;
int main() {
    int a, b;
    while(cin >> a >> b) {
        cout << a+b << endl;
    }
    return 0;
}
```

cin是不会读空格和回车的。其实K题和这个是一样的，就是int改成long。如果代码觉得逻辑没有错误可以改一下数据类型。还有些题某一行的第一个输入为下面会输入多少数字，这种行控制方法比较容易实现，就不再赘述。如果没有告诉的，使用cin.get()。

## 1.2 使用cin.get()

计算一系列数的和

**输入描述:**

> 输入数据有多组, 每行表示一组输入数据。
>
> 每行不定有n个整数，空格隔开。(1 <= n <= 100)。

**输出描述:**

> 每组数据输出求和的结果

**输入例子1:**

> 1 2 3  
> 4 5  
> 0 0 0 0 0

**输出例子1:**

> 6  
> 9  
> 0

**代码：**

```c++
#include <iostream>
using namespace std;
int main() {
    int num;
    int sum = 0;
    while(cin >> num) {
        sum += num;
        if(cin.get() == '\n') {
            cout << sum << endl;
            sum = 0;
        }
    }
    return 0;
}
```

这里使用使用cin.get()，可以只读一个字符，空白字符和换行符都可以读进去。

# 2 [字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)输入输出

## 2.1 使用cin

对输入的字符串进行排序后输出

**输入描述:**

> 输入有两行，第一行n
>
> 第二行是n个空格隔开的字符串

**输出描述:**

> 输出一行排序后的字符串，空格隔开，无结尾空格

**输入例子1:**

> 5  
> c d a bb e

**输出例子1:**

> a bb c d e

**代码：**

```c++
#include <iostream>
#include <algorithm>
#include <vector>
#include <string>

using namespace std;
int main() {
    int num;
    cin >> num;
    vector<string> strs(num);
    while(--num >= 0) {
        cin >> strs[num];
    }
    sort(strs.begin(), strs.end());
    num = strs.size() - 1;
    for(int i = 0; i < num; ++i) {
        cout <<strs[i] <<' ';
    }
    cout <<strs[num] <<'\n';

    return 0;
}
```

这里的换行控制因为告诉了多少就直接利用cin来做了，如果事先没有告诉要输入多少个，需要利用cin.get()。

## 2.2 利用cin.get()

对输入的字符串进行排序后输出

**输入描述:**

> 多个测试用例，每个测试用例一行。
>
> 每行通过空格隔开，有n个字符，n＜100

**输出描述:**

> 对于每组测试用例，输出一行排序过的字符串，每个字符串通过空格隔开

**输入例子1:**

> a c bb  
> f dddd  
> nowcoder

**输出例子1:**

> a bb c  
> dddd f  
> nowcoder

**代码：**

```c++
#include <iostream>
#include <algorithm>
#include <vector>
#include <string>

using namespace std;
int main() {
    string str;
    vector<string> strs;
    while(cin >> str) {
        strs.push_back(str);
        if(cin.get() == '\n') {
            sort(strs.begin(), strs.end());
            int num = strs.size() - 1;
            for(int i = 0; i < num; ++i) {
                cout <<strs[i] <<' ';
            }
            cout <<strs[num] <<'\n';
            strs.clear();
        }
    }
    return 0;
}
```

这里利用cin.get()对换行进行了判断，这里都是建立在分隔符为空格的情况下，如果分隔符为`,`，如果用cin.get()会比较麻烦，下面是用getline()和istringstream的示例。

## 2.3 用getline()和istringstream

对输入的字符串进行排序后输出

**输入描述:**

> 多个测试用例，每个测试用例一行。  
> 每行通过,隔开，有n个字符，n＜100

**输出描述:**

> 对于每组用例输出一行排序后的字符串，用’,'隔开，无结尾空格

**输入例子1:**

> a,c,bb  
> f,dddd  
> nowcoder

**输出例子1:**

> a,bb,c  
> dddd,f  
> nowcoder

**逆向思维**

```c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <sstream>
#include <string>

using namespace std;

int main()
{    
    string line;
    while (getline(cin, line)) {//while (cin >> line)
        istringstream sin(line);
        string str;
        vector<string> strs;
        while (getline(sin, str, ',')) {
            strs.push_back(str);
        }
        sort(strs.begin(),strs.end());
        int num = strs.size() - 1;
        for(int i = 0; i < num; ++i) {
            cout <<strs[i] <<',';
        }
        cout <<strs[num] <<'\n';
    }
    return 0;
}
```

getline()可以实现对流的自定义读取，使用istringstream将一个字符串变成了流，这样能被getline使用。