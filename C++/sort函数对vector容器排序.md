# C++自带sort函数对vector容器元素进行排序

```c++
1.包含头文件 #include<algorithm>，然后using namespace std;
2.假如你定义的vector变量为vector<Type> num，则如下：
  sort(num.begin(), num.end(), sortFun);
  然后如果是基本类型假如是int,第三个参数可以使用系统自带的less<int>()或者greater<int>()，假如是自定义类型话或者复杂类型就需自己定义比较规则函数sortFun,以下以opencv中的Point2d类型举例：
 
#include<iostream>
#include<algorithm>
#include<vector>
#include<opencv2\opencv.hpp>
using namespace std;
using namespace cv;
vector<cv::Point2d> po;
 
//自定义排序函数  
bool sortFun(const cv::Point2d &p1, const cv::Point2d &p2)
{
	return p1.x < p2.x;//升序排列  
}
 
int main()
{
	Point2d p1(2, 4), p2(4, 3), p3(1, 7), p4(0,4);
	po.push_back(p1);
	po.push_back(p2);
	po.push_back(p3);
	po.push_back(p4);
	cout << "排序前： ";
	for (auto elem : po)
		cout << elem << " ";
 
	sort(po.begin(), po.end(), sortFun);
	cout << endl << "排序后： " ;
	for (auto elem : po)
		cout << elem << " ";
	
	cout << endl;
	system("pause");
	return 0;
}
```



<img src="https://gcore.jsdelivr.net/gh/2234416233/myImage/img/2018072516412341.png" style="zoom:200%;" />