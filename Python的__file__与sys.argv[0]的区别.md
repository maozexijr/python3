# Python __file__与sys.argv[0]的区别



```python
# !/usr/bin/python3
# coding: utf-8

import os
import sys

print(os.path.abspath(sys.argv[0]))
print(os.path.abspath(__file__))

# 文件demo.py 仅放在 d:/temp 文件夹下
```

![img](https://img-blog.csdnimg.cn/20201104094912783.bmp)
