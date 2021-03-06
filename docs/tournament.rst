对比结果: Tournaments
===========================

假设，我们需要分析一个性能改进的影响。

首先，我们需要一个基线测量。比如::

    esrally --track=pmc --revision=latest --user-tag="intention:baseline_github_1234"

上面我们基于 Elasticsearch 最新源码做了一次基线测量。我们可以使用命令行参数 ``--user-tag`` 设置一个键值对来记录测试的意图。

然后我们实施修改，最后我们希望运行另一个基准测试来看看这些修改对性能的影响。如果是这样，我们不希望 Rally 切换我们的代码树(译注：希望 Rally 使用当前被修改过的代码，不希望使用其他标准版本的代码)，从而我们应该指定伪修订版本 ``current``:: 

    esrally --track=pmc --revision=current --user-tag="intention:reduce_alloc_1234"

在运行了两个基准测试之后，我们希望知道性能影响。通过 Rally ，我们可以很简单的分析两个基准测试的结果差异。首先，我们需要通过 ``esrally list races`` 来发现我们能够比较的基准测试::

    dm@io:~ $ esrally list races

        ____        ____
       / __ \____ _/ / /_  __
      / /_/ / __ `/ / / / / /
     / _, _/ /_/ / / / /_/ /
    /_/ |_|\__,_/_/_/\__, /
                    /____/
    Recent races:

    Race Timestamp    Track    Track Parameters   Challenge            Car       User Tag
    ----------------  -------  ------------------ -------------------  --------  ------------------------------
    20160518T122341Z  pmc                         append-no-conflicts  defaults  intention:reduce_alloc_1234
    20160518T112057Z  pmc                         append-no-conflicts  defaults  intention:baseline_github_1234
    20160518T101957Z  pmc                         append-no-conflicts  defaults


我们可以看到 User Tag 来帮助我们识别基准测试（上面通过 ``--user-tag`` 设置的）。我们希望比较最近两次基准测试，需要提供两次测试的时间戳::

    dm@io:~ $ esrally compare --baseline=20160518T112057Z --contender=20160518T112341Z

        ____        ____
       / __ \____ _/ / /_  __
      / /_/ / __ `/ / / / / /
     / _, _/ /_/ / / / /_/ /
    /_/ |_|\__,_/_/_/\__, /
                    /____/

    Comparing baseline
      Race timestamp: 2016-05-18 11:20:57
      Challenge: append-no-conflicts
      Car: defaults

    with contender
      Race timestamp: 2016-05-18 12:23:41
      Challenge: append-no-conflicts
      Car: defaults

    ------------------------------------------------------
        _______             __   _____
       / ____(_)___  ____ _/ /  / ___/_________  ________
      / /_  / / __ \/ __ `/ /   \__ \/ ___/ __ \/ ___/ _ \
     / __/ / / / / / /_/ / /   ___/ / /__/ /_/ / /  /  __/
    /_/   /_/_/ /_/\__,_/_/   /____/\___/\____/_/   \___/
    ------------------------------------------------------
                                                      Metric    Baseline    Contender               Diff
    --------------------------------------------------------  ----------  -----------  -----------------
                            Min Indexing Throughput [docs/s]       19501        19118  -383.00000
                         Median Indexing Throughput [docs/s]       20232      19927.5  -304.45833
                            Max Indexing Throughput [docs/s]       21172        20849  -323.00000
                                         Indexing time [min]     55.7989       56.335    +0.53603
                                            Merge time [min]     12.9766      13.3115    +0.33495
                                          Refresh time [min]     5.20067      5.20097    +0.00030
                                            Flush time [min]   0.0648667    0.0681833    +0.00332
                                   Merge throttle time [min]    0.796417     0.879267    +0.08285
                   Query latency term (50.0 percentile) [ms]     2.10049      2.15421    +0.05372
                   Query latency term (90.0 percentile) [ms]     2.77537      2.84168    +0.06630
                  Query latency term (100.0 percentile) [ms]     4.52081      5.15368    +0.63287
            Query latency country_agg (50.0 percentile) [ms]     112.049      110.385    -1.66392
            Query latency country_agg (90.0 percentile) [ms]     128.426      124.005    -4.42138
           Query latency country_agg (100.0 percentile) [ms]     155.989      133.797   -22.19185
                 Query latency scroll (50.0 percentile) [ms]     16.1226      14.4974    -1.62519
                 Query latency scroll (90.0 percentile) [ms]     17.2383      15.4079    -1.83043
                Query latency scroll (100.0 percentile) [ms]     18.8419      18.4241    -0.41784
     Query latency country_agg_cached (50.0 percentile) [ms]     1.70223      1.64502    -0.05721
     Query latency country_agg_cached (90.0 percentile) [ms]     2.34819      2.04318    -0.30500
    Query latency country_agg_cached (100.0 percentile) [ms]     3.42547      2.86814    -0.55732
                Query latency default (50.0 percentile) [ms]     5.89058      5.83409    -0.05648
                Query latency default (90.0 percentile) [ms]     6.71282      6.64662    -0.06620
               Query latency default (100.0 percentile) [ms]     7.65307       7.3701    -0.28297
                 Query latency phrase (50.0 percentile) [ms]     1.82687      1.83193    +0.00506
                 Query latency phrase (90.0 percentile) [ms]     2.63714      2.46286    -0.17428
                Query latency phrase (100.0 percentile) [ms]     5.39892      4.22367    -1.17525
                                Median CPU usage (index) [%]     668.025       679.15   +11.12499
                                Median CPU usage (stats) [%]      143.75        162.4   +18.64999
                               Median CPU usage (search) [%]       223.1        229.2    +6.10000
                                      Total Young Gen GC [s]      39.447       40.456    +1.00900
                                        Total Old Gen GC [s]       7.108        7.703    +0.59500
                                             Index size [GB]     3.25475      3.25098    -0.00377
                                        Totally written [GB]     17.8434      18.3143    +0.47083
                                 Heap used for segments [MB]     21.7504      21.5901    -0.16037
                               Heap used for doc values [MB]     0.16436      0.13905    -0.02531
                                    Heap used for terms [MB]     20.0293      19.9159    -0.11345
                                    Heap used for norms [MB]    0.105469    0.0935669    -0.01190
                                   Heap used for points [MB]    0.773487     0.772155    -0.00133
                                   Heap used for points [MB]    0.677795     0.669426    -0.00837
                                               Segment count         136          121   -15.00000
                         Indices Stats(90.0 percentile) [ms]     3.16053      3.21023    +0.04969
                         Indices Stats(99.0 percentile) [ms]     5.29526      3.94132    -1.35393
                        Indices Stats(100.0 percentile) [ms]     5.64971      7.02374    +1.37403
                           Nodes Stats(90.0 percentile) [ms]     3.19611      3.15251    -0.04360
                           Nodes Stats(99.0 percentile) [ms]     4.44111      4.87003    +0.42892
                          Nodes Stats(100.0 percentile) [ms]     5.22527      5.66977    +0.44450
