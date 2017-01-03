Cars
====

"car"在Rally中，是指某个指定的Elasticsearch配置。目前，Rally有一套固定的“cars”，不能改变（译注：car不能改，但是可以修改指定其中的某个来用）。你可以通过调用 ``esrally list cars`` 获取列表::

        ____        ____
       / __ \____ _/ / /_  __
      / /_/ / __ `/ / / / / /
     / _, _/ /_/ / / / /_/ /
    /_/ |_|\__,_/_/_/\__, /
                    /____/

    Available cars:

    Name
    ----------
    defaults
    4gheap
    two_nodes
    verbose_iw

你可以通过参数 ``--car=4gheap`` 来指定car。
