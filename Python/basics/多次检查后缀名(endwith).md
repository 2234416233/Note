# 多次检查后缀名(endwith)



在通过后缀名查找类型文件的时候, 多次使用endwith, 使用元组(tuple), 简化操作.
此类方式, 也可以应用于if语句多次相似检测.

------

代码

```python
# 列出文件夹内所有代码
def list_dictionary_codes(root_dir):
    paths_list = []
    for parent, dirNames, fileNames in os.walk(root_dir):
        for name in fileNames:
            ext = ['.h', '.m', '.xib', '.json', '.c', '.cpp', '.mm', '.md']
            if name.endswith(tuple(ext)):
      
    paths_list.append(os.path.join(parent, name))
    return paths_list123456789
```

