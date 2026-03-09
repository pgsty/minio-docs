.. _minio-mc-admin-accesskey-create:

=============================
``mc admin accesskey create``
=============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin accesskey create


语法
----

.. start-mc-admin-accesskey-create-desc

:mc-cmd:`mc admin accesskey create` 命令为现有 MinIO 用户添加新的 access key 和 secret key 对。

.. end-mc-admin-accesskey-create-desc

.. admonition:: OpenID Connect 或 AD/LDAP 用户的访问密钥
   :class: note

   此命令用于为直接在 MinIO 部署上创建、且不由第三方方案管理的用户创建访问密钥。

   要为 :ref:`Active Directory/LDAP users <minio-external-identity-management-ad-ldap>` 生成访问密钥，请使用 :mc:`mc idp ldap accesskey create`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会创建一个与现有 MinIO 用户关联的新访问密钥：

      .. code-block:: shell                                                                 
         :class: copyable

         mc admin accesskey create        \
            myminio/ myuser               \                                                  
            --access-key myuseraccesskey  \                                  
            --secret-key myusersecretkey  \
            --policy /path/to/policy.json 
                                                                                   
      该命令会返回新账户的 access key 和 secret key。

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] admin accesskey create                    \
                                          ALIAS                     \
                                          [USER]                    \
                                          [--access-key string]     \
                                          [--secret-key string]     \
                                          [--policy path]           \
                                          [--name string]           \
                                          [--description string]    \
                                          [--expiry-duration value] \
                                          [--expiry date]
					
      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: USER
   :optional:

   MinIO 要为其添加新访问密钥的用户名。
   如果未指定，MinIO 会为已认证用户生成 access key/secret key 对。

.. mc-cmd:: --access-key
   :optional:

   作为此账户 access key 使用的字符串。
   若省略，MinIO 会自动生成一个随机的 20 字符值。

   Access Key 名称在所有用户之间*必须*唯一。

.. mc-cmd:: --description
   :optional:

   为访问密钥添加描述。
   例如，可以说明该访问密钥存在的原因。

.. mc-cmd:: --expiry
   :optional:

   为访问密钥设置过期日期。
   该日期必须是未来时间。
   不可设置已经过去的过期日期。

   允许的日期和时间格式：

   - ``2024-10-24``
   - ``2024-10-24T10:00``
   - ``2024-10-24T10:00:00``
   - ``2024-10-24T10:00:00Z``
   - ``2024-10-24T10:00:00-07:00``
   
   与 :mc-cmd:`~mc admin accesskey create --expiry-duration` 互斥。

.. mc-cmd:: --expiry-duration
   :optional:

   access key 保持有效的时长。
   有效时间单位为 "ns"、"us"（或 "µs"）、"ms"、"s"、"m"、"h"。

   以下示例会让凭证在 30 天后过期：

   .. code-block::

      --expiry-duration 720h

   与 :mc-cmd:`~mc admin accesskey create --expiry` 互斥。

.. mc-cmd:: --name
   :optional:

   为访问密钥添加人类可读名称。

.. mc-cmd:: --policy
   :optional:

   要附加到新访问密钥上的 :ref:`policy document <minio-policy>` 的可读路径，最大大小为 2048 字符。
   所附加策略不得授予父用户策略或组策略未明确允许的任何操作或资源访问权限。

.. mc-cmd:: --secret-key
   :optional:

   与新账户关联的 secret key。
   若省略，MinIO 会自动生成一个随机的 40 字符值。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
----

为已认证用户创建 access key / secret key 对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会为当前登录到别名为 ``myminio`` 的 MinIO 部署的用户生成新的随机 access key 和 secret key 对。
该 access key 和 secret key 与已认证用户具有相同的访问策略。

.. code-block:: shell
   :class: copyable

   mc admin accesskey create myminio/

为已认证用户创建自定义 access key / secret key 对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会为当前登录到别名为 ``myminio`` 的 MinIO 的用户创建新的 access key 和 secret key 对。
该 access key 和 secret key 与已认证用户具有相同的访问策略。

.. code-block:: shell
   :class: copyable

   mc admin accesskey create myminio/ --access-key myaccesskey --secret-key mysecretkey 

为另一位用户创建有时长限制的 access key / secret key 对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会为别名 ``myminio`` 上的用户 ``miniouser`` 创建新的 access key 和 secret key 对。
该 access key 和 secret key 与 ``miniouser`` 具有相同的访问策略。
这些凭证在创建后 24 小时内有效。

.. code-block:: shell
   :class: copyable

   mc admin accesskey create myminio/ miniouser --expiry-duration 24h


为已认证用户创建会过期的 access key / secret key 对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会为当前登录到别名 ``myminio`` 的 MinIO 部署的用户生成新的随机 access key 和随机 secret key 对。
该 access key 和 secret key 与已认证用户具有相同的访问策略。
这些凭证将于 2025 年 1 月 15 日过期。

.. code-block:: shell
   :class: copyable

   mc admin accesskey create myminio/ --expiry 2025-01-15

指定的日期**必须**是未来日期。
有关有效的日期时间格式，请参见 :mc-cmd:`~mc admin accesskey create --expiry` 标志。

为不同用户创建具有自定义访问权限的 access key / secret key 对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会为别名 ``myminio`` 上的用户 ``miniouser`` 创建新的 access key 和 secret key 对。
该 access key 和 secret key 的访问权限比 ``miniouser`` 更受限，具体由策略 JSON 文件指定。

.. code-block:: shell
   :class: copyable

   mc admin accesskey create myminio/ miniouser --policy /path/to/policy.json 

指定的策略文件**不得**授予 ``miniouser`` 当前尚未拥有的任何访问权限。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
