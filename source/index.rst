.. ollama documentation master file, created by
   sphinx-quickstart on Thu May 22 16:20:39 2025.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

ollama Linux部署与LLM调用
==================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

本教程基于Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-213-generic x86_64)系统，使用离线部署方式进行ollama部署，能够有效解决
官网下载安装速度巨慢的问题。教程分为3个部分：1.删除旧的ollama环境；2.下载安装包并且进行安装；3.启动ollama服务，调用模型



删除旧的ollama环境
==================

.. * :ref:`genindex`
.. * :ref:`modindex`

.. * :ref:`search`
1.查看ollama环境位置（若存在）
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. sourcecode:: bash

   which ollama

使用该命令后若得到ollama的位置，如/usr/bin/ollama或者/usr/local/bin/ollama，则开始删除操作，若无则可跳过直接进行安装。

2.停止ollama服务
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: bash

   sudo systemctl stop ollama.service

3.删除 Ollama 主程序​
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: bash

   sudo rm /usr/bin/ollama

4.清理数据目录​
>>>>>>>>>>>>>>>>>>

.. code-block:: bash

   sudo rm -rf /usr/share/ollama/  # 默认安装目录
   sudo rm -rf ~/.ollama  # 用户数据（模型、配置等）


5.移除 systemd 服务（如存在）​
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. code-block:: bash

   sudo systemctl disable ollama.service
   sudo rm /etc/systemd/system/ollama.service
   sudo systemctl daemon-reload


6.验证卸载完成
>>>>>>>>>>>>>>>>>>>>>

.. code-block:: bash

   which ollama  # 应无输出
   ls /usr/bin/ollama  # 检查文件是否已删除

下载安装包，进行安装
=====================

1.下载安装包
>>>>>>>>>>>>>>>>>>
从下载地址 https://github.com/ollama/ollama/releases 中选择你需要的ollama版本，本人使用0.7.0，在assets处下载压缩包。
**此处需注意**：在cmd窗口中查看服务器CPU的型号

.. code-block:: bash

   lscpu

x86_64 CPU选择下载文件ollama-linux-amd64.tgz，arm架构CPU选择下载ollama-linux-arm64.tgz。
下载完成后上传至服务器

2.解压
>>>>>>>>>>>>>>>>>
 
将安装脚本 https://github.com/ollama/ollama/blob/main/scripts/install.sh 放到和tgz压缩包同一个路径下，并且执行命令解压安装包

.. code-block:: bash

   tar -C /usr -xzf ollama-linux-amd64.tgz


3. 修改脚本
>>>>>>>>>>>>>>>>

首先，由于我们使用的是安装包部署，因此要将原来的下载链接注释掉

.. code-block:: bash

   status "Downloading ollama..."
   ## 在install.sh的第65行
   #curl --fail --show-error --location --progress-bar -o $TEMP_DIR/ollama "https://ollama.com/download/ollama-linux-${ARCH}${VER_PARAM}"

其次，第二处修改，修改ollama安装目录

.. code-block:: bash

   status "Installing ollama to $BINDIR..."
   $SUDO install -o0 -g0 -m755 -d $BINDIR
   ## 在install.sh的第73行
   #$SUDO install -o0 -g0 -m755 $TEMP_DIR/ollama $BINDIR/ollama
   $SUDO install -o0 -g0 -m755 ./ollama-linux-amd64  $BINDIR/ollama



4.执行安装脚本
>>>>>>>>>>>>>>>>>>

.. code-block:: bash

   bash install.sh

如果报错误权限不足，执行

.. code-block:: bash 

   chmod +x install.sh

验证安装是否完成

.. code-block:: bash
   ollama -v

输出如下版本号则说明成功

.. code-block:: bash

   ollama version is 0.7.0


调用模型
=============

重启ollama服务，查看服务启动状态

.. code-block:: bash

   sudo systemctl restart ollama
   sudo systemctl status ollama

输出active (running)则启动成功，接下来调用模型

.. code-block:: bash

   ollama run qwen3:30b-a3b   

输出

.. code-block:: bash

   pulling manifest                  
   verifying sha256 digest 
   writing manifest 
   success 

大功告成

