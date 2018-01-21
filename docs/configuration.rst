配置
=============

Rally已经配置为安装即用。如果你在安装完Rally后，运行 ``esrally`` ，它会检查配置是否存在，然后问你一些问题（以完成配置）。

如果你后面需要重新配置Rally，只需要重新执行 ``esrally configure`` 。


简单配置
--------------------

Rally默认会运行一个简单的配置程序，并且自动检测更多的默认配置供你选择。如果你需要更多的控制 Rally 配置，你可以执行 ``esrally configure --advanced-config`` 。

Rally能够从源代码或者 `官方发布的二进制版本 <https://www.elastic.co/downloads/elasticsearch>`_ 构建 Elasticsearch。如果你需要用 Rally 从源代码构建 Elasticsearch，只能使用 Elasticsearch 5.0 及以上版本。因为 Elasticsearh 5.0 的构建工具由 Maven 换成了 Gradle。Rally 只支持 Gradle，所以限制了只能用（源代码构建）Elasticsearch 5.0 及以上版本。

如果你想从源代码构建 Elasticsearch，在运行配置程序之前需要安装 Gradle。

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

    * Autodetecting available third-party software
      git    : [OK]
      gradle : [OK]
      JDK    : [OK]

    * Setting up benchmark data directory in /Users/dm/.rally/benchmarks

当你看到上面这些，Rally 自动检测了 git，Gradle 和 JDK 是否已经安装。如果你没有安装 Gradle，也没关系，只是不能从源代码构建 Elasticsearch 而已。我们假设没安装 Gradle::

    dm@io:~ $ esrally

        ____        ____
       / __ \____ _/ / /_  __
      / /_/ / __ `/ / / / / /
     / _, _/ /_/ / / / /_/ /
    /_/ |_|\__,_/_/_/\__, /
                    /____/

    Running simple configuration. You can run the advanced configuration with:

      esrally configure --advanced-config

    * Autodetecting available third-party software
      git    : [OK]
      gradle : [MISSING]
      JDK 8  : [OK]

    ********************************************************************************
    You don't have the required software to benchmark Elasticsearch source builds.

    You can still benchmark binary distributions with e.g.:

      esrally --distribution-version=5.0.0
    ********************************************************************************

正如你所看到的，Rally告诉你，你不能从源代码构建Elasticsearch，但是你仍然可以使用官方发布的二进制版本做基准测试。

Rally可能不能自动找到你的 JDK 8 或者 JDK 9 的 home 目录。如果是这样，它会在后面的配置过程中问你。如果你不提供 JDK 的 home 目录，Rally 就不能在这个机器上启动 Elasticsearch，但是你还是可以把它当作负载发生器来 :doc:`基准测试远程集群 </recipes>` 。

在初始化检测之后，Rally 会自动检测你的 Elasticsearch 项目目录（在当前目录或者在 ``../elasticsearch`` ）或者选择一个默认目录::

    * 设置基准测试数据目录(data directory)为 /Users/dm/.rally/benchmarks
    * 设置基准测试源代码目录(source directory)为 /Users/dm/.rally/benchmarks/src/elasticsearch

如果Rally在第一步没有找到Gradle，它不会问你要源代码目录，而是会跳过继续。

Rally配置完成::

    Configuration successfully written to /Users/dm/.rally/rally.ini. Happy benchmarking!

    To benchmark Elasticsearch with the default benchmark, run:

      esrally

    More info about Rally:

    * Type esrally --help
    * Read the documentation at https://esrally.readthedocs.io/en/latest/
    * Ask a question on the forum at https://discuss.elastic.co/c/elasticsearch/rally

恭喜你！可以 :doc:`运行你的第一个基准测试 </race>` 了。

高级配置
----------------------

如果你需要通过一些变量来控制更多或者希望存储你的指标(metrics)到一个专用的 Elasticsearch 实例中，那么你需要执行高级配置程序。你可以在任何时候调用 ``esrally configure --advanced-config`` 。

先决条件
~~~~~~~~~~~~~

当使用高级配置时，你可以选择 Rally 把它的指标(metrics)存储到一个专用的 Elasticsearch 实例中，而不是存在内存里。因此，你需要安装下面这些软件:

* Elasticsearch：作为Rally指标存储的专用Elasticsearch实例。如果你不希望自己设置，你也可以使用 `Elastic Cloud <https://www.elastic.co/cloud>`_ 。
* 可选：Kibana（也包含在 `Elastic Cloud <https://www.elastic.co/cloud>`_ ）。

准备工作
~~~~~~~~~~~~~

首先 `安装Elasticsearch <https://www.elastic.co/downloads/elasticsearch>`_ 5.0或更高版本。安装一个节点的版本就可以了。Rally使用这个实例存储指标数据。它自己会创建必要的索引库。配>置过程会问你要这个（Elasticsearch）集群的主机和端口信息

.. note::

   Rally会选择 39200-39300 (HTTP) 和 39300-39400 (transport) 的端口范围来做集群的基准测试。所以请确定这些端口没有被占用。

可选但是推荐安装 `Kibana <https://www.elastic.co/downloads/kibana>`_ 。注意Rally不会自动配置Kibana。

配置选项
~~~~~~~~~~~~~~~~~~~~~

Rally在高级配置中会问你一些东西：

* **基准测试数据目录(data directory)**: Rally 在这个目录下存储所有基准测试相关的数据，这个目录可能需要数十GB。如果你希望使用一个专用分区，你可以在这里指定一个不同的数据目录。
* **Elasticsearch 项目目录**: 这是Elasticsearch源代码所在的目录。如果你不开发Elasticsearch，默认配置就行了。如果你需要对本地修改的代码做基准测试，那么你就需要指定你的开发项目目录了。>注意，Rally会在这个目录执行构建（它会运行 ``gradle clean`` 和 ``gradle :distribution:tar:assemble`` ）
* **JDK 8 根目录**: 如果Rally自动检测不到JDK 8的home目录，它会问你。填写你需要用的JDK的根目录。默认情况下，如果 Java 8 可用，Rally会选择 Java 8，否则会回退到 Java 9。 
* **Metrics store type**: 你可以选择 ``in-memory`` 或者 ``elasticsearch`` 这两种类型； ``in-memory`` 不需要额外的部署， ``elasticsearch`` 需要你启动一个专用的 Elasticsearch 实例来存储指标(metrics)，但是它让你对结果能更灵活的进行分析。
* **Metrics store settings** (只针对 metrics store type ``elasticsearch`` 有效)：提供一个 Elasticsearch 指标存储的连接详细信息。这个实例只能专用于 Rally，它可以是一个很小的实例。默认配置的单节点 Elasticsearch 就可以了。
* **Name for this benchmark environment** (只针对 metrics store type ``elasticsearch`` 有效)：你可以使用同一个指标存储为多个环境服务(比如，本地的、持续集成等等。译注：应该指的是对于多个基准测试的环境)。你可以不同的名称来区分这些环境的指标。
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
