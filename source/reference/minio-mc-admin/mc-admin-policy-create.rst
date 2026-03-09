==========================
``mc admin policy create``
==========================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc admin policy create

Syntax
------

.. start-mc-admin-policy-create-desc

在目标 MinIO 部署上创建一个新策略。

.. end-mc-admin-policy-create-desc

MinIO 部署默认包含以下 :ref:`内置策略 <minio-policy-built-in>`：

- :userpolicy:`readonly` 
- :userpolicy:`readwrite`
- :userpolicy:`diagnostics`
- :userpolicy:`writeonly`

.. tab-set::

   .. tab-item:: EXAMPLE


      假设以下 JSON 策略文档保存在名为 ``/tmp/listmybuckets.json`` 的文件中：
      
      .. code-block:: javascript
         :class: copyable
      
         {
            "Version": "2012-10-17",
            "Statement": [
               {
                  "Effect": "Allow",
                  "Action": [
                     "s3:ListAllMyBuckets"
                  ],
                  "Resource": [
                     "arn:aws:s3:::*"
                  ]
               }
            ]
         }

      以下命令使用文件 ``/tmp/listmybuckets.json`` 中的策略，在 :term:`alias` ``myminio`` 上创建一个名为 ``listmybuckets`` 的新策略。

      .. code-block:: shell
         :class: copyable

         mc admin policy create myminio listmybuckets /tmp/listmybuckets.json  

   .. tab-item:: SYNTAX

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc admin policy create     \
                         TARGET     \
                         POLICYNAME \
                         POLICYPATH


      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


Parameters
~~~~~~~~~~

:mc-cmd:`mc admin policy create` 命令接受以下参数：

.. mc-cmd:: TARGET

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，用于向该部署添加新策略。

.. mc-cmd:: POLICYNAME

   要添加的策略名称。
      
   指定已有策略的名称会覆盖 :mc-cmd:`~mc admin policy create TARGET` MinIO 部署上的该策略。

.. mc-cmd:: POLICYPATH

   要添加的策略文件路径。
   该文件 *必须* 为 JSON 格式，使用 :iam-docs:`IAM 兼容语法 <reference_policies.html>`，且长度不超过 2048 个字符。

Global Flags
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

Examples
--------

在别名为 ``myminio`` 的部署上，使用 ``/tmp/writeonly.json`` 的 JSON 文件创建一个名为 ``writeonly`` 的新策略。

.. code-block:: shell
   :class: copyable

   mc admin policy create myminio writeonly /tmp/writeonly.json
