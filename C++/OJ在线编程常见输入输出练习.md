# OJ在线编程常见输入输出练习



## 4\. a+b(4)

输入描述：

> 输入数据包括多组。  
> 每组数据一行,每行的第一个整数为整数的个数n(1 <= n <= 100), n为0的时候结束输入。  
> 接下来n个正整数,即需要求和的每个正整数。

输出描述：

> 每组数据输出求和的结果

输入例子1:

> 4 1 2 3 4  
> 5 1 2 3 4 5  
> 0

输出例子1:

> 10  
> 15

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int n;
    vector<int> vec;
    while(cin>>n && n){       
        for(int i=0;i<n;i++){
            int a;
            cin>>a;
            vec.push_back(a);
        }
        cout<<accumulate(vec.begin(), vec.end(),0)<<endl;
        vec.clear();
    }
    return 0;
}
```

## 5\. a+b(5)

输入描述：

> 输入的第一行包括一个正整数t(1 <= t <= 100), 表示数据组数。  
> 接下来t行, 每行一组数据。  
> 每行的第一个整数为整数的个数n(1 <= n <= 100)。  
> 接下来n个正整数, 即需要求和的每个正整数。

输出描述：

> 每组数据输出求和的结果

输入例子1:

> 2  
> 4 1 2 3 4  
> 5 1 2 3 4 5

输出例子1:

> 10  
> 15

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int n;
    cin>>n;
    vector<int> vec;
    for(int j=0;j<n;j++){
        int m;
        cin>>m;
        for(int i=0;i<m;i++){
            int a;
            cin>>a;
            vec.push_back(a);
        }
        cout<<accumulate(vec.begin(), vec.end(),0)<<endl;
        vec.clear();
    }
    return 0;
}

```

## 6\. a+b(6)

输入描述：

> 输入数据有多组, 每行表示一组输入数据。  
> 每行的第一个整数为整数的个数n(1 <= n <= 100)。  
> 接下来n个正整数, 即需要求和的每个正整数。

输出描述：

> 每组数据输出求和的结果

输入例子1:

> 4 1 2 3 4  
> 5 1 2 3 4 5

输出例子1:

> 10  
> 15

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int n;
    vector<int> vec;
    while(cin>>n){
        for(int i=0;i<n;i++){
            int a;
            cin>>a;
            vec.push_back(a);
        }
        cout<<accumulate(vec.begin(), vec.end(),0)<<endl;
        vec.clear();
    }
    return 0;
}
```

## 7\. a+b(7)

输入描述：

> 输入数据有多组, 每行表示一组输入数据。  
> 每行不定有n个整数，空格隔开。(1 <= n <= 100)。

输出描述：

> 每组数据输出求和的结果

输入例子1:

> 1 2 3  
> 4 5  
> 0 0 0 0 0

输出例子1:

> 6  
> 9  
> 0

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    stringstream ss;
    string s;
    while(getline(cin,s)){
        int a,sum = 0;
        ss.clear();
        ss<<s;
        while(1){
            ss>>a;
            if ( ss.fail() ) break;
            sum+=a;
        }
        cout<<sum<<endl;
    }
    return 0;
}
```

## 8\. \[编程题\]字符串排序(1)

输入描述：

> 输入有两行，第一行n  
> 第二行是n个空格隔开的字符串

输出描述：

> 输出一行排序后的字符串，空格隔开，无结尾空格

输入例子1:

> 5  
> c d a bb e

输出例子1:

> a bb c d e

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int n;
    cin>>n;
    vector<string> vec;
    for(int i = 0;i < n;i++){
        string a;
        cin>>a;
        vec.push_back(a);
    }
    sort(vec.begin(),vec.end());
    for(int i = 0;i < n;i++){
        cout<<vec[i]<<" ";
    }
    return 0;
}

```

## 9\. \[编程题\]字符串排序(2)

输入描述：

> 多个测试用例，每个测试用例一行。  
> 每行通过空格隔开，有n个字符，n＜100

输出描述：

> 对于每组测试用例，输出一行排序过的字符串，每个字符串通过空格隔开

输入例子1:

> a c bb  
> f dddd  
> nowcoder

输出例子1:

> a bb c  
> dddd f  
> nowcoder

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    vector<string> vec;
    string s;
    while(cin>>s){
        vec.push_back(s);
        if(cin.get()=='\n'){
            sort(vec.begin(),vec.end());
            for(int i = 0;i < vec.size();i++)
                cout<<vec[i]<<" ";
            cout<<endl;
            vec.clear();
        }
    }
    return 0;
}
```

## 10\. \[编程题\]字符串排序(3)

输入描述：

> 多个测试用例，每个测试用例一行。  
> 每行通过,隔开，有n个字符，n＜100

输出描述：

> 对于每组用例输出一行排序后的字符串，用’,'隔开，无结尾空格

输入例子1:

> a,c,bb  
> f,dddd  
> nowcoder

输出例子1:

> a,bb,c  
> dddd,f  
> nowcoder

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    string s1,s2;
    vector<string> vec;
    while(cin>>s1){
        stringstream ss(s1); //s1初值
        while(getline(ss,s2,',')){
            vec.push_back(s2);
        }
        if(cin.get()=='\n'){
            sort(vec.begin(),vec.end());
            for(int i=0;i<vec.size()-1;i++) cout<<vec[i]<<",";
            cout<<vec[vec.size()-1]<<endl;       
            vec.clear();
        }
    }
    return 0;
}
```

## 11\. \[编程题\]自测本地通过提交为0

输入描述：

> 输入有多组测试用例，每组空格隔开两个整数

输出描述：

> 对于每组数据输出一行两个整数的和

输入例子1:

> 1 1

输出例子1:

> 2

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    long long a,b;
    while(cin>>a>>b) cout<<a+b<<endl;
    return 0;
}
```