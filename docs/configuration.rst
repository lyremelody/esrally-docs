配置
=============

Rally已经配置为安装即用。如果你在安装完Rally后，运行 ``esrally`` ，它会检查配置是否存在，然后问你一些问题（以完成配置）。

如果你后面需要重新配置Rally，只需要重新执行 ``esrally configure`` 。

.. note::

   如果执行过程中出现这个错误 ``UnicodeEncodeError: 'ascii' codec can't encode character``，请将你的shell配置支持UTF-8编码。你可以通过执行 ``locale`` 来检查当前环境否是UTF-8编码。如果还有问题，那么添加环境变量设置 ``export LC_ALL=en_US.UTF-8`` 到你的shell初始化文件中（如果使用Bash，那么shell初始化文件是 ``~/.bashrc`` ），然后重新登录。


简单配置
--------------------

Rally默认会运行一个简单的配置程序，并且自动检测更多的默认配置供你选择。如果你需要更多的控制Rally配置，你可以执行 ``esrally configure --advanced-config`` 。

Rally能够从源代码或者 `官方发布的二进制版本 <https://www.elastic.co/downloads/elasticsearch>`_ 构建Elasticsearch。如果你需要用Rally从源代码构建Elasticsearch，只能使用Elasticsearch 5.0及以上版本。因为Elasticsearh 5.0的构建工具由Maven换成了Gradle。Rally只支持Gradle，所以限制了只能用（源代码构建）Elasticsearch 5.0及以上版本。

如果你想从源代码构建Elasticsearch，在运行配置程序之前需要安装Gradle 2.13。

我们来一步步的运行一个例子：首先运行 ``esrally``::

    dm@io:~ $ esrally

        ____        ____
       / __ \____ _/ / /_  __
      / /_/ / __ `/ / / / / /
     / _, _/ /_/ / / / /_/ /
    /_/ |_|\__,_/_/_/\__, /
                    /____/

    Running simple configuration. You can run the advanced configuration with:

      esrally configure --advanced-config

    [鉁揮 Autodetecting available third-party software
      git    : [鉁揮
      gradle : [鉁揮
      JDK 8  : [鉁揮

    [鉁揮 Setting up benchmark data directory in [/Users/dm/.rally/benchmarks] (needs several GB).


当你看到上面这些，Rally自动检测了git，Gradle和JDK 8是否已经安装。如果你没有安装Gradle，也没关系，只是不能从源代码构建Elasticsearch而已。我们假设没安装Gradle::

    dm@io:~ $ esrally

        ____        ____
       / __ \____ _/ / /_  __
      / /_/ / __ `/ / / / / /
     / _, _/ /_/ / / / /_/ /
    /_/ |_|\__,_/_/_/\__, / /____/

    Running simple configuration. You can run the advanced configuration with:

      esrally configure --advanced-config

    [鉁揮 Autodetecting available third-party software
      git    : [鉁揮
      gradle : [鉁昡
      JDK 8  : [鉁揮

    **********************************************************************************
    You don't have the necessary software to benchmark source builds of Elasticsearch.

    You can still benchmark binary distributions with e.g.:

      esrally --distribution-version=5.0.0
    **********************************************************************************

正如你所看到的，Rally告诉你，你不能从源代码构建Elasticsearch，但是你仍然可以使用官方发布的二进制版本做基准测试。

Rally可能不能自动找到你的JDK 8 home目录。如果是这样，它会在后面的配置过程中问你。

在初始化检测之后，Rally会自动检测你的Elasticsearch项目目录（在当前目录或者在 ``../elasticsearch`` ）。如果一些正常，你会看到这些::

    [鉁揮 Autodetected Elasticsearch project directory at [/Users/dm/elasticsearch].

否则，Rally会选择一个默认目录并且提示你确认::

    [鉁揮 Setting up benchmark data directory in [/Users/dm/.rally/benchmarks] (needs several GB).
    Enter your Elasticsearch project directory: [default: '/Users/dm/.rally/benchmarks/src']:

如果你觉得这个默认值可以，那么按回车键（“Enter”），Rally会办好剩余的事情。否则，你需要在这提供你的Elasticsearch项目目录。请记住，当你启动了一个基准测试，Rally将会在这个目录使用Gradle启动构建。

如果Rally在第一步没有找到Gradle，它不会问你要源代码目录，而是会跳过继续。

Rally配置完成::

    [鉁揮 Configuration successfully written to [/Users/dm/.rally/rally.ini]. Happy benchmarking!

    To benchmark the latest version of Elasticsearch with the default benchmark run:

      esrally --revision=latest

    For more help see the user documentation at https://esrally.readthedocs.io

恭喜你！可以运行你的第一个基准测试了。

高级配置
----------------------

如果你需要通过一些变量来控制更多或者希望使用更高级的功能，比如 :doc:`tournaments </tournament>` ，那么你需要执行高级配置程序。你可以在任何时候调用 ``esrally configure --advanced-config`` 。

先决条件
~~~~~~~~~~~~~

当使用高级配置时，Rally会把它的指标（metrics）存储到一个专用的Elasticsearch实例中，而不是存在内存里。因此，你需要安装下面这些软件:

* Elasticsearch：作为Rally指标存储的专用Elasticsearch实例。如果你不希望自己设置，你也可以使用 `Elastic Cloud <https://www.elastic.co/cloud>`_ 。
* 可选：Kibana（也包含在 `Elastic Cloud <https://www.elastic.co/cloud>`_ ）。

准备工作
~~~~~~~~~~~

首先 `安装Elasticsearch <https://www.elastic.co/downloads/elasticsearch>`_ 2.3或更高版本。安装一个节点的版本就可以了。Rally使用这个实例存储指标数据。它自己会创建必要的索引库。配置过程会问你要这个（Elasticsearch）集群的主机和端口信息。

.. note::

   Rally会选择 39200-39300 (HTTP) 和 39300-39400 (transport) 的端口范围来做集群的基准测试。所以请确定这些端口没有被占用。

可选但是推荐安装 `Kibana <https://www.elastic.co/downloads/kibana>`_ 。注意Rally不会自动配置Kibana。

配置选项
~~~~~~~~~~~~~~~~~~~~~

Rally在高级配置中会问你一些东西：

* Elasticsearch项目目录：这是Elasticsearch源代码所在的目录。如果你不开发Elasticsearch，默认配置就行了。如果你需要对本地修改的代码做基准测试，那么你就需要指定你的开发项目目录了。注意，Rally会在这个目录执行构建（它会运行 ``gradle clean`` 和 ``gradle :distribution:tar:assemble`` ）。
* JDK 8 根目录：如果Rally自动检测不到JDK 8的home目录，它会问你。填写你需要用的JDK的根目录。
* 这个基准测试环境的名字：你可以使用同一个指标存储（Elasticsearch实例）用于多个环境（比如本地，持续集成等）。所以你可以通过不同的名字来区分不同的环境。（译注：多个环境共用一个Elasticsearch实例作为存储；一种环境用一个索引库存信息，所以这里的名字应该可以理解为存储环境的指标数据的索引库的名字）
* 指标存储配置：提供用于存储指标数据的Elasticsearch的详细连接信息。它必须是一个（Elasticsearch）实例，但是它的规模可以很小。一个单节点集群默认配置就可以满足需求。现在还不支持在高级配置中选择内存存储。如果你确实需要，请在Github上提交一个issue。
* Rally是否要安装Elasticsearch基准测试所有默认需要安装的数据。这会使用很多磁盘空间，你需要定期清理 ``~/.rally/benchmarks/races`` 。

代理配置
-------------------

Rally downloads all necessary data automatically for you:

* Elasticsearch distributions from elastic.co if you specify ``--distribution-version=SOME_VERSION_NUMBER``
* Elasticsearch source code from Github if you specify a revision number e.g. ``--revision=952097b``
* Track meta-data from Github
* Track data from an S3 bucket

Hence, it needs to connect via http(s) to the outside world. If you are behind a corporate proxy you need to configure Rally and git. As many other Unix programs, Rally relies that the HTTP proxy URL is available in the environment variable ``http_proxy`` (note that this is in lower-case). Hence, you should add this line to your shell profile, e.g. ``~/.bash_profile``::

    export http_proxy=http://proxy.acme.org:8888/

Afterwards, source the shell profile with ``source ~/.bash_profile`` and verify that the proxy URL is correctly set with ``echo $http_proxy``.

Finally, you can set up git::

    git config --global http.proxy $http_proxy

For details, please refer to the `Git config documentation <https://git-scm.com/docs/git-config>`_.

Please verify that the proxy setup for git works correctly by cloning any repository, e.g. the ``rally-tracks`` repository::

    git clone https://github.com/elastic/rally-tracks.git

If the configuration is correct, git will clone this repository. You can delete the folder ``rally-tracks`` after this verification step.

To verify that Rally will connect via the proxy server you can check the log file. If the proxy server is configured successfully, Rally will log the following line on startup::

    Rally connects via proxy URL [http://proxy.acme.org:3128/] to the Internet (picked up from the environment variable [http_proxy]).


.. note::

   Rally will use this proxy server only for downloading benchmark-related data. It will not use this proxy for the actual benchmark.
