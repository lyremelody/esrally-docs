运行一个基准测试: Races
==================================

译注：建议开始之前先看 :doc:`术语表 </glossary>`, 对整体概念有个了解。像“race”、“track”、“challenge”这些概念，我在这个翻译中尽量保持原词(不翻译)。

定义
----------

在Rally中，一个“race”是一次基准测试实验的执行。你可以在你的基准测试中选择不同的基准测试场景( :doc:`tracks </track>` )

List Tracks
-----------

从获取可用的tracks开始::

    esrally list tracks

将显示如下列表::

    Name        Description                                          Documents  Compressed Size    Uncompressed Size    Default Challenge        All Challenges
    ----------  -------------------------------------------------  -----------  -----------------  -------------------  -----------------------  ---------------------------
    geonames    POIs from Geonames                                    11396505  252.4 MB           3.3 GB               append-no-conflicts      append-no-conflicts,appe...
    geopoint    Point coordinates from PlanetOSM                      60844404  481.9 MB           2.3 GB               append-no-conflicts      append-no-conflicts,appe...
    http_logs   HTTP server log data                                 247249096  1.2 GB             31.1 GB              append-no-conflicts      append-no-conflicts,appe...
    nested      StackOverflow Q&A stored as nested docs               11203029  663.1 MB           3.4 GB               nested-search-challenge  nested-search-challenge,...
    noaa        Global daily weather measurements from NOAA           33659481  947.3 MB           9.0 GB               append-no-conflicts      append-no-conflicts,appe...
    nyc_taxis   Taxi rides in New York in 2015                       165346692  4.5 GB             74.3 GB              append-no-conflicts      append-no-conflicts,appe...
    percolator  Percolator benchmark based on AOL queries              2000000  102.7 kB           104.9 MB             append-no-conflicts      append-no-conflicts,appe...
    pmc         Full text benchmark with academic papers from PMC       574199  5.5 GB             21.7 GB              append-no-conflicts      append-no-conflicts,appe...

前两列展示每个 track 的名称和描述。一个 track 会制定一个或多个 challenges 来描述要被运行的负载任务。

启动一个基准测试
-----------------------

.. note::
    不要以 root 用户运行 Rally，因为 Elasticsearch 不能以 root 权限启动。

你需要定义 track 和 challenge 来启动一个基准测试。例如::

    esrally --distribution-version=6.0.0 --track=geopoint --challenge=append-fast-with-conflicts

Rally 会基于这个 track 来运行基准测试。如果你之前没有启动过 Rally，它可能是类似如下的输出::

    dm@io:~ $ esrally --distribution-version=6.0.0 --track=geopoint --challenge=append-fast-with-conflicts

        ____        ____
       / __ \____ _/ / /_  __
      / /_/ / __ `/ / / / / /
     / _, _/ /_/ / / / /_/ /
    /_/ |_|\__,_/_/_/\__, /
                    /____/

    [INFO] Racing on track [geopoint], challenge [append-fast-with-conflicts] and car ['defaults'] with version [6.0.0].
    [INFO] Downloading Elasticsearch 6.0.0 ... [OK]
    [INFO] Rally will delete the benchmark candidate after the benchmark
    [INFO] Downloading data from [http://benchmarks.elasticsearch.org.s3.amazonaws.com/corpora/geopoint/documents.json.bz2] (482 MB) to [/Users/dm/.rally/benchmarks/data/geopoint/documents.json.bz2] ... [OK]
    [INFO] Decompressing track data from [/Users/dm/.rally/benchmarks/data/geopoint/documents.json.bz2] to [/Users/dm/.rally/benchmarks/data/geopoint/documents.json] (resulting size: 2.28 GB) ... [OK]
    [INFO] Preparing file offset table for [/Users/dm/.rally/benchmarks/data/geopoint/documents.json] ... [OK]
    Running index-update                                                           [  0% done]


运行基准测试需要一段时间，请耐心等待。

当基准测试结束，Rally 会在命令行展示总结报告::

    |                          Metric |         Task |     Value |   Unit |
    |--------------------------------:|-------------:|----------:|-------:|
    |                   Indexing time |              |   124.712 |    min |
    |                      Merge time |              |   21.8604 |    min |
    |                    Refresh time |              |   4.49527 |    min |
    |             Merge throttle time |              |  0.120433 |    min |
    |                Median CPU usage |              |     546.5 |      % |
    |              Total Young Gen GC |              |    72.078 |      s |
    |                Total Old Gen GC |              |     3.426 |      s |
    |                      Index size |              |   2.26661 |     GB |
    |                 Totally written |              |    30.083 |     GB |
    |          Heap used for segments |              |   10.7148 |     MB |
    |        Heap used for doc values |              | 0.0135536 |     MB |
    |             Heap used for terms |              |   9.22965 |     MB |
    |            Heap used for points |              |   0.78789 |     MB |
    |     Heap used for stored fields |              |  0.683708 |     MB |
    |                   Segment count |              |       115 |        |
    |                  Min Throughput | index-update |   59210.4 | docs/s |
    |               Median Throughput | index-update |   65276.2 | docs/s |
    |                  Max Throughput | index-update |   76516.6 | docs/s |
    |       50.0th percentile latency | index-update |   556.269 |     ms |
    |       90.0th percentile latency | index-update |   852.779 |     ms |
    |       99.0th percentile latency | index-update |   1854.31 |     ms |
    |       99.9th percentile latency | index-update |   2972.96 |     ms |
    |      99.99th percentile latency | index-update |   4106.91 |     ms |
    |        100th percentile latency | index-update |   4542.84 |     ms |
    |  50.0th percentile service time | index-update |   556.269 |     ms |
    |  90.0th percentile service time | index-update |   852.779 |     ms |
    |  99.0th percentile service time | index-update |   1854.31 |     ms |
    |  99.9th percentile service time | index-update |   2972.96 |     ms |
    | 99.99th percentile service time | index-update |   4106.91 |     ms |
    |   100th percentile service time | index-update |   4542.84 |     ms |
    |                  Min Throughput |  force-merge |  0.221067 |  ops/s |
    |               Median Throughput |  force-merge |  0.221067 |  ops/s |
    |                  Max Throughput |  force-merge |  0.221067 |  ops/s |
    |        100th percentile latency |  force-merge |   4523.52 |     ms |
    |   100th percentile service time |  force-merge |   4523.52 |     ms |


    ----------------------------------
    [INFO] SUCCESS (took 1624 seconds)
    ----------------------------------


.. note::
    你可以通过指定命令行参数 ``--report-file=/path/to/your/report.md`` 来把报告保存到文件，还可以通过命令行参数 ``--report-format=csv`` 将报告保存成CSV格式。

Rally 刚刚做了些什么？

* 它下载和启动了Elasticsearch 6.0.0
* 它下载了 geopoint track 相关数据
* 它运行了一个实际的基准测试
* 最后报告了结果

如果你好奇 Rally 运行的操作，请看 `geopoint track 规范 <https://github.com/elastic/rally-tracks/blob/5/geopoint/track.json>`_ 或者开始 :doc:`定义你自己的基准测试 </adding_tracks>` 。你也可以配置 Rally :doc:`存储所有数据到Elasticsearch </configuration>` ，然后你可以通过Kibana来分析结果。最后，你可能希望 :doc:`修改Elasticsearch配置 </car>` 。

