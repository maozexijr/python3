# PyCharm 配置 QtDesigner、PyUIC、PyRCC



### 1. 安装相关依赖 

```bash
$ pip install PyQt5
$ pip install pyqt5-tools
```



### 2. 配置QtDesigner

菜单栏 File → Settings → Tools → External Tools → +，如下配置

```bash
Name: QtDesigner
Program: C:/Python3/Lib/site-packages/pyqt5_tools/Qt/bin/qtdesigner.exe
Working directory: $ProjectFileDir$
```



![img](https://img-blog.csdnimg.cn/20200605120854784.png)



### 3. 使用QtDesigner

菜单栏 Tools → External Tools → QtDesigner，或者在.ui文件上右键菜单栏 External Tools 也可以



### 4. 处理启动报错



![img](https://img-blog.csdnimg.cn/20200604181504371.png)

将路径 *Python3/Lib/site-packages/pyqt5_tools/Qt/plugins* 下的 platforms 文件夹全部拷贝，覆盖 *Python3/Lib/site-packages/pyqt5_tools/Qt/bin* 中的 platforms



### 5. 汉化QtDesigner

QtDesigner是QtCreator的子程序，自身并不支持简体汉化，想要汉化只能从QtCreator的安装文件中查找

QtCreator的下载路径 https://download.qt.io/archive/qtcreator/ ，并安装

在安装路径下查找文件夹 qtcreator/share/qtcreator/translations 下的所有结尾 _zh_CN.qm的汉化包，拷贝覆盖到路径

Python3/Lib/site-packages/pyqt5_tools/Qt/bin/translations 下

![img](https://img-blog.csdnimg.cn/20200605112547212.png)



### 6. 配置PyUIC

同样的方法配置 PyUIC，参数如下

```bash
Name: PyUIC
Program: C:\Python3\python.exe
Arguments: -m PyQt5.uic.pyuic $FileName$ -o $FileNameWithoutExtension$.py
Working directory: $FileDir$
```



![img](https://img-blog.csdnimg.cn/20200605120452569.png)



### 7. 配置PyRCC

```bash
Name: PyRCC
Program: C:\Python3\Scripts\pyrcc5.exe
Arguments: $FileName$ -o $FileNameWithoutExtension$_rc.py
Working directory: $FileDir$
```



![img](https://img-blog.csdnimg.cn/20200605120134828.png)


