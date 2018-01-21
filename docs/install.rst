安装
============

这是Rally的详细安装指南。如果你赶时间，你可以去看看 :doc:`快速入门 </quickstart>` 。

先决条件
------------

Rally 支持Windows平台，只在 MacOS 和 Linux 平台上测试过。在安装 Rally 之前，请确定已经安装了下面这些包。

Python
~~~~~~

* Python 3.4 或者 最好 `python3` 在 PATH 中。通过 ``python3 --version`` 验证。
* Python3 的头文件（包含在Python3 开发包中）  
* ``pip3`` 在PATH中。通过 ``pip3 --version`` 验证。

**Debian / Ubuntu**

::

    sudo apt-get install gcc python3-pip python3-dev


**RHEL 6/ CentOS 6**

*测试过 CentOS release 6.9 (Final).*

.. note::

    你需要先开启 `EPEL <https://fedoraproject.org/wiki/EPEL>`_ 。 

::

    sudo yum install -y gcc python34.x86_64 python34-devel.x86_64 python34-setuptools.noarch
    # installs pip as it is not available as an OS package
    sudo python3 /usr/lib/python3.4/site-packages/easy_install.py pip


**RHEL 7 / CentOS 7**

.. note::

    你需要先开启 `EPEL <https://fedoraproject.org/wiki/EPEL>`_ 。

*测试过 CentOS Linux release 7.4.1708 (Core).*

::

    sudo yum install -y gcc python34.x86_64 python34-devel.x86_64 python34-pip.noarch

**Amazon Linux**

::

    sudo yum install -y gcc python35-pip.noarch python35-devel.x86_64

**MacOS**

我们推荐你使用 `Homebrew <https://brew.sh/>`_::

    brew install python3

git
~~~

``git 1.9`` 或更高版本。通过 ``git --version`` 验证。

**Debian / Ubuntu**

::

    sudo apt-get install git


**Red Hat / CentOS / Amazon Linux**

::

    sudo yum install git


.. note::

   如果你使用RHEL，请确定通过 `Red Hat Software Collections <https://www.softwarecollections.org/en/scls/rhscl/git19/>`_ 安装了最近版本的git。


**MacOS**

``git`` 已经在 MacOS 中安装好。

JDK
~~~

如果你需要启动 Elasticsearch，你需要在所有需要启动的机器上安装 JDK 。如果你只是把 Rally 用来做负载发生器，JDK 就不需要了。

我们建议使用 Oracle JDK，你也可以使用OpenJDK。更多安装 JDK 的细节，你可以看看你操作系统的文档。


.. note::

   如果你下载了 Rally，安装并进行本地Elasticsearch的基准测试 (比如 `Rally 默认行为 <http://esrally.readthedocs.io/en/stable/quickstart.html#run-your-first-race>`_)，你需要确定按照 `推荐的内核设置 <https://www.elastic.co/guide/en/elasticsearch/reference/master/system-config.html>`_ 配置了你的 Rally 服务器的操作系统。


安装 Rally
----------------

通过 pip 就可以简单的安装 Rally: ``pip3 install esrally``

.. note::

   根据你的系统设置，你可能需要使用 ``sudo`` 来执行这个命令 

如果在安装过程中出错，可能是在安装 ``psutil`` 出错，我们使用 ``psutil`` 来收集系统参数比如CPU利用率。请确定你已经安装过上面“先决条件”里提到的 Python 开发包。

Non-sudo 安装
----------------

如果你不想用 ``sudo`` ，也是可行的，只是稍微复杂一点：

1. 在安装Rally（上面的步骤）的时候指定参数 ``--user`` ，所以执行的命令是：``python3 setup.py develop --user`` 。
2. 检查安装脚本的输出，或者查看 `Python documentation on the variable site.USER_BASE <https://docs.python.org/3.5/library/site.html#site.USER_BASE>`_ 找到脚本（译注：这里的脚本指的应该是 ``esrally`` ）的位置。在Linux下，通常在 ``~/.local/bin`` 。

你可以添加 ``~/.local/bin`` 到PATH路径中，或者直接调用 ``~/.local/bin/esrally`` 来使用esrally。

VirtualEnv 安装 
------------------

你也可以使用Virtualenv来安装Rally到一个隔离的环境，而不用使用sudo。

1. 使用 ``virtualenv --python=python3`` 安装一个新virtualenv环境到一个目录
2. 使用 ``source /path/to/virtualenv/dir/bin/activate`` 激活这个virtualenv环境
3. 使用 ``pip install esrally`` 安装Rally

当你要使用Rally时，先运行激活脚本（上面第2步）。当你使用完Rally，在Shell中执行 ``deactivate``  来退出虚拟环境

.. _install_offline-install:

Kubernetes Job
--------------

你可以通过 `this <https://github.com/gdmello/elasticsearch-rally>`_ `Helm Chart <https://helm.sh/>`_ 把 Rally 当作一个 Kubernetes `Job <https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/>`_ 来运行。

Docker
------

你也可以把 Rally 运行在一个 docker 容器。按照这个说明来做 `here <https://github.com/gdmello/elasticsearch-rally/tree/master/docker>`_ 。

离线安装
---------------

.. ifconfig:: release.endswith('.dev0')

    .. warning::

        This documentation is for the version of Rally currently under development. We do not provide offline installation packages for development versions.
        Were you looking for the `documentation of the latest stable version <//esrally.readthedocs.io/en/stable/>`_?

如果你在一个不能访问因特网的企业环境，你可以使用 Rally 的离线安装包。按照下面的这些步骤来安装：

1. 安装上面文档提到的所有依赖包
2. 下载 `最新发布的离线包 <https://github.com/elastic/rally/releases/latest>`_ ，并将它拷贝到目标机器。
3. 解压安装包 ``tar -xzf esrally-dist-*.tar.gz`` 。
4. 执行安装脚本 ``sudo ./esrally-dist-*/install.sh`` 。

下一步
----------

当你安装了 Rally 之后，你需要配置它。运行 ``esrally configure`` 或者参考 :doc:`配置帮助 </configuration>` 获得更多的指导。
