.. _minio-mc-idp-ldap-accesskey-edit:

==============================
``mc idp ldap accesskey edit``
==============================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2


.. mc:: mc idp ldap accesskey edit


说明
----

.. start-mc-idp-ldap-accesskey-edit-desc

:mc:`mc idp ldap accesskey edit` 在本地服务器上修改指定的 :ref:`access key <minio-id-access-keys>`。

.. end-mc-idp-ldap-accesskey-edit-desc

.. tab-set::

   .. tab-item:: 示例

         以下示例在 ``minio`` 部署上修改 access key ``mykey`` 的 secret：

      .. code-block:: shell
         :class: copyable

         mc idp ldap accesskey edit myminio/ mykey --secret-key 'xxxxxxx' 

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap accesskey rm                        \
                                          ALIAS                        \
                                          KEY                          \
                                          [--secret-key <string>]      \
                                          [--policy <string>]          \
                                          [--name <string>]            \
                                          [--description <string>]     \
                                          [--expiry-duration <string>] \
                                          [--expiry <string>]


      - 将 ``ALIAS`` 替换为已配置 AD/LDAP 集成的 MinIO 部署的 :ref:`alias <alias>`。
      - 将 ``KEY`` 替换为要删除的 access key。
        
      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   已配置 AD/LDAP 的 MinIO 部署的 :ref:`alias <alias>`。

   例如：

   .. code-block:: none

         mc idp ldap accesskey ls minio

.. mc-cmd:: KEY
   :required:

   要删除的已配置 access key。

.. mc-cmd:: --description
   :optional:

   为服务账户添加描述。
   例如，可说明该 access key 的用途。

.. mc-cmd:: --expiry
   :optional:

   access key 的过期日期。
   请输入 YYYY-MM-DD 格式的日期。

   例如，要让凭证在 2024 年 12 月 31 日后过期，输入 ``2024-12-31``。

   与 :mc-cmd:`~mc idp ldap accesskey edit --expiry-duration` 互斥。

.. mc-cmd:: --expiry-duration
   :optional:

   access key 保持有效的时长，格式为 ``#d#h#s``。
       
   例如，``7d``、``24h``、``5d12h30s`` 都是有效字符串。

   与 :mc-cmd:`~mc idp ldap accesskey edit --expiry` 互斥。

.. mc-cmd:: --name
   :optional:

   账号的人类可读名称。

.. mc-cmd:: --policy
   :optional:

   账号使用的 JSON 格式策略文件路径。

   如果未指定，账号将使用与已认证用户相同的策略。

.. mc-cmd:: --secret-key
   :optional:

   账号使用的 secret。


示例
~~~~

修改 access key 的 secret
+++++++++++++++++++++++++

在 ``minio`` 部署上修改 access key ``mykey`` 的 secret。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey edit myminio/ mykey --secret-key 'xxxxxxx' 

修改 access key 的过期时长
++++++++++++++++++++++++++

在 ``minio`` 部署上修改 access key ``mykey`` 的过期时长。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey edit myminio/ mykey ---expiry-duration 24h 


全局参数
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
