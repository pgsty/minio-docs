.. _minio-mc-idp-ldap-accesskey-create:

================================
``mc idp ldap accesskey create``
================================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2


.. mc:: mc idp ldap accesskey create

.. versionadded:: mc RELEASE.2023-12-23T08-47-21Z 

描述
----

.. start-mc-idp-ldap-accesskey-create-desc

:mc:`mc idp ldap accesskey create` 允许添加 LDAP 访问密钥对。

.. end-mc-idp-ldap-accesskey-create-desc

.. tab-set::

   .. tab-item:: EXAMPLE

         以下示例在 ``minio`` :ref:`alias <alias>` 上创建一个新的访问密钥对，并使用与已认证用户相同的策略：

      .. code-block:: shell
         :class: copyable

         mc idp ldap accesskey create minio/

   .. tab-item:: SYNTAX

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap accesskey create                   \
                                          ALIAS                       \
                                          [--access-key <value>]      \
                                          [--secret-key <value>]      \
                                          [--policy <value>]          \
                                          [--name <value>]            \
                                          [--description <value>]     \
                                          [--expiry <value>]          \         
                                          [--expiry-duration <value>]

      - 将 ``ALIAS`` 替换为已配置 AD/LDAP 集成的 MinIO 部署的 :ref:`alias <alias>`。

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

         mc idp ldap accesskey create minio

.. mc-cmd:: --access-key
   :optional:

   用于该账户的访问密钥。
   访问密钥不能包含 ``=``（等号）或 ``,``（逗号）字符。

   需要 :mc-cmd:`~mc idp ldap accesskey create --secret-key`

.. mc-cmd:: --secret-key
   :optional:

   用于该账户的密钥。

   需要 :mc-cmd:`~mc idp ldap accesskey create --access-key`

.. mc-cmd:: --policy
   :optional:

   账户要使用的 JSON 格式策略文件路径。

   如果未指定，账户将使用与已认证用户相同的策略。

.. mc-cmd:: --name
   :optional:

   用于该账户的人类可读名称。

.. mc-cmd:: --description
   :optional:

   为服务账户添加描述。
   例如，你可以说明创建该访问密钥的原因。

.. mc-cmd:: --expiry-duration
   :optional:

   访问密钥对保持有效的时长，格式为 ``#d#h#s``。
       
   例如，``7d``、``24h``、``5d12h30s`` 都是有效字符串。

   与 :mc-cmd:`~mc idp ldap accesskey create --expiry` 互斥。

.. mc-cmd:: --expiry
   :optional:

   访问密钥在该日期之后过期。
   日期请使用 YYYY-MM-DD 格式输入。

   例如，要使凭证在 2024 年 12 月 31 日之后过期，请输入 ``2024-12-31``。

   与 :mc-cmd:`~mc idp ldap accesskey create --expiry-duration` 互斥。

.. mc-cmd:: --login
   :optional:

   .. deprecated:: RELEASE.2024-04-18T16-45-29Z

      使用 :mc-cmd:`mc idp ldap accesskey create-with-login` 获取此前由该参数提供的功能。

   提示用户使用 LDAP 凭证登录以生成访问密钥。
   指定用于登录提示的、已配置 LDAP 的 MinIO Server URL。

   需要交互式终端。


全局标志
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

示例
----

为已认证用户创建新的访问密钥对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会为 ``minio`` alias 上当前已认证用户创建一个新的访问密钥对。
该命令会输出随机生成的访问密钥和密钥。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey create minio

使用自定义访问密钥和密钥创建新的访问密钥对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会为 ``minio`` alias 上当前已认证用户创建一个新的访问密钥对，其中访问密钥和密钥都由你指定。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey create minio/ --access-key my-access-key-change-me --secret-key my-secret-key-change-me

创建 24 小时后过期的新访问密钥对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会为 ``minio`` alias 上当前已认证用户创建一个新的访问密钥对。
该凭证会在 24 小时后过期。

该命令会输出随机生成的访问密钥和密钥。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey create minio --expiry-duration 24h

创建新的访问密钥并提示以该用户身份登录
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会创建一个新的访问密钥对。
MinIO Client 会先要求你在 ``minio.example.com`` 上已配置 LDAP 的 MinIO 站点中，以该访问密钥所属用户身份登录。

该命令会输出随机生成的访问密钥和密钥。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey create minio --login minio.example.com

创建在指定日期后过期的新访问密钥对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会为 ``minio`` alias 上当前已认证用户创建一个新的访问密钥对。
该凭证会在 2024 年 2 月 29 日之后过期。

该命令会输出随机生成的访问密钥和密钥。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey create minio --expiry 2024-02-29
