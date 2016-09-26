+++
Categories = ["Development", "Python"]
Description = ""
Tags = ["Development", "Python"]
date = "2016-08-15T22:49:10+08:00"
menu = ""
title = "How To Install Scikit-learn Under OSX 10.11"

+++

# 如何在osx下安装正确安装 scikit-learn

### 使用 Anaconda 

`Anaconda` 是一个 Python 的集成科学计算环境，一键安装，方便好用。但是当我安装之后发现其只有自带了：

- numpy
- scipy

而并没有 `scikit-learn`，然后当我使用 

```shell
conda install scikit-learn
```
进行安装之后发现还是无法使用这个缺省的库，经我多方查找资料，原来是 `Mac 10.11` 版本中自带的 `Python 2.7` 环境与 `Anaconda` 的环境是分离的，所以只能 `Anaconda` 中使用这些库。

然而这是我不太喜欢的一种方式，所以我放弃了，转而研究在 `Mac` 自带的 Python 环境中安装。

### 解除 OSX 10.11 的 ｀SIP｀ 限制

当我开始用 `pip` 安装第三方包的时候出现了如下的错误：

```shell
Collecting numpy

Using cached numpy-1.10.2-cp27-none-macosx_10_6_intel.macosx_10_9_intel.macosx_10_9_x86_64.macosx_10_10_intel.macosx_10_10_x86_64.whlInstalling
 collected packages: numpy
 Found existing installation: numpy 1.8.0rc1
 DEPRECATION: Uninstalling a distutils installed project (numpy) has been deprecated and will be removed in a future version. This is due to the fact that uninstalling a distutils project will only partially uninstall the project.
 Uninstalling numpy-1.8.0rc1:Exception:Traceback
 (most recent call last):
 File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/basecommand.py", line 211, in main
 status = self.run(options, args)
 File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/commands/install.py", line 311, in run
 root=options.root_path,
 File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/req/req_set.py", line 640, in install
 requirement.uninstall(auto_confirm=True)
 File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/req/req_install.py", line 716, in uninstall
 paths_to_remove.remove(auto_confirm)
 File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/req/req_uninstall.py", line 125, in remove
 renames(path, new_path)
 File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/utils/__init__.py",
 line 315, in renames
shutil.move(old, new)
 File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 302, in move
 copy2(src, real_dst)
 File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 131, in copy2
 copystat(src, dst)
 File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 103, in copystat
 os.chflags(dst, st.st_flags)OSError:
 [Errno 1] Operation not permitted: '/var/folders/5n/vbm997m56xg3kw67y6bccn2m0000gn/T/pip-4tcBsd-uninstall/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/numpy-1.8.0rc1-py2.7.egg-info'
```

仔细一看，发现了 `Operation not permitted` 的错误。

经过 Google ，发现了原来是 Apple 经历了 `XCode编译器注入` 事件之后，提升了 `Mac OS X El Capitan系统` 的安全保护机制，加入了：

> System Integrity Protection (SIP)
—— 系统完整性保护，其作用为强制性地保护系统相关的文件夹，开发者不能直接操作相关的文件内容。

而 Python 库所在的路径为：

```shell
/System/Library/Frameworks/Python.framework/Versions/2.7/...
```

当然是属于其完整性保护的保护伞之下的，所以我需要把 `SIP` 关掉之后才能对其进行安装。

引用外国大牛的关闭 SIP 的方法如下：

1. Click the  menu.
2. Select `Restart` …
3. Hold down `command-R` to boot into the Recovery System.
4. Click the `Utilities menu` and select `Terminal`.
5. Type `csrutil disable` and press `return` .
6. Close the `Terminal` app.
7. Click the  menu and select `Restart` … .

当然在安装完成之后最好还是将 SIP 重新打开：

1. Click the  menu.
2. Select `Restart` …
3. Hold down `command-R` to boot into the Recovery System.
4. Click the `Utilities menu` and select `Terminal`.
5. Type `csrutil enable` and press `return` .
6. Close the `Terminal` app.
7. Click the  menu and select `Restart` … .

### 安装 scikit-learn 的正确姿势

好吧，终于解决了上面这些问题了，安装应该没有问题了吧？很可惜，当我运行完：

```shell
$ sudo pip install numpy
$ sudo pip install scipy
$ sudo pip install scikit-learn
```

然后在 `Python` 下 `import sklearn` 时发生了如下错误：

```shell
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
File "/Library/Python/2.7/site-packages/sklearn/__init__.py", line 57, in <module>
from .base import clone
File "/Library/Python/2.7/site-packages/sklearn/base.py", line 11, in <module>
from .utils.fixes import signature
File "/Library/Python/2.7/site-packages/sklearn/utils/__init__.py", line 10, in <module>
from .murmurhash import murmurhash3_32
File "numpy.pxd", line 155, in init sklearn.utils.murmurhash (sklearn/utils/murmurhash.c:5029)
ValueError: numpy.dtype has the wrong size, try recompiling
```

好惨，经过多方搜索，绝大部分的解答都是：

```shell
$ pip uninstall numpy scipy scikit-learn
$ pip install numpy scipy scikit-learn
```

然而都并没有什么卵用，最后在这里找到了解决办法 => [here](http://scikit-learn-general.narkive.com/kMA6mRCk/valueerror-numpy-dtype-has-the-wrong-size-try-recompiling)

其方法时不要使用 `pip install scikit-learn`

而是到 Github 中找到 [scikit-learn](https://github.com/scikit-learn/scikit-learn) 项目进行安装：

```shell
$ git clone https://github.com/scikit-learn/scikit-learn.git
$ sudo python setup.py install
```

等待几分钟，安装完成，大功告成～

### 参考网站：
+ 在 osx 10.11 下解除 pip 权限安装 => [参考地址](http://blog.csdn.net/shi_weihappy/article/details/50938486)
+ 如何正确安装scikit-learn => [参考地址](http://www.tuicool.com/articles/aEJriuY)