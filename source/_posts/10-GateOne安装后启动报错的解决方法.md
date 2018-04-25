---
title: 10. GateOne安装后启动报错的解决方法
date: 2018-04-25 13:08:47
categories:
  - Linux
  - Softwares
tags:
  - GateOne
  - Web SSH
---

### 错误日志
```log
Traceback (most recent call last):
  File "/usr/local/bin/gateone", line 9, in <module>
    load_entry_point('gateone==1.2.0', 'console_scripts', 'gateone')()
  File "/usr/lib/python2.7/dist-packages/pkg_resources/__init__.py", line 542, in load_entry_point
    return get_distribution(dist).load_entry_point(group, name)
  File "/usr/lib/python2.7/dist-packages/pkg_resources/__init__.py", line 2569, in load_entry_point
    return ep.load()
  File "/usr/lib/python2.7/dist-packages/pkg_resources/__init__.py", line 2229, in load
    return self.resolve()
  File "/usr/lib/python2.7/dist-packages/pkg_resources/__init__.py", line 2235, in resolve
    module = __import__(self.module_name, fromlist=['__name__'], level=0)
  File "/usr/local/lib/python2.7/dist-packages/gateone-1.2.0-py2.7.egg/gateone/core/server.py", line 402, in <module>
    from gateone.auth.authorization import require, authenticated, policies
  File "/usr/local/lib/python2.7/dist-packages/gateone-1.2.0-py2.7.egg/gateone/auth/authorization.py", line 38, in <module>
    @memoize
  File "/usr/local/lib/python2.7/dist-packages/gateone-1.2.0-py2.7.egg/gateone/core/utils.py", line 290, in __init__
    MEMO = AutoExpireDict(timeout=timeout, interval="30s")
  File "/usr/local/lib/python2.7/dist-packages/gateone-1.2.0-py2.7.egg/gateone/core/utils.py", line 161, in __init__
    self.interval = kwargs.pop('interval')
  File "/usr/local/lib/python2.7/dist-packages/gateone-1.2.0-py2.7.egg/gateone/core/utils.py", line 211, in interval
    self._timeout_checker, value, io_loop=self.io_loop)
TypeError: __init__() got an unexpected keyword argument 'io_loop'
Exception AttributeError: "'AutoExpireDict' object has no attribute '_key_watcher'" in <bound method AutoExpireDict.__del__ of {}> ignored
```

<!-- more -->

### 产生原因
由于`tornado`最新版本与旧版本不兼容导致

### 解决方法
安装4.5版本
```shell
sudo pip install tornado==4.5.3
```
