# pyqt5-实时刷新页面（QApplication.processEvents()）



对于执行很耗时的程序来说，由于PyQt需要等待程序执行完毕才能进行下一步，这个过程表现在界面上就是卡顿，而如果需要执行这个耗时程序时不断的刷新界面。那么就可以使用QApplication.processEvents()，那么就可以一边执行耗时程序，一边刷新界面的功能，给人的感觉就是程序运行很流畅，因此QApplicationEvents（）的使用方法就是，在主函数执行耗时操作的地方，加入QApplication.processEvents()

1. ```
    1 import sys,time
    2 from PyQt5.QtWidgets import QWidget,QPushButton,QApplication,QListWidget,QGridLayout
    3 
    4 class WinForm(QWidget):
    5     def __init__(self,parent=None):
    6         super(WinForm, self).__init__(parent)
    7         #设置标题与布局方式
    8         self.setWindowTitle('实时刷新界面的例子')
    9         layout=QGridLayout()
   10 
   11         #实例化列表控件与按钮控件
   12         self.listFile=QListWidget()
   13         self.btnStart=QPushButton('开始')
   14 
   15         #添加到布局中指定位置
   16         layout.addWidget(self.listFile,0,0,1,2)
   17         layout.addWidget(self.btnStart,1,1)
   18 
   19         #按钮的点击信号触发自定义的函数
   20         self.btnStart.clicked.connect(self.slotAdd)
   21         self.setLayout(layout)
   22     def slotAdd(self):
   23         for n in range(10):
   24             #获取条目文本
   25             str_n='File index{0}'.format(n)
   26             #添加文本到列表控件中
   27             self.listFile.addItem(str_n)
   28             #实时刷新界面
   29             QApplication.processEvents()
   30             #睡眠一秒
   31             time.sleep(1)
   32 if __name__ == '__main__':
   33     app=QApplication(sys.argv)
   34     win=WinForm()
   35     win.show()
   36     sys.exit(app.exec_())
   ```