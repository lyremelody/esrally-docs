离线使用
=============

在某些企业环境中，服务无法访问互联网。你仍然可以在这样的环境中使用Rally，本页总结了你需要的所有信息。

安装和配置
------------------------------

我们提供了一个特殊的离线安装包。详细说明参考 :ref:`离线安装向导 <install_offline-install>` 。在安装后，你可以按照正常的 :doc:`配置程序 </configuration>` 来操作。

命令行使用
------------------

Rally 启动时会自动检测到没有互联网连接可用，并打印以下警告::

    [WARNING] No Internet connection detected. Automatic download of track data sets etc. is disabled.

它通过尝试连接 ``github.com`` 来检测(互联网是否可用)。你可以通过明确指定 ``--offline`` 来禁用这个(检测)。

使用 tracks
------------

一个 Rally track 描述一个基准测试场景。你可以写自己的 track 或者使用 Rally 提供的 track。对于前者，Rally 将只提供一个离线环境。对于后者，Rally 将会正常的从互联网下载 track 和它关联的数据。如果你想要在离线环境使用 Rally 的某个标准 track，你需要先在能访问互联网的机器上下载所有相关文件，并拷贝到目标机器上。

在能访问互联网的机器上，使用 `下载脚本 <https://raw.githubusercontent.com/elastic/rally-tracks/master/download.sh>`_ 下载某个 track 的所有数据。例如::

    # downloads the script from Github
    curl -O https://raw.githubusercontent.com/elastic/rally-tracks/master/download.sh
    chmod u+x download.sh
    # download all data for the geonames track
    ./download.sh geonames

这样会下载 geonames track 所有数据，并在当前目录生成一个压缩文件 ``rally-track-data-geonames.tar`` 。把该文件复制到将在目标机器上执行 Rally 的用户的 Home 目录。(比如 ``/home/rally-user``) 。

在目标机器执行::

    cd ~
    tar -xf rally-track-data-geonames.tar

下载脚本不需要在具有互联网访问权限的计算机上安装 Rally，但要求可以使用 ``git`` 和 ``curl`` 。

当你复制数据(到目标机器)后，你可以通过 ``esrally list tracks`` 获取可用的 track 列表。如果一个 track 在这个列表内，它只是意味着 track 描述在本地可用，并不意味着所有的数据可用。

使用 cars
----------

.. note::

    如果你仅仅把 Rally 当作一个负载发生器，你可以跳过这一节。

If you have Rally configure and start Elasticsearch then you also need the out-of-the-box configurations available. Run the following command on a machine with Internet access::

    git clone https://github.com/elastic/rally-teams.git ~/.rally/benchmarks/teams/default
    tar -C ~ -czf rally-teams.tar.gz .rally/benchmarks/teams/default

Copy that file to the target machine(s) and run on the target machine::

    cd ~
    tar -xzf rally-teams.tar.gz

After you've copied the data, you can list the available tracks with ``esrally list cars``.
