# pyqt中QWidget的show 一闪而过的原因及解决办法



在pyqt中new了一个QWidget，然后调用show()，本来是希望显示非模态对话框的，但是往往会一闪而过，就消失了，请看下面的代码： 

```python
        wgt_tmp=QWidget()
        wgt_tmp.setMinimumWidth(700)
        wgt_tmp.setMinimumSize(QSize(700,700))
        wgt_tmp.setStyleSheet("background-color: red;")
        wgt_tmp.show()
        wgt_tmp.destroyed.connect(self.print_destroyinfo)
```

原因是：wgt_tmp是个临时变量，所在的方法执行完毕后就destroyed了，如果要想不被销毁，则将wgt_tmp变成本类中的变量即可，即将wgt_tmp变成self.wgt_tmp即可，或者将wgt_tmp放到本类中的widget中也可，参考如下代码：

```python
        wgt_tmp=QWidget()
        wgt_tmp.setMinimumWidth(700)
        wgt_tmp.setMinimumSize(QSize(700,700))
        wgt_tmp.setStyleSheet("background-color: red;")
        self.layout().addWidget(self._widget)
        wgt_tmp.show()
        wgt_tmp.destroyed.connect(self.print_destroyinfo)
```

 