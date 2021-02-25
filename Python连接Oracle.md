
# Python连接Oracle数据库



### 1. 查看当前python版本

```bash
$ python
Python 3.7.9 (tags/v3.7.9:13c94747c7, Aug 17 2020, 18:58:18) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
```



### 2. 安装 cx-Oracle

不建议 PIP直接安装，下载页 *https://pypi.org/project/cx-Oracle/#files*

```bash
$ pip install cx_Oracle-8.1.0-cp37-cp37m-win_amd64.whl
```



### 3. 下载客户端

根据Oracle服务端版本 下载客户端软件，链接地址 *https://www.oracle.com/technetwork/cn/topics/winx64soft-101515-zhs.html*

instantclient-basic-windows.x64-11.2.0.4.0 .zip



### 4. 简单查询

```python
# !/usr/bin/python3
# coding: utf-8
import os
import sys
import traceback

import cx_Oracle

cwd = os.path.dirname(os.path.abspath(sys.argv[0]))

os.environ['NLS_LANG'] = 'SIMPLIFIED CHINESE_CHINA.UTF8'
os.environ['TNS_ADMIN'] = '%s/instantclient_11_2' % cwd

# 缓存
cache = dict()


def fetch(sql, way='fetchall', size=sys.maxsize):
    global cache
    if sql in cache:
        return cache[sql]

    connect = None
    cursor = None
    try:
        # 连接方式1
        # connect = cx_Oracle.connect(
        #     'user/pwd@(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=TCP)(HOST=host)(PORT=port)))(CONNECT_DATA=(SID=sid)))')

        # 连接方式2
        # connect = cx_Oracle.connect('user', 'pwd', 'host:port/sid')

        # 连接方式3
        connect = cx_Oracle.connect('user/pwd@host:port/sid')
        cursor = connect.cursor()
        cursor.execute(sql)
        if 'fetchone' == way:
            result = cursor.fetchone()
        elif 'fetchmany' == way:
            result = cursor.fetchmany(size)
        else:
            result = cursor.fetchall()

        cache[sql] = result
        return result
    except:
        traceback.print_exc()
    finally:
        if cursor:
            cursor.close()
        if connect:
            connect.commit()
            connect.close()


def fetchall(sql):
    return fetch(sql, 'fetchall')


def fetchmany(sql, size):
    return fetch(sql, 'fetchmany', size)


def fetchone(sql):
    return fetch(sql, 'fetchone')
```
