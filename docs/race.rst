����һ����׼����: Races
==================================

��ע�����鿪ʼ֮ǰ�ȿ� :doc:`����� </glossary>`, ����������и��˽⡣��race������track������challenge����Щ���������������о�������ԭ��(������)��

����
----------

��Rally�У�һ����race����һ�λ�׼����ʵ���ִ�С����������Ļ�׼������ѡ��ͬ�Ļ�׼���Գ���( :doc:`tracks </track>` )

List Tracks
-----------

�ӻ�ȡ���õ�tracks��ʼ::

    esrally list tracks

����ʾ�����б�::

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

ǰ����չʾÿ�� track �����ƺ�������һ�� track ���ƶ�һ������ challenges ������Ҫ�����еĸ�������

����һ����׼����
-----------------------

.. note::
    ��Ҫ�� root �û����� Rally����Ϊ Elasticsearch ������ root Ȩ��������

����Ҫ���� track �� challenge ������һ����׼���ԡ�����::

    esrally --distribution-version=6.0.0 --track=geopoint --challenge=append-fast-with-conflicts

Rally �������� track �����л�׼���ԡ������֮ǰû�������� Rally�����������������µ����::

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


���л�׼������Ҫһ��ʱ�䣬�����ĵȴ���

����׼���Խ�����Rally ����������չʾ�ܽᱨ��::

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
    �����ͨ��ָ�������в��� ``--report-file=/path/to/your/report.md`` ���ѱ��汣�浽�ļ���������ͨ�������в��� ``--report-format=csv`` �����汣���CSV��ʽ��

Rally �ո�����Щʲô��

* �����غ�������Elasticsearch 6.0.0
* �������� geopoint track �������
* ��������һ��ʵ�ʵĻ�׼����
* ��󱨸��˽��

�������� Rally ���еĲ������뿴 `geopoint track �淶 <https://github.com/elastic/rally-tracks/blob/5/geopoint/track.json>`_ ���߿�ʼ :doc:`�������Լ��Ļ�׼���� </adding_tracks>` ����Ҳ�������� Rally :doc:`�洢�������ݵ�Elasticsearch </configuration>` ��Ȼ�������ͨ��Kibana�������������������ϣ�� :doc:`�޸�Elasticsearch���� </car>` ��

