快速入门
==========

安装
-------

安装Python 3.4+和 ``pip3`` ，JDK 8或9 和git 1.9+。然后执行下面的命令，如果需要的话在前面加上 ``sudo``::

    pip3 install esrally

如果你有任何困难或者需要更多细节的说明，请看 :doc:`详细安装指南</install>`

配置
---------

Just invoke ``esrally configure``.

更详细的说明和详细的演练参考 :doc:`配置指南 </configuration>`

执行你的第一个race
-------------------

现在准备执行我们的第一个 :doc:`race </glossary>`::

    esrally --distribution-version=6.0.0

这个操作会下载Elasticsearch 6.0.0，然后执行Rally的默认 :doc:`track </glossary>` - `geonames track <https://github.com/elastic/rally-tracks/tree/master/geonames>`_ 。执行完成后，会在命令行产生一个总结报告::

    ------------------------------------------------------
        _______             __   _____
       / ____(_)___  ____ _/ /  / ___/_________  ________
      / /_  / / __ \/ __ `/ /   \__ \/ ___/ __ \/ ___/ _ \
     / __/ / / / / / /_/ / /   ___/ / /__/ /_/ / /  /  __/
    /_/   /_/_/ /_/\__,_/_/   /____/\___/\____/_/   \___/
    ------------------------------------------------------

    |                         Metric |                 Task |     Value |   Unit |
    |-------------------------------:|---------------------:|----------:|-------:|
    |                  Indexing time |                      |   28.0997 |    min |
    |                     Merge time |                      |   6.84378 |    min |
    |                   Refresh time |                      |   3.06045 |    min |
    |                     Flush time |                      |  0.106517 |    min |
    |            Merge throttle time |                      |   1.28193 |    min |
    |               Median CPU usage |                      |     471.6 |      % |
    |             Total Young Gen GC |                      |    16.237 |      s |
    |               Total Old Gen GC |                      |     1.796 |      s |
    |                     Index size |                      |   2.60124 |     GB |
    |                Totally written |                      |   11.8144 |     GB |
    |         Heap used for segments |                      |   14.7326 |     MB |
    |       Heap used for doc values |                      |  0.115917 |     MB |
    |            Heap used for terms |                      |   13.3203 |     MB |
    |            Heap used for norms |                      | 0.0734253 |     MB |
    |           Heap used for points |                      |    0.5793 |     MB |
    |    Heap used for stored fields |                      |  0.643608 |     MB |
    |                  Segment count |                      |        97 |        |
    |                 Min Throughput |         index-append |   31925.2 | docs/s |
    |              Median Throughput |         index-append |   39137.5 | docs/s |
    |                 Max Throughput |         index-append |   39633.6 | docs/s |
    |      50.0th percentile latency |         index-append |   872.513 |     ms |
    |      90.0th percentile latency |         index-append |   1457.13 |     ms |
    |      99.0th percentile latency |         index-append |   1874.89 |     ms |
    |       100th percentile latency |         index-append |   2711.71 |     ms |
    | 50.0th percentile service time |         index-append |   872.513 |     ms |
    | 90.0th percentile service time |         index-append |   1457.13 |     ms |
    | 99.0th percentile service time |         index-append |   1874.89 |     ms |
    |  100th percentile service time |         index-append |   2711.71 |     ms |
    |                           ...  |                  ... |       ... |    ... |
    |                           ...  |                  ... |       ... |    ... |
    |                 Min Throughput |     painless_dynamic |   2.53292 |  ops/s |
    |              Median Throughput |     painless_dynamic |   2.53813 |  ops/s |
    |                 Max Throughput |     painless_dynamic |   2.54401 |  ops/s |
    |      50.0th percentile latency |     painless_dynamic |    172208 |     ms |
    |      90.0th percentile latency |     painless_dynamic |    310401 |     ms |
    |      99.0th percentile latency |     painless_dynamic |    341341 |     ms |
    |      99.9th percentile latency |     painless_dynamic |    344404 |     ms |
    |       100th percentile latency |     painless_dynamic |    344754 |     ms |
    | 50.0th percentile service time |     painless_dynamic |    393.02 |     ms |
    | 90.0th percentile service time |     painless_dynamic |   407.579 |     ms |
    | 99.0th percentile service time |     painless_dynamic |   430.806 |     ms |
    | 99.9th percentile service time |     painless_dynamic |   457.352 |     ms |
    |  100th percentile service time |     painless_dynamic |   459.474 |     ms |

    ----------------------------------
    [INFO] SUCCESS (took 2634 seconds)
    ----------------------------------


下一步
----------

现在你可以查看 :doc:`怎样执行基准测试 </race>` 以了解如何解读 :doc:`总结报告 </summary_report>` 中的信息，或者开始 :doc:`定义你自己的基准测试 </adding_tracks>` 。查看 :doc:`技巧和窍门 </recipes>` 会帮助你了解怎么处理Rally中遇到的具体问题。

还可以通过执行 ``esrally --help`` 来查看可用的选项，查看 :doc:`命令行参考</command_line_reference>` 以获得每个参数的更多细节解释。

