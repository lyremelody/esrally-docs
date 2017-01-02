Rally |release|
===============

注意
------------------------------------

这个是esrally官方文档的翻译，原文请移步 `Rally 官方文档 <http://esrally.readthedocs.io/en/latest/>`_


.. ifconfig:: release.endswith('.dev0')

    .. 警告::
        
        这个是Rally当前开发版文档。
        你可以看看 `最新稳定版文档 <//esrally.readthedocs.io/en/stable/>`_?

如果你希望对Elasticsearch做基准测试，那么Rally适合你。它能够帮助你完成下面这些任务：

* 为基准测试安装和卸载Elasticsearch集群
* 管理基准测试的数据以及可以支持多个Elasticsearch版本
* 运行基准测试并且记录结果
* 用附加遥测设备的方式找出性能问题（Finding performance problems by attaching so-called telemetry devices）
* 比较性能结果

为了让基准测试的数据具有可重复性，我们在Rally上付出了相当大的努力。

获取帮助和为Rally投稿
-------------------------------------

使用我们的 `讨论论坛 <https://discuss.elastic.co/c/elasticsearch/rally>`_ 提问和提供关于Rally的反馈。投稿者请参考 `投稿指南 <https://github.com/elastic/rally/blob/master/CONTRIBUTING.md>`_ 

源代码
-----------

Rally的源代码托管在 `Github <https://github.com/elastic/rally>`_ 上.

目录
--------

.. toctree::
   :maxdepth: 1

   quickstart
   install
   configuration
   race
   adding_tracks
   track
   car
   telemetry
   pipelines
   metrics
   tournament
   recipes
   command_line_reference
   developing
   faq
   glossary

许可证
-------------

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
