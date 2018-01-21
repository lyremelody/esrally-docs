Rally |release|
===============


.. ifconfig:: release.endswith('.dev0')

    .. warning::
        
        这个是Rally当前开发版文档。
        你可以看看 `最新稳定版文档 <https://esrally.readthedocs.io/en/stable/>`_


注意
------------------------------------

这个是esrally官方文档的翻译，原文请移步 `Rally 官方文档 <http://esrally.readthedocs.io/en/latest/>`_

如果发现有翻译上的问题，可邮件给我 lyremelody AT 163 dot com。谢谢～


Rally |release|
-----------------

如果你希望对Elasticsearch做基准测试，那么Rally适合你。它能够帮助你完成下面这些任务：

* 为基准测试安装和卸载Elasticsearch集群
* 管理基准测试的数据以及可以支持多个Elasticsearch版本
* 运行基准测试并且记录结果
* 用附加遥测设备的方式找出性能问题（Finding performance problems by attaching so-called telemetry devices）
* 比较性能结果


为了让基准测试的数据具有可重复性，我们在Rally上付出了相当大的努力

通常，Rally能够支持Elasticsearch 1.x及后续所有版本。但 :doc:`使用插件进行基准测试 </elasticsearch_plugins>` 和 :ref:`基于源码构建基准测试  <pipelines_from-sources-complete>` 只支持 Elasticsearch 5.0及后续版本。


Getting Help or Contributing to Rally
-------------------------------------

Use our `Discuss forum <https://discuss.elastic.co/c/elasticsearch/rally>`_ to provide feedback or ask questions about Rally. Please see our `contribution guide <https://github.com/elastic/rally/blob/master/CONTRIBUTING.md>`_ on guidelines for contributors.

Source Code
-----------

Rally's source code is available on `Github <https://github.com/elastic/rally>`_. You can also check the `changelog <https://github.com/elastic/rally/releases>`_ and the `roadmap <https://github.com/elastic/rally/milestones>`_ there.

.. toctree::
   :caption: Getting Started with Rally
   :maxdepth: 2

   quickstart
   install
   configuration
   race
   tournament
   recipes

.. toctree::
   :caption: Extending Rally
   :maxdepth: 2

   adding_tracks
   developing

.. toctree::
   :caption: Reference Documentation
   :maxdepth: 2

   command_line_reference
   offline
   track
   car
   elasticsearch_plugins
   telemetry
   rally_daemon
   pipelines
   metrics
   summary_report

.. toctree::
   :caption: Additional Information
   :maxdepth: 2

   migrate
   faq
   glossary
   community



License
-------

This software is licensed under the Apache License, version 2 ("ALv2"), quoted below.

Copyright 2015-|year| Elasticsearch <https://www.elastic.co>

Licensed under the Apache License, Version 2.0 (the "License"); you may not
use this file except in compliance with the License. You may obtain a copy of
the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
License for the specific language governing permissions and limitations under
the License.
