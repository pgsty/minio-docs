.. _minio-mc-admin-svcacct-edit:

==============================
``mc admin user svcacct edit``
==============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin user svcacct edit

.. important::

   此命令已被替代，并将在未来的 MinIO 客户端 发布版本中弃用。

   从 MinIO客户端版本RELEASE.2024-10-08T09-37-26Z 起，请使用 :mc:`mc admin accesskey edit` 命令修改内置 MinIO IDP 用户的访问密钥。

   如需修改 AD/LDAP 用户的访问密钥，请使用 :mc:`mc idp ldap accesskey edit` 命令。

语法
----

.. start-mc-admin-svcacct-edit-desc

:mc-cmd:`mc admin user svcacct edit` 命令用于修改与指定用户关联的访问密钥配置。

.. end-mc-admin-svcacct-edit-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令为 ``myminio`` 部署上的 ``myuserserviceaccount`` 访问密钥应用新的策略和密钥：

      .. code-block:: shell  
         :class: copyable 

         mc admin user svcacct edit                                             \  
                               --secret-key "myuserserviceaccountnewsecretkey"  \     
                               --policy "/path/to/new/policy.json"              \  
                               myminio myuserserviceaccount

   .. tab-item:: 语法

      该命令的语法如下：
  
      .. code-block:: shell  
         :class: copyable 

         mc [GLOBALFLAGS] admin user svcacct edit            \  
                                             [--secret-key]  \  
                                             [--policy]      \  
                                             ALIAS           \  
                                             SERVICEACCOUNT 

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: SERVICEACCOUNT
   :required:

   要修改的服务账户。

.. mc-cmd:: --description
   :optional:

   .. versionadded:: RELEASE.2023-05-18T16-59-00Z

   为服务账户添加描述。
   例如，可以说明该服务账户存在的原因。

.. mc-cmd:: --expiry
   :optional:

   .. versionadded:: RELEASE.2023-05-30T22-41-38Z

   为服务账户设置过期日期。
   日期必须在未来，不能设置已经过去的过期日期。

   允许的日期和时间格式：

   - ``2023-06-24``
   - ``2023-06-24T10:00``
   - ``2023-06-24T10:00:00``
   - ``2023-06-24T10:00:00Z``
   - ``2023-06-24T10:00:00-07:00``

.. mc-cmd:: --name
   :optional:

   .. versionadded:: RELEASE.2023-05-18T16-59-00Z

   为服务账户添加一个便于识别的名称。

.. mc-cmd:: --policy
   :optional:

   要附加到新访问密钥的 :ref:`policy document <minio-policy>` 路径，最大长度为 2048 个字符。
   附加的策略不能授予父用户策略未明确允许的任何操作或资源访问权限。

   新策略会覆盖此前已附加的任何策略。

.. mc-cmd:: --secret-key
   :optional:

   与新访问密钥关联的密钥。
   会覆盖此前的密钥。
   使用这些访问密钥的应用程序 *必须* 更新为新凭证，才能继续执行操作。


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
