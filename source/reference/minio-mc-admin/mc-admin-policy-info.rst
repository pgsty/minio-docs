========================
``mc admin policy info``
========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin policy info

语法
----

.. start-mc-admin-policy-info-desc

如果目标 MinIO 部署上存在指定策略，则以 JSON 格式返回该策略。

.. end-mc-admin-policy-info-desc


.. tab-set::

   .. tab-item:: 示例

      以下命令显示 :term:`alias` ``myminio`` 上 ``writeonly`` 策略的内容。

      .. code-block:: shell
         :class: copyable

          mc admin policy info myminio writeonly  

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc admin policy info TARGET POLICYNAME
                              [--policy-file, -f <path>]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

:mc-cmd:`mc admin policy info` 命令接受以下参数：

.. mc-cmd:: TARGET
   :required:

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，用于显示指定策略。

.. mc-cmd:: POLICYNAME
   :required:

   要显示其详细信息的策略名称。

.. mc-cmd:: --policy-file
   :optional:

   指定一个文件路径，用于写入指定策略的 JSON 内容。
   如果该路径已存在，命令会用指定策略的内容覆盖已有文件。
      
全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

显示 :term:`alias` ``myminio`` 上 ``writeonly`` 策略的内容。

.. code-block:: shell
   :class: copyable

   mc admin policy info myminio writeonly

显示指定策略的信息，并将策略 JSON 内容写入 /tmp/policy.json。

.. code-block:: shell
   :class: copyable

   mc admin policy info myminio writeonly --policy-file /tmp/policy.json

输出
~~~~

该命令返回的输出类似如下：

.. code-block:: json

   {
      "Version": "2012-10-17",
      "Statement": [
         {
            "Effect": "Allow",
            "Action": [
               "s3:PutObject"
            ],
            "Resource": [
               "arn:aws:s3:::*"
            ]
         }
      ]
   }
