创建自定义的tracks
======================

.. note::
    查看 :doc:`track参考 </track>` 以获取track结构更多信息


track例子
-------------

让我们来一步步创建一个track的例子。首先，我们需要一些数据。有很多有趣的可以做新基准测试的公开数据集，我们也有 `储备可添加的基准测试数据 <https://github.com/elastic/rally-tracks/issues>`_ 。

`Geonames <http://www.geonames.org/>`_ 提供地理数据，基于 `creative commons 许可证 <http://creativecommons.org/licenses/by/3.0/>`_ 。我们将下载 `allCountries.zip <http://download.geonames.org/export/dump/allCountries.zip>`_ (约300MB)，解压，检查生成的 ``allCountries.txt`` 文件。

你很快会发现 ``allCountries.txt`` 这个文件是通过制表符分隔的，但是我们需要JSON格式的数据以通过bulk-index API导入到Elasticsearch。我门可以通过一个小脚本来做个转换::

    import json
    import csv
    
    cols = (('geonameid', 'int'),
           ('name', 'string'),
           ('asciiname', 'string'),
           ('alternatenames', 'string'),
           ('latitude', 'double'),
           ('longitude', 'double'),
           ('feature_class', 'string'),
           ('feature_code', 'string'),
           ('country_code', 'string'),
           ('cc2', 'string'),
           ('admin1_code', 'string'),
           ('admin2_code', 'string'),
           ('admin3_code', 'string'),
           ('admin4_code', 'string'),
           ('population', 'long'),
           ('elevation', 'int'),
           ('dem', 'string'),
           ('timezone', 'string'))
           
    with open('allCountries.txt') as f:
     while True:
       line = f.readline()
       if line == '':
         break
       tup = line.strip().split('\t')
       d = {}
       for i in range(len(cols)):
         name, type = cols[i]
         if tup[i] != '':
           if type in ('int', 'long'):
             d[name] = int(tup[i])
           elif type == 'double':
             d[name] = float(tup[i])
           else:
             d[name] = tup[i]
    
       print(json.dumps(d))


我们可以这样调用脚本生成JSON文件 ``python3 toJSON.py > documents.json`` 。

下一步我们需要压缩这个JSON文件 ``bzip2 -9 -c documents.json > documents.json.bz2`` 。把这个数据文件上传到一个公开有效的地方。在这个例子中，我们选择放在 ``http://benchmarks.elastic.co/corpora/geonames``

对于初始局部测试，你可以把这个数据文件放在Rally的数据目录，``~/.rally/benchmarks/data`` 路径下。在这个例子中，为了让Rally能够找到，你需要将这个针对“geonames” track的数据文件放在 ``~/.rally/benchmarks/data/geonames`` 。另外，在运行Rally的时候，为了避免Rally尝试下载其他基准测试数据，你需要指定 ``--offline`` 参数。

下一步我们需要为上面的文档写一个映射信息文件（mapping file）。关于怎么写一个映射信息文件，参考 `the Elasticsearch documentation on mappings <https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html>`_ 和 `example mapping file <https://github.com/elastic/rally-tracks/blob/master/geonames/mappings.json>`_ 。将这个映射信息文件提交到你的 ``rally-tracks`` 代码库一个专用的目录里。这个代码库存放在 ``~/.rally/benchmarks/tracks/default`` ，将这个映射信息文件放在 ``~/.rally/benchmarks/tracks/default/geonames`` 。

track代码库是用git管理的，所以通过执行 ``git checkout master`` 来确保是使用的主线。然后在映射信息文件同级添加一个新的JSON文件，这个文件名为 “track.json”，下面是实际的规范／说明 ::

    {
      "meta": {
        "short-description": "Standard benchmark in Rally (8.6M POIs from Geonames)",
        "description": "This test indexes 8.6M documents (POIs from Geonames, total 2.8 GB json) using 8 client threads and 5000 docs per bulk request against Elasticsearch",
        "data-url": "http://benchmarks.elasticsearch.org.s3.amazonaws.com/corpora/geonames"
      },
      "indices": [
        {
          "name": "geonames",
          "types": [
            {
              "name": "type",
              "mapping": "mappings.json",
              "documents": "documents.json.bz2",
              "document-count": 8647880,
              "compressed-bytes": 197857614,
              "uncompressed-bytes": 2790927196
            }
          ]
        }
      ],
      "operations": [
        {
          "name": "index",
          "type": "index",
          "bulk-size": 5000
        },
        {
          "name": "force-merge",
          "type": "force-merge"
        },
        {
          "name": "query-match-all",
          "operation-type": "search",
          "body": {
            "query": {
              "match_all": {}
            }
          }
        },
      ],
      "challenges": [
        {
          "name": "append-no-conflicts",
          "description": "",
          "index-settings": {
            "index.number_of_replicas": 0
          },
          "schedule": [
            {
              "operation": "index",
              "warmup-time-period": 120,
              "clients": 8
            },
            {
              "operation": "force-merge",
              "clients": 1
            },
            {
              "operation": "query-match-all",
              "clients": 8,
              "warmup-iterations": 1000,
              "iterations": 1000,
              "target-throughput": 100
            }
          ]
        }
      ]
    }

最后，你需要提交你的变更：``git commit -a -m "Add geonames track"`` 。

有几件事情要注意:

* Rally 假设 the challenge 需要按默认的方式（“append-no-conflicts”）执行。如果你希望执行不同的challenge，需要提供命令行参数 ``--challenge=YOUR_CHALLENGE_NAME`` 。
* 你可以随意添加查询动作。我们基于 `官方Python Elasticsearch client <http://elasticsearch-py.readthedocs.org/>`_ 来实现查询。
* ``types`` 属性下面的数字需要是整形的，用于提供报告进度。

.. note::

    We have defined a `JSON schema for tracks <https://github.com/elastic/rally/blob/master/esrally/resources/track-schema.json>`_ which you can use to check how to define your track. You should also check the tracks provided by Rally for inspiration.

当你调用 ``esrally list tracks``, 新的track就会显示出来::

    dm@io:~ $ esrally list tracks
    
        ____        ____
       / __ \____ _/ / /_  __
      / /_/ / __ `/ / / / / /
     / _, _/ /_/ / / / /_/ /
    /_/ |_|\__,_/_/_/\__, /
                    /____/
    Available tracks:
    
    Name        Description                                               Challenges
    ----------  --------------------------------------------------------  -------------------
    geonames    Standard benchmark in Rally (8.6M POIs from Geonames)     append-no-conflicts

恭喜你，你已经创建了你的第一个track！你可以通过调用 ``esrally --track=geonames --offline`` 来测试（可以把“geonames”换成你的track名称），可以通过调用 ``esrally --track=geonames --challenge=append-fast-with-conflicts --offline`` 来运行某个指定的challenges。

如果你希望向社区共享你的track，请继续读下去。

How to contribute a track
-------------------------

First of all, please read Rally's `contributors guide <https://github.com/elastic/rally/blob/master/CONTRIBUTING.md>`_.

If you want to contribute your track, follow these steps:

1. Create a track JSON file and mapping files as described above and place them in a separate folder in the ``rally-tracks`` repository. Please also add a README file in this folder which contains licensing information (respecting the licensing terms of the source data). Note that pull requests for tracks without a license cannot be accepted.
2. Upload the associated data so they can be publicly downloaded via HTTP. The data should be compressed either as .bz2 (recommended) or as .zip.
3. Create a pull request in the `rally-tracks Github repo <https://github.com/elastic/rally-tracks>`_.

高级话题
---------------

模版语言
^^^^^^^^^^^^^^^^^

Rally uses `Jinja2 <http://jinja.pocoo.org/docs/dev/>`_ as template language. This allows you to use Jinja2 expressions in track files.


扩展点
""""""""""""""""

Rally also provides a few extension points to Jinja2:

* ``now``: This is a global variable that represents the current date and time when the template is evaluated by Rally.
* ``days_ago()``: This is a `filter <http://jinja.pocoo.org/docs/dev/templates/#filters>`_ that you can use for date calculations.

You can find an example in the logging track::

    {
      "name": "range",
        "index": "logs-*",
        "type": "type",
        "body": {
          "query": {
            "range": {
              "@timestamp": {
                "gte": "now-{{'15-05-1998' | days_ago(now)}}d/d",
                "lt": "now/d"
              }
            }
          }
        }
      }
    }

The data set that is used in the logging track starts on 26-04-1998 but we want to ignore the first few days for this query, so we start on 15-05-1998. The expression ``{{'15-05-1998' | days_ago(now)}}`` yields the difference in days between now and the fixed start date and allows us to benchmark time range queries relative to now with a predetermined data set.

自定义参数来源
^^^^^^^^^^^^^^^^^^^^^^^^

.. note::

    This is a rather new feature and the API may change! However, the effort to use custom parameter sources is very low.

Consider the following operation definition::

    {
      "name": "term",
      "operation-type": "search",
      "body": {
        "query": {
          "term": {
            "body": "physician"
          }
        }
      }
    }

This query is defined statically in the track specification but sometimes you may want to vary parameters, e.g. search also for "mechanic" or "nurse". In this case, you can write your own "parameter source" with a little bit of Python code.

First, define the name of your parameter source in the operation definition::

    {
      "name": "term",
      "operation-type": "search",
      "param-source": "my-custom-term-param-source"
      "professions": ["mechanic", "physician", "nurse"]
    }

Rally will recognize the parameter source and looks then for a file ``track.py`` in the same directory as the corresponding JSON file. This file contains the implementation of the parameter source::

    import random


    def random_profession(indices, params):
        # you must provide all parameters that the runner expects
        return {
            "body": {
                "query": {
                    "term": {
                        "body": "%s" % random.choice(params["professions"])
                    }
                }
            },
            "index": None,
            "type": None,
            "use_request_cache": False
        }

    def register(registry):
        registry.register_param_source("my-custom-term-param-source", random_profession)

The example above shows a simple case that is sufficient if the operation to which your parameter source is applied is idempotent and it does not matter whether two clients execute the same operation.

The function ``random_profession`` is the actual parameter source. Rally will bind the name "my-custom-term-param-source" to this function by calling ``register``. ``register`` is called by Rally before the track is executed.

The parameter source function needs to declare the two parameters ``indices`` and ``params``. `indices` contains all indices of this track and ``params`` contains all parameters that have been defined in the operation definition in ``track.json``. We use it in the example to read the professions to choose.

If you need more control, you need to implement a class. The example above, implemented as a class looks as follows::

    import random


    class TermParamSource:
        def __init__(self, indices, params):
            self._indices = indices
            self._params = params

        def partition(self, partition_index, total_partitions):
            return self

        def size(self):
            return 1

        def params(self):
            # you must provide all parameters that the runner expects
            return {
                "body": {
                    "query": {
                        "term": {
                            "body": "%s" % random.choice(self._params["professions"])
                        }
                    }
                },
                "index": None,
                "type": None,
                "use_request_cache": False
            }


    def register(registry):
        registry.register_param_source("my-custom-term-param-source", TermParamSource)


Let's walk through this code step by step:

* Note the method ``register`` where you need to bind the name in the track specification to your parameter source implementation class similar to the simple example.
* The class ``TermParamSource`` is the actual parameter source and needs to fulfill a few requirements:

    * It needs to have a constructor with the signature ``__init__(self, indices, params)``. You don't need to store these parameters if you don't need them.
    * ``partition(self, partition_index, total_partitions)`` is called by Rally to "assign" the parameter source across multiple clients. Typically you can just return ``self`` but in certain cases you need to do something more sophisticated. If each clients needs to act differently then you can provide different parameter source instances here.
    * ``size(self)``: This method is needed to help Rally provide a proper progress indication to users if you use a warmup time period. For bulk indexing, this would return the number of bulks (for a given client). As searches are typically executed with a pre-determined amount of iterations, just return ``1`` in this case.
    * ``params(self)``: This method needs to return a dictionary with all parameters that the corresponding "runner" expects. For the standard case, Rally provides most of these parameters as a convenience, but here you need to define all of them yourself. This method will be invoked once for every iteration during the race. We can see that we randomly select a profession from a list which will be then be executed by the corresponding runner.

.. note::

    Be aware that ``params(self)`` is called on a performance-critical path so don't do anything in this method that takes a lot of time (avoid any I/O). For searches, you should usually throttle throughput anyway and there it does not matter that much but if the corresponding operation is run without throughput throttling, please double-check that you did not introduce a bottleneck in the load test driver with your custom parameter source.

In the implementation of custom parameter sources you can access the Python standard API. Using any additional libraries is not supported.

You can also implement your parameter sources and runners in multiple Python files but the main entry point is always ``track.py``. The root package name of your plugin is the name of your track.

Custom runners
^^^^^^^^^^^^^^

You cannot only define custom parameter sources but also custom runners. Runners execute an operation against Elasticsearch. Out of the box, Rally supports the following operations:

* Bulk indexing
* Force merge
* Searches
* Index stats
* Nodes stats

If you want to use any other operation, you can define a custom runner. Consider, we want to use the percolate API with an older version of Elasticsearch (note that it has been replaced by the percolate query in Elasticsearch 5.0). To achieve this, we c

In track.json specify an operation with type "percolate" (you can choose this name freely)::

    {
      "name": "percolator_with_content_google",
      "operation-type": "percolate",
      "body": {
        "doc": {
          "body": "google"
        },
        "track_scores": true
      }
    }


Then create a file track.py next to track.json and implement the following two functions::

    def percolate(es, params):
        es.percolate(
            index="queries",
            doc_type="content",
            body=params["body"]
        )


    def register(registry):
        registry.register_runner("percolate", percolate)


The function ``percolate`` is the actual runner and takes the following parameters:

* ``es``, which is the Elasticsearch Python client
* ``params`` which is a dict of parameters provided by its corresponding parameter source. Treat this parameter as read only and do not attempt to write to it.

This function can return either:

* Nothing at all. Then Rally will assume that by default ``1`` and ``"ops"`` (see below)
* A tuple of ``weight`` and a ``unit``, which is usually ``1`` and ``"ops"``. If you run a bulk operation you might return the bulk size here, for example in number of documents or in MB. Then you'd return for example ``(5000, "docs")`` Rally will use these values to store throughput metrics.
* A ``dict`` with arbitrary keys. If the ``dict`` contains the key ``weight`` it is assumed to be numeric and chosen as weight as defined above. The key ``unit`` is treated similarly. All other keys are added to the ``meta`` section of the corresponding service time and latency metrics records.

Similar to a parameter source you also need to bind the name of your operation type to the function within ``register``.

.. note::

    You need to implement ``register`` just once and register all parameter sources and runners there.


Running tasks in parallel
^^^^^^^^^^^^^^^^^^^^^^^^^

Rally supports running tasks in parallel with the ``parallel`` element. Below you find a few examples that show how it should be used:

In the simplest case, you let Rally decide the number of clients needed to run the parallel tasks::


        {
          "parallel": {
            "warmup-iterations": 1000,
            "iterations": 1000,
            "tasks": [
              {
                "operation": "default",
                "target-throughput": 50
              },
              {
                "operation": "term",
                "target-throughput": 200
              },
              {
                "operation": "phrase",
                "target-throughput": 200
              },
              {
                "operation": "country_agg_uncached",
                "target-throughput": 50
              }
            ]
          }
        }
      ]
    }

Rally will determine that four clients are needed to run each task in a dedicated client.

However, you can also explicitly limit the number of clients::

        {
          "parallel": {
            "clients": 2,
            "warmup-iterations": 1000,
            "iterations": 1000,
            "tasks": [
              {
                "operation": "default",
                "target-throughput": 50
              },
              {
                "operation": "term",
                "target-throughput": 200
              },
              {
                "operation": "phrase",
                "target-throughput": 200
              },
              {
                "operation": "country_agg_uncached",
                "target-throughput": 50
              }
            ]
          }
        }

This will run the four tasks with just two clients. You could also specify more clients than there are tasks but these will then just idle.

You can also specify a number of clients on sub tasks explicitly (by default, one client is assumed per subtask). This allows to define a weight for each client operation. Note that you need to define the number of clients also on the ``parallel`` parent element, otherwise Rally would determine the number of totally needed clients again on its own::

        {
          "parallel": {
            "clients": 3,
            "warmup-iterations": 1000,
            "iterations": 1000,
            "tasks": [
              {
                "operation": "default",
                "target-throughput": 50
              },
              {
                "operation": "term",
                "target-throughput": 200
              },
              {
                "operation": "phrase",
                "target-throughput": 200,
                "clients": 2
              },
              {
                "operation": "country_agg_uncached",
                "target-throughput": 50
              }
            ]
          }
        }

This will ensure that the phrase query will be executed by two clients. All other ones are executed by one client.

.. warning::
    You cannot nest parallel tasks.

Custom Track Repositories
^^^^^^^^^^^^^^^^^^^^^^^^^

Rally provides a default track repository that is hosted on `Github <https://github.com/elastic/rally-tracks>`_. You can also add your own track repositories although this requires a bit of additional work. First of all, track repositories need to be managed by git. The reason is that Rally can benchmark multiple versions of Elasticsearch and we use git branches in the track repository to determine the best match for each track. The versioning scheme is as follows:

* The `master` branch needs to work with the latest `master` branch of Elasticsearch.
* All other branches need to match the version scheme of Elasticsearch, i.e. ``MAJOR.MINOR.PATCH-SUFFIX`` where all parts except ``MAJOR`` are optional.

Rally implements a fallback logic so you don't need to define a branch for each patch release of Elasticsearch. For example:

* The branch `6.0.0-alpha1` will be chosen for the version ``6.0.0-alpha1`` of Elasticsearch.
* The branch `5` will be chosen for all versions for Elasticsearch with the major version 5, e.g. ``5.0.0``, ``5.1.3`` (provided there is no specific branch).

Rally tries to use the branch with the best match to the benchmarked version of Elasticsearch.

Creating a new track repository
"""""""""""""""""""""""""""""""

All track repositories are located in ``~/.rally/benchmarks/tracks``. If you want to add a dedicated track repository, called ``private`` follow these steps::

    cd ~/.rally/benchmarks/tracks
    mkdir private
    cd private
    git init
    # add your track now
    git commit -a -m "Initial commit"


If you also have a remote for this repository, open ``~/.rally/rally.ini`` in your editor of choice and add the following line in the section ``tracks``, otherwise just skip this step::

    private.url = <<URL_TO_YOUR_ORIGIN>>

Rally will then automatically update the local tracking branches before the benchmark starts.

You can now verify that everything works by listing all tracks in this track repository::

    esrally list tracks --track-repository=private

This shows all tracks that are available on the ``master`` branch of this repository. Suppose you only created tracks on the branch ``2`` because you're interested in the performance of Elasticsearch 2.x, then you can specify also the distribution version::

    esrally list tracks --track-repository=private --distribution-version=2.0.0


Rally will follow the same branch fallback logic as described above.

Adding an already existing track repository
"""""""""""""""""""""""""""""""""""""""""""

If you want to add a track repository that already exists, just open ``~/.rally/rally.ini`` in your editor of choice and add the following line in the section ``tracks``::

    your_repo_name.url = <<URL_TO_YOUR_ORIGIN>>

After you have added this line, have Rally list the tracks in this repository::

    esrally list tracks --track-repository=your_repo_name

