.. _minio-bucket-replication-resynchronize:


====================================
从远程副本重新同步存储桶
====================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

本页中的步骤使用健康的复制远端来重新同步 MinIO 存储桶中的内容。重新同步可用于在副本配置下的 MinIO 部署发生部分或全部数据丢失后进行恢复。

例如，考虑如下所示的 MinIO 主动-主动复制配置：

.. image:: /images/replication/active-active-twoway-replication.svg
   :width: 600px
   :alt: 主动-主动复制在两个远程部署之间同步数据。
   :align: center

重新同步允许使用其中一个参与复制的 MinIO 部署上的健康数据，作为重建另一个部署的源。

重新同步是按存储桶执行的过程。对于远端中每个遭受部分或全部数据丢失的存储桶，都必须重复执行重新同步。

.. admonition:: BC/DR 操作期间的专业支持
   :class: important

   `MinIO SUBNET <https://min.io/pricing?jmp=docs>`__ 用户可以 `登录 <https://subnet.min.io/>`__ 并创建与重新同步相关的新 issue。通过 SUBNET 与 MinIO Engineering 协调，可以确保重新同步成功并恢复正常运行，包括性能测试和健康诊断。

   社区用户可以通过 `MinIO Community Slack <https://slack.min.io>`__ 寻求支持。社区支持仅按尽力而为原则提供，不对响应速度提供 SLA。

.. _minio-bucket-replication-serverside-resynchronize-requirements:

要求
----

MinIO 部署必须在线
~~~~~~~~~~~~~~~~~~

重新同步要求源部署和目标部署都在线，并且能够接受读写操作。源端 *必须* 具备到远端的完整网络连通性。

远端部署可以处于“不健康”状态，即发生了部分或全部数据丢失。只要源端和目标端保持连通，重新同步就可以修复这些数据丢失问题。

重新同步要求现有复制配置
~~~~~~~~~~~~~~~~~~~~~~~~

重新同步要求健康的源部署已经为不健康的目标存储桶配置好了复制配置。此外，重新同步仅适用于使用 :ref:`existing object replication <minio-replication-behavior-existing-objects>` 选项创建的那些复制规则。

使用 :mc:`mc replicate ls` 查看健康源存储桶已配置的复制规则和目标。

复制要求对象加密设置匹配
~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-replication.rst
   :start-after: start-replication-encrypted-objects
   :end-before: end-replication-encrypted-objects

复制要求使用 MinIO 部署
~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-replication.rst
   :start-after: start-replication-minio-only
   :end-before: end-replication-minio-only

复制要求启用版本控制
~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-replication.rst
   :start-after: start-replication-requires-versioning
   :end-before: end-replication-requires-versioning

复制要求对象锁定状态匹配
~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-replication.rst
   :start-after: start-replication-requires-object-locking
   :end-before: end-replication-requires-object-locking

注意事项
--------

重新同步需要时间
~~~~~~~~~~~~~~~~

重新同步是一个后台过程，会持续检查源 MinIO 存储桶中的对象，并在需要时将其复制到远端。完成复制所需的时间会因对象数量和大小、到远端 MinIO 部署的吞吐量以及源 MinIO 部署上的负载而异。由于这些变量的存在，总完成时间通常无法预测。

MinIO 建议在同步完成之前，通过配置负载均衡器或代理，仅将流量导向健康集群。以下命令可帮助了解重新同步状态：

- 在源端执行 :mc-cmd:`mc replicate resync status` 以跟踪重新同步进度。

- 在源端和远端执行 :mc:`mc replicate status` 以跟踪正常复制数据。

- 在源端和远端都运行 ``mc ls -r --versions ALIAS/BUCKET | wc -l``，以验证两端的对象总数和对象版本总数。

在数据丢失后重新同步对象
------------------------

此过程使用现有的 :ref:`MinIO replication configuration <minio-bucket-replication-serverside>`，将缺失数据恢复到参与该配置的某个 MinIO 部署。具体来说，一个健康的 MinIO 部署（即 ``SOURCE``）会将其现有数据同步到不健康的 MinIO 部署（即 ``TARGET``）。

此过程假定 ``SOURCE`` 已存在一个 :ref:`alias <alias>`，并且该别名具有配置复制所需的 :ref:`necessary permissions <minio-bucket-replication-serverside-twoway-permissions>`。

你可以对每个需要重新同步的存储桶重复执行此过程。每个存储桶同时最多只能运行一个复制作业。

1) 列出健康源端上已配置的复制目标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

运行 :mc:`mc replicate ls` 命令，列出健康 ``SOURCE`` 部署上针对需要重新同步的 ``BUCKET`` 已配置的远程目标。

.. code-block:: shell
   :class: copyable

   mc replicate ls SOURCE/BUCKET --json

- 将 ``SOURCE`` 替换为源 MinIO 部署的 :ref:`alias <alias>`。

- 将 ``BUCKET`` 替换为要作为重新同步源的存储桶名称。

输出类似如下：

.. code-block:: shell
   :emphasize-lines: 16

   {
      "op": "",
      "status": "success",
      "url": "",
      "rule": {
         "ID": "cer1tuk9a3p5j68crk60",
         "Status": "Enabled",
         "Priority": 0,
         "DeleteMarkerReplication": {
            "Status": "Enabled"
         },
         "DeleteReplication": {
            "Status": "Enabled"
         },
         "Destination": {
            "Bucket": "arn:minio:replication::UUID:BUCKET"
         },
         "Filter": {
            "And": {},
            "Tag": {}
         },
         "SourceSelectionCriteria": {
            "ReplicaModifications": {
               "Status": "Enabled"
            }
         },
         "ExistingObjectReplication": {
            "Status": "Enabled"
         }
      }
   }

输出中的每个文档都表示一条已配置的复制规则。
``Destination.Bucket`` 字段指定该存储桶上某条规则对应的 ARN。
找出你希望从中重新同步对象的存储桶对应的正确 ARN。

2) 启动重新同步过程
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

运行 :mc-cmd:`mc replicate resync start` 命令以开始重新同步过程：

.. code-block:: shell
   :class: copyable

   mc replicate resync start --remote-bucket "arn:minio:replication::UUID:BUCKET" SOURCE/BUCKET

- 将 ``--remote-bucket`` 的值替换为 ``TARGET`` MinIO 部署上不健康 ``BUCKET`` 的 ARN。

- 将 ``SOURCE`` 替换为源 MinIO 部署的 :ref:`alias <alias>`。

- 将 ``BUCKET`` 替换为健康 ``SOURCE`` MinIO
  部署上的存储桶名称。

该命令会返回一个重新同步作业 ID，表示该过程已经开始。

3) 监控重新同步
~~~~~~~~~~~~~~~

在源部署上使用 :mc-cmd:`mc replicate resync status` 命令跟踪已接收的复制数据：

.. code-block:: shell
   :class: copyable

   mc replicate resync status ALIAS/BUCKET

输出类似如下：

.. code-block:: shell

   mc replicate resync status /data
   Resync status summary:
   ● arn:minio:replication::6593d572-4dc3-4bb9-8d90-7f79cc612f01:data                                           
      Status: Ongoing
      Replication Status | Size (Bytes)    | Count          
      Replicated         | 2.3 GiB         | 18             
      Failed             | 0 B             | 0 

重新同步过程完成后，:guilabel:`Status` 会更新为 ``Completed``。

4) 后续步骤
~~~~~~~~~~~

- 如果 ``TARGET`` 存储桶的损坏已波及复制规则，则必须重新创建这些规则，使其与之前的复制配置一致。更多指导请参见 :ref:`minio-bucket-replication-serverside-twoway`。

- 执行基础验证，确认复制配置中的所有存储桶在 :mc:`mc ls` 和 :mc:`mc stat` 等命令下显示出相近的结果。

- 在恢复所有复制规则并验证站点间复制之后，你可以配置负责管理连接的反向代理、负载均衡器或其他网络控制平面，使其恢复向已重新同步的部署发送流量。
