.. _minio-mc-idp-ldap-accesskey-create-with-login:

===========================================
``mc idp ldap accesskey create-with-login``
===========================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2


.. mc:: mc idp ldap accesskey create-with-login

.. versionadded:: mc RELEASE.2024-04-18T16-45-29Z

描述
----

.. start-mc-idp-ldap-accesskey-create-with-login-desc

:mc:`mc idp ldap accesskey create-with-login` 使用基于终端的交互式提示与外部 AD/LDAP 服务器进行身份验证，并生成可供 MinIO 使用的访问密钥。

.. end-mc-idp-ldap-accesskey-create-with-login-desc

.. tab-set::

   .. tab-item:: 示例

         以下示例会提示用户提供其 AD/LDAP 凭证。
         然后使用与该 AD/LDAP 用户关联的一个或多个策略生成新的访问密钥对。

      .. code-block:: shell
         :class: copyable

         mc idp ldap accesskey create-with-login https://minio.example.net/

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap accesskey create-with-login        \
                                          URL                         \
                                          [--access-key <value>]      \
                                          [--secret-key <value>]      \
                                          [--policy <value>]          \
                                          [--name <value>]            \
                                          [--description <value>]     \
                                          [--expiry <value>]          \
                                          [--expiry-duration <value>]

      - 将 ``URL`` 替换为已配置 AD/LDAP 集成的 MinIO 部署的 :abbr:`FQDN (Fully Qualified Domain Name)`。

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: URL
   :required:

   已配置 AD/LDAP 集成的 MinIO 部署的 :abbr:`FQDN (Fully Qualified Domain Name)`。

   例如：

   .. code-block:: none

         mc idp ldap accesskey create-with-login https://minio.example.net

.. mc-cmd:: --access-key
   :optional:

   成功完成身份验证后要使用的访问密钥。
   省略此项则由 MinIO 随机生成一个值。
   
   访问密钥不能包含 ``=``（等号）或 ``,``（逗号）字符。

   需要同时指定 :mc-cmd:`~mc idp ldap accesskey create-with-login --secret-key`

.. mc-cmd:: --secret-key
   :optional:

   成功完成身份验证后要使用的 secret key。
   省略此项则由 MinIO 随机生成一个值。

   需要同时指定 :mc-cmd:`~mc idp ldap accesskey create-with-login --access-key`

.. mc-cmd:: --policy
   :optional:

   账户要使用的 JSON 格式 :ref:`policy <minio-policy>` 文件路径。
   该策略_不能_授予超出已认证 AD/LDAP 用户关联权限范围之外的额外权限。

   省略此项则使用 AD/LDAP 用户关联策略。

.. mc-cmd:: --name
   :optional:

   为创建的访问密钥指定一个便于识别的名称。

.. mc-cmd:: --description
   :optional:

   为服务账户创建描述。
   例如，你可以说明该访问密钥存在的原因。

.. mc-cmd:: --expiry-duration
   :optional:

   访问密钥对保持可用的时长，格式为 ``#d#h#s``。
       
   例如，``7d``、``24h``、``5d12h30s`` 都是有效字符串。

   与 :mc-cmd:`~mc idp ldap accesskey create-with-login --expiry` 互斥。

.. mc-cmd:: --expiry
   :optional:

   访问密钥的过期日期。
   以 ``YYYY-MM-DD`` 格式输入日期。

   例如，要让凭证在 2024 年 12 月 31 日后过期，输入 ``2024-12-31``。

   与 :mc-cmd:`~mc idp ldap accesskey create-with-login --expiry-duration` 互斥。


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

以下命令为 ``minio`` 别名下当前已认证用户创建新的访问密钥对。
该命令会输出随机生成的 access key 和 secret key。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey create-with-login https://minio.example.net

使用自定义 access key 和 secret key 创建新的访问密钥对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令为 ``minio`` 别名下当前已认证用户创建新的访问密钥对，其中 access key 和 secret key 都由你指定。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey create-with-login https://minio.example.net/ --access-key my-access-key-change-me --secret-key my-secret-key-change-me

创建 24 小时后过期的新访问密钥对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令为 ``minio`` 别名下当前已认证用户创建新的访问密钥对。
该凭证会在 24 小时后过期。

该命令会输出随机生成的 access key 和 secret key。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey create-with-login https://minio.example.net --expiry-duration 24h

创建在指定日期后过期的新访问密钥对
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令为 ``minio`` 别名下当前已认证用户创建新的访问密钥对。
该凭证会在 2025 年 2 月 28 日后过期。

该命令会输出随机生成的 access key 和 secret key。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey create-with-login https://minio.example.net --expiry 2025-02-28
