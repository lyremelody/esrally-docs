总结报告
==============

在每次 :doc:`基准测试(race) </race>` 最后，Rally都会提供总结报告。接下来，我们解释一下每行的意思，这些在 :doc:`metrics key </metrics>` 都有引用，如果你希望自己在Kibana中构建你的报告，这些解释会有些帮助。注意，不是每个总结报告都会显示所有的这些行。

Indexing time
-------------

* **Definition**: 索引使用的总时间，通过 Elasticsearch 的 indices stats API 获得。注意这里不是挂钟时间(例如，如果 M 个索引线程跑了 N 分钟，我们会报告 M*N 分钟，而不是 N 分钟)。
* **Corresponding metrics key**: ``indexing_total_time``

Indexing throttle time
----------------------

* **Definition**: Total time that indexing has been throttled as reported by the indices stats API. 注意这里不是挂钟时间(例如，如果 M 个索引线程跑了 N 分钟，我们会报告 M*N 分钟，而不是 N 分钟)。
* **Corresponding metrics key**: ``indexing_throttle_time``

Merge time
----------

* **Definition**: 合并用的总运行时间，通过 Elasticsearch 的 indices stats API 获得。注意这里不是挂钟时间。
* **Corresponding metrics key**: ``merges_total_time``

Refresh time
------------

* **Definition**: 索引刷新(更新信息，可用于搜索)消耗的总时间，通过 Elasticsearch 的 indices stats API 获得。注意这里不是挂钟时间。
* **Corresponding metrics key**: ``refresh_total_time``

Flush time
----------

* **Definition**: 索引刷新(写入磁盘)消耗的总时间，通过 Elasticsearch 的 indices stats API 获得。注意这里不是挂钟时间。
* **Corresponding metrics key**: ``flush_total_time``

Merge throttle time
-------------------

* **Definition**: Total time within merges have been throttled as reported by the indices stats API. Note that this is not Wall clock time.
* **Corresponding metrics key**: ``merges_total_throttled_time``


Merge time (``X``)
------------------

Where ``X`` is one of:

* postings
* stored fields
* doc values
* norms
* vectors
* points

..

* **Definition**: Lucene 报告的合并时间，这里不同于上文的 “合并用的总运行时间”。仅当 Lucene 索引写跟踪日志开启的时候有效 (配置 car 的 `verbose_iw` 来获取这个结果)
* **Corresponding metrics keys**: ``merge_parts_total_time_*``


Median CPU usage
----------------

* **Definition**: 整个测试 Elasticsearch 处理过程中，CPU的中位使用率，基于1秒采样周期。最大值是 N * 100%，N 是CPU核数。
* **Corresponding metrics key**: ``cpu_utilization_1s``


Total Young Gen GC
------------------

* **Definition**: 集群年轻代垃圾回收总的耗时，通过 Elasticsearch 的 node stats API 获得。
* **Corresponding metrics key**: ``node_total_young_gen_gc_time``


Total Old Gen GC
----------------

* **Definition**: 集群老年代垃圾回收总的耗时，通过 Elasticsearch 的 node stats API 获得。
* **Corresponding metrics key**: ``node_total_old_gen_gc_time``

Index size
----------

* **Definition**: 基准测试后，索引最终的大小。
* **Corresponding metrics key**: ``final_index_size_bytes``

Totally written
---------------

* **Definition**: 在基准测试过程中总共写到磁盘的字节数。在 Linux 系统上，这个指标仅代表 Elasticsearch 写的数据字节数；在 Mac OS X 上，这个指标代表所有进程写的数据字节数。
* **Corresponding metrics key**: ``disk_io_write_bytes``

Heap used for ``X``
-------------------

Where ``X`` is one of:


* doc values
* terms
* norms
* points
* stored fields

..

* **Definition**: 相关项目使用的字节数，通过 Elasticsearch 的 indices stats API 获得。
* **Corresponding metrics keys**: ``segments_*_in_bytes``

Segment count
-------------

* **Definition**: 总段数，通过 Elasticsearch 的 indices stats API 获得。
* **Corresponding metrics key**: ``segments_count``


Throughput
----------

Rally 为每个任务报告最小吞吐率、中位吞吐率和最大吞吐率。

* **Definition**: Elasticsearch 在一段时间内能够处理的操作数，通常时间长度是秒。
* **Corresponding metrics key**: ``throughput``

Latency
-------

Rally 为每个任务报告一些百分位数字。显示哪个百分位数取决于 Rally 能够捕捉到多少请求 (比如，如果 Rally 只能捕获5个样例，它不会显示 99.99% 的数值内容，因为这样会虚报)。

* **Definition**: 提交请求和收到完整回复之间的时间段。它也包含等待时间，比如，请求花费在等待 Elasticsearch 准备好服务的时间。
* **Corresponding metrics key**: ``latency``

Service time
------------

Rally 为每个任务报告一些百分位数字。显示哪个百分位数取决于 Rally 能够捕捉到多少请求 (比如，如果 Rally 只能捕获5个样例，它不会显示 99.99% 的数值内容，因为这样会虚报)。

* **Definition**: 请求处理开始和接收完整响应之间的时间段。这个指标很容易与 ``latency`` 混淆，它不包含等待时间。大多数负载测试工具称之为“延迟” (虽然这是错误的)。
* **Corresponding metrics key**: ``service_time``

Error rate
----------

* **Definition**: 错误率，错误响应相对于响应总数的比例。任何被 Elasticsearch Python 客户端抛出的异常都被认为是错误响应(例如，HTTP 响应码 4xx、5xx或者网络错误，如网络不可达)。对于具体的细节，请参考 `Elasticsearch 客户端参考文档 <https://elasticsearch-py.readthedocs.io>`_ 。通常任何大于零的错误率都会提示。你应该通过检查 Rally 和 Elasticsearch 的日志来确定根本原因，然后重新进行基准测试。
* **Corresponding metrics key**: ``service_time``. 每个 ``service_time`` 记录都会有 ``meta.success`` 标记。Rally 简单统计这个标记是 ``true`` 和 ``false`` 的个数。
