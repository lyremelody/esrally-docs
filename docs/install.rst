安装
------------

这是Rally的详细安装指南。如果你赶时间，你可以去看看 :doc:`快速入门 </quickstart>` 。

先决条件
~~~~~~~~~~~~~

在开始安装Rally前，请确定已经安装下面这些包：

* Python 3.4 或者 `python3` （通过 ``python3 --version`` 验证，应该打印 ``Python 3.4.0`` 或更高版本 ）
* ``pip3`` 在PATH中（通过 ``pip3 --version`` 验证）
* JDK 8
* git 1.9 或更高版本

Rally不支持Windows，只在Mac OS X和Linux上测试过。

.. note::

   如果你使用RHEL，请通过 `Red Hat Software Collections <https://www.softwarecollections.org/en/scls/rhscl/git19/>`_ 确定安装了最新的git。


安装Rally
~~~~~~~~~~~~~~~~

使用pip进行安装，很简单：``pip3 install esrally``

.. note::

   根据你的系统对于软件安装的要求，你可能会要使用 ``sudo``

如果在安装过程中出错，可能是在安装 ``psutil`` 出错，我们使用 ``psutil`` 来收集系统参数比如CPU利用率。这种情况，需要看看 `installation instructions of psutil <https://github.com/giampaolo/psutil/blob/master/INSTALL.rst>`_ 。记住Rally是基于Python 3的，你需要安装Python 3相关头文件，而不是Python 2的。

Non-sudo安装
~~~~~~~~~~~~~~~~

如果你不想用 ``sudo`` ，也是可行的，只是稍微复杂一点：

1. 在安装Rally（上面的步骤）的时候指定参数 ``--user`` ，所以执行的命令是：``python3 setup.py develop --user`` 。
2. 检查安装脚本的输出，或者查看 `Python documentation on the variable site.USER_BASE <https://docs.python.org/3.5/library/site.html#site.USER_BASE>`_ 找到脚本（译注：这里的脚本指的应该是 ``esrally`` ）的位置。在Linux下，通常在 ``~/.local/bin`` 。

你可以添加 ``~/.local/bin`` 到PATH路径中，或者直接调用 ``~/.local/bin/esrally`` 来使用esrally。

VirtualEnv安装
~~~~~~~~~~~~~~~~~~

你也可以使用Virtualenv来安装Rally到一个隔离的环境，而不用使用sudo。

1. 使用 ``virtualenv --python=python3`` 安装一个新virtualenv环境到一个目录
2. 使用 ``/path/to/virtualenv/dir/bin/activate`` 激活这个virtualenv环境
3. 使用 ``pip install esrally`` 安装Rally

当你要使用Rally时，先运行激活脚本（上面第2步）。当你使用完Rally，在Shell中执行 ``deactivate``  来退出虚拟环境。


下一步
~~~~~~~~~~

当你安装完成，你还需要配置它。执行 ``esrally configure`` 或者参考 :doc:`配置帮助 </configuration>`
