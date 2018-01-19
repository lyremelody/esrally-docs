运行Races
=============

“race” 在Rally中是指一个基准测试的执行。你可以使用不同的数据集（我们称为 :doc:`tracks </track>` ）来做基准测试。

获取Tracks列表
--------------------

开始获取有效的tracks::

    esrally list tracks

这样会显示下面的列表::

    Name        Description                                                           Challenges
    ----------  --------------------------------------------------------------------  ------------------------------------------------------------------------------------------------------
    geonames    Standard benchmark in Rally (8.6M POIs from Geonames)                 append-no-conflicts,append-no-conflicts-index-only,append-fast-no-conflicts,append-fast-with-conflicts
    geopoint    60.8M POIs from PlanetOSM                                             append-no-conflicts,append-no-conflicts-index-only,append-fast-no-conflicts,append-fast-with-conflicts
    logging     Logging benchmark                                                     append-no-conflicts,append-no-conflicts-index-only,append-fast-no-conflicts,append-fast-with-conflicts
    nyc_taxis   Trip records completed in yellow and green taxis in New York in 2015  append-no-conflicts,append-no-conflicts-index-only
    percolator  Percolator benchmark based on 2M AOL queries                          append-no-conflicts
    pmc         Full text benchmark containing 574.199 papers from PMC                append-no-conflicts,append-no-conflicts-index-only,append-fast-no-conflicts,append-fast-with-conflicts
    tiny        First 2k documents of the geonames track for local tests              append-no-conflicts,append-no-conflicts-index-only,append-fast-no-conflicts,append-fast-with-conflicts

前两列显示track的名称和简短描述。一个track指定一个或多个challenges（challeges定义会被运行的操作）

开始运行一个race
---------------------

.. note::
    不要使用root用户运行Rally，因为Elasticsearch不允许以root运行。

运行race，你需要定义要运行的track和challenge。例如::

    esrally --distribution-version=5.0.0 --track=geopoint --challenge=append-fast-with-conflicts

Rally开始基于指定的track运行基准测试。如果你之前没有启动过Rally，输出会与下面的内容相似::

    dm@io:~ $ esrally --distribution-version=5.0.0 --track=geopoint --challenge=append-fast-with-conflicts

        ____        ____
       / __ \____ _/ / /_  __
      / /_/ / __ `/ / / / / /
     / _, _/ /_/ / / / /_/ /
    /_/ |_|\__,_/_/_/\__, /
                    /____/

    [INFO] Racing on track [geopoint], challenge [append-fast-with-conflicts] and car [defaults]
    [INFO] Downloading Elasticsearch 5.0.0 ... [OK]
    [INFO] Rally will delete the benchmark candidate after the benchmark
    [INFO] Downloading data from [http://benchmarks.elasticsearch.org.s3.amazonaws.com/corpora/geopoint/documents.json.bz2] (482 MB) to [/Users/dm/.rally/benchmarks/data/geopoint/documents.json.bz2] ... [OK]
    [INFO] Decompressing track data from [/Users/dm/.rally/benchmarks/data/geopoint/documents.json.bz2] to [/Users/dm/.rally/benchmarks/data/geopoint/documents.json] (resulting size: 2.28 GB) ... [OK]
    [INFO] Preparing file offset table for [/Users/dm/.rally/benchmarks/data/geopoint/documents.json] ... [OK]
    Running index-update                                                           [  0% done]


请耐心等待基准测试运行完，它需要一些时间。

当测试运行完，Rally会在命令行显示一个测试总结::

    |                          Metric |    Operation |     Value |   Unit |
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
    你可以指定参数 ``--report-file=/path/to/your/report.md`` 来将结果报告保存在一个文件里，或者通过指定参数 ``--report-format=csv`` 将报告结果保存在CSV文件中

Rally究竟做了什么？

* 它下载并启动Elasticsearch 5.0.0
* 它下载geopoint track的相关数据
* 它执行了真实的基准测试
* 最后它提供了结果报告

如果你对Rally执行的操作比较关心，请查看 `geopoint track specification <https://github.com/elastic/rally-tracks/blob/5/geopoint/track.json>`_ 或者 :doc:`创建你自己的tracks </adding_tracks>` 。你可以配置Rally :doc:`store all data samples in Elasticsearch </configuration>` ，从而可以在Kibana中分析结果。最后，你可能会想 :doc:`修改（用于基准测试的）Elasticsearch的配置 </car>`


