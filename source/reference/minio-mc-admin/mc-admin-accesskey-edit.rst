.. _minio-mc-admin-accesskey-edit:

================================
``mc admin user accesskey edit``
================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin accesskey edit


语法
----

.. start-mc-admin-accesskey-edit-desc

:mc-cmd:`mc admin accesskey edit` 命令用于修改与指定用户关联的访问密钥配置。

.. end-mc-admin-accesskey-edit-desc

该命令要求访问密钥至少有一个属性发生变更。
否则，命令会报错退出。

.. tab-set::

   .. tab-item:: 示例

      以下命令为 ``myminio`` 部署上的 ``myuserserviceaccount`` 访问密钥应用新的策略和 secret key：

      .. code-block:: shell  
         :class: copyable 

         mc admin accesskey edit                                             \  
                            myminio myuserserviceaccount                     \
                            --secret-key "myuserserviceaccountnewsecretkey"  \     
                            --policy "/path/to/new/policy.json"    

   .. tab-item:: 语法

      命令语法如下：
  
      .. code-block:: shell  
         :class: copyable 

         mc [GLOBALFLAGS] admin accesskey edit                      \  
                                          ALIAS                     \  
                                          ACCESSKEY                 \
                                          [--description string]    \
                                          [--expiry-duration value] \
                                          [--expiry value]          \
                                          [--name string]           \
                                          [--policy path]           \  
                                          [--secret-key string]  

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: ACCESSKEY
   :required:

   要修改的访问密钥。

.. mc-cmd:: --description
   :optional:

   为访问密钥添加或修改描述。
   例如，可用于说明该访问密钥的用途。

.. mc-cmd:: --expiry
   :optional:

   设置或修改访问密钥的过期日期。
   日期必须为未来时间，不能设置已过去的过期日期。

   允许的日期和时间格式：

   - ``2023-06-24``
   - ``2023-06-24T10:00``
   - ``2023-06-24T10:00:00``
   - ``2023-06-24T10:00:00Z``
   - ``2023-06-24T10:00:00-07:00``

   与 :mc-cmd:`~mc admin accesskey edit --expiry-duration` 互斥。

.. mc-cmd:: --expiry-duration
   :optional:

   访问密钥保持有效的时长。
   有效时间单位为 "ns"、"us"（或 "µs"）、"ms"、"s"、"m"、"h"。

   若要让凭证在 30 天后过期，请使用：

   .. code-block::

      --expiry-duration 720h

   与 :mc-cmd:`~mc admin accesskey edit --expiry` 互斥。

.. mc-cmd:: --name
   :optional:

   为访问密钥添加或修改一个便于识别的名称。

.. mc-cmd:: --policy
   :optional:

   要附加到新访问密钥的 :ref:`策略文档 <minio-policy>` 路径，最大长度为 2048 个字符。
   附加策略不能授予父用户策略中未显式允许的任何操作或资源访问权限。

   新策略会覆盖此前附加的任何策略。

.. mc-cmd:: --secret-key
   :optional:

   要与新访问密钥关联的 secret key。
   会覆盖先前的 secret key。
   使用该访问密钥的应用 *必须* 更新为新凭证，才能继续执行操作。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

修改访问密钥的 secret key
~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令修改 ``myminio`` 部署上 ``myuseraccesskey`` 访问密钥的 secret key。

.. code-block:: shell
   :class: copyable

   mc admin accesskey edit myminio/ myuseraccesskey --secret-key 'new-secret-key-change-me'

修改访问密钥的过期设置
~~~~~~~~~~~~~~~~~~~~~~

以下命令修改 ``myminio`` 部署上 ``myuseraccesskey`` 访问密钥的过期值。

.. code-block:: shell
   :class: copyable

   mc admin accesskey edit myminio/ myuseraccesskey --expiry-duration 24h

如果访问密钥已设置 :mc-cmd:`~mc admin accesskey edit --expiry`，则不能再添加 :mc-cmd:`~mc admin accesskey edit --expiry-duration`。


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
