术语表
========

译注："Rally"是拉力赛的意思，Elasticsearch Rally 把基准测试比作汽车拉力赛，下面这些术语都是从汽车拉力赛借鉴过来的。
 

.. glossary::

    track
        直译为“赛道”。:doc:`track </track>` 用来描述基于特定数据集的一个或者多个基准测试场景。它会定义例如涉及到的索引库、数据文件以及被调用的操作。可以通过 ``esrally list tracks`` 获取可用的 track 列表。虽然 Rally 提供了一些开箱即用的 track，你可以基于你自己的数据 :doc:`创建你的 track</adding_tracks>` 

    challenge
        直译为“挑战”。一个 challenge 描述一个基准测试场景，比如通过4个客户端以最大吞吐量索引文档，同时每秒从另外两个客户端发起10个短语查询。场景(challenge)通常被定义在 track 的内容中。通过 ``esrally list tracks`` 可以看到相应 track 可用的场景(challenge)。

    car
        直译为“赛车”。一个 :doc:`car </car>` 指一个用来基准测试的 Elasticsearch 集群(配置)。比如自定义的配置，指定一个堆大小或者自定义的日志配置(的 Elasticsearch 集群配置)。通过 ``esrally list cars`` 获取可用配置(Elasticsearch 集群)的列表。

    telemetry
        :doc:`Telemetry </telemetry>` 用来收集被基准测试的 Elasticsearch 集群(car)的指标，比如 CPU 使用率或者索引库大小。

    race
        直译为“比赛”。一次 :doc:`race </race>` 是指一次基准测试。另一个名字是"benchmarking trial"(译为“基准测试”)。在一次基准测试中，Rally 会基于一个数据集对一个Elasticsearch集群做一个场景的基准测试。

    tournament
        直译为“锦标赛”。一次 tournament 是对于两个 race 进行一次结果对比。可以使用 Rally 的 :doc:`锦标赛模式 </tournament>` 来做。

