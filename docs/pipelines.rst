Pipelines
=========

pipeline 是为了获得基准测试结果而执行的一系列步骤。这里的 pipeline 不是为了定义基准测试流程或者基准测试执行前后的步骤。 

通过一个例子来澄清这个：如果你希望对一个 Elasticsearch 二进制发行版进行基准测试，Rally 需要去下载一个发行版包，解压，启动 Elasticsearch，然后运行基准测试。然而，如果你希望对一份 Elasticsearch 源码进行基准测试，首先需要通过 Gradle 构建出一个二进制版本。所以，在这两个例子中，pipeline 执行了不同的步骤。

你可以通过 ``esrally list pipelines`` 来获取所有的 pipeline 列表::

    Available pipelines:

    Name                     Description
    -----------------------  ---------------------------------------------------------------------------------------------
    from-distribution        Downloads an Elasticsearch distribution, provisions it, runs a benchmark and reports results.
    from-sources-complete    Builds and provisions Elasticsearch, runs a benchmark and reports results.
    benchmark-only           Assumes an already running Elasticsearch instance, runs a benchmark and reports results
    from-sources-skip-build  Provisions Elasticsearch (skips the build), runs a benchmark and reports results.

benchmark-only
~~~~~~~~~~~~~~

这种方式适用于你希望对自己搭建的集群做基准测试。当你完全确定你需要这么做时，才使用这种方式，否则，别用。由于被测试的集群不是 Rally 启动的，测试的结果可能不容易重现，并且像CPU使用率这些指标没法被收集。

在对集群进行基准测试的时候，你同样需要指定要被连接的主机信息。调用方式如下::

    esrally --pipeline=benchmark-only --target-hosts=search-node-a.intranet.acme.com:9200,search-node-b.intranet.acme.com:9200


from-distribution
~~~~~~~~~~~~~~~~~

This pipeline allows to benchmark an official Elasticsearch distribution which will be automatically downloaded by Rally. The earliest supported version is Elasticsearch 1.7.0. An example invocation::

    esrally --pipeline=from-distribution --distribution-version=1.7.5

The version numbers have to match the name in the download URL path.

You can also benchmark Elasticsearch snapshot versions by specifying the snapshot repository::

    esrally --pipeline=from-distribution --distribution-version=5.0.0-SNAPSHOT --distribution-repository=snapshot

However, this feature is mainly intended for continuous integration environments and by default you should just benchmark official distributions.

.. note::

   This pipeline is just mentioned for completeness but Rally will autoselect it for you. All you need to do is to define the ``--distribution-version`` flag.

.. _pipelines_from-sources-complete:

from-sources-complete
~~~~~~~~~~~~~~~~~~~~~

You should use this pipeline when you want to build and benchmark Elasticsearch from sources. This pipeline will only work from Elasticsearch 5.0 onwards because Elasticsearch switched from Maven to Gradle and Rally only supports one build tool in the interest of maintainability.

Remember that you also need to install git and Gradle before and Rally needs to be configured for building for sources. If that's not the case you'll get an error and have to run ``esrally configure`` first. An example invocation::

    esrally --pipeline=from-sources-complete --revision=latest

You have to specify a :ref:`revision <clr_revision>`.

.. note::

   This pipeline is just mentioned for completeness but Rally will automatically select it for you. All you need to do is to define the ``--revision`` flag.


from-sources-skip-build
~~~~~~~~~~~~~~~~~~~~~~~

This pipeline is similar to ``from-sources-complete`` except that it assumes you have built the binary once. It saves time if you want to run a benchmark twice for the exact same version of Elasticsearch. Obviously it doesn't make sense to provide a revision: It is always the previously built revision. An example invocation::

    esrally --pipeline=from-sources-skip-build

