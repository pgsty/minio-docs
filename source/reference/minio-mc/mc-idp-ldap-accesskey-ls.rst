.. _minio-mc-idp-ldap-accesskey-ls:

============================
``mc idp ldap accesskey ls``
============================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2


.. mc:: mc idp ldap accesskey list
.. mc:: mc idp ldap accesskey ls


描述
----

.. start-mc-idp-ldap-accesskey-ls-desc

:mc:`mc idp ldap accesskey ls` 用于显示 LDAP 访问密钥对列表。

.. end-mc-idp-ldap-accesskey-ls-desc

:mc:`mc idp ldap accesskey ls` 也称为 :mc:`mc idp ldap accesskey list`。

.. include:: /includes/common-minio-ad-ldap-params.rst
   :start-after: start-minio-ad-ldap-accesskey-creation
   :end-before: end-minio-ad-ldap-accesskey-creation

.. tab-set::

   .. tab-item:: 示例

         以下示例返回 ``minio`` :ref:`alias <alias>` 上与已认证用户关联的访问密钥列表：

      .. code-block:: shell
         :class: copyable

         mc idp ldap accesskey ls minio/

      如果已认证用户具有 ``admin:ListUsers`` 权限，则该示例命令会返回所有用户及其关联访问密钥的列表。

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap accesskey ls           \
                                          ALIAS           \
                                          [--all]         \
                                          [--self]        \
                                          [--svcacc-only] \
                                          [--temp-only]   \
                                          [--users-only]  \
                                          [DN] ...


      - 将 ``ALIAS`` 替换为已配置 AD/LDAP 集成的 MinIO 部署 :ref:`alias <alias>`。
      - 将 ``DN`` 替换为用户的 `distinguished name <https://learn.microsoft.com/en-us/previous-versions/windows/desktop/ldap/distinguished-names>`__ 字符串。
        你可以用空格分隔多个 distinguished name。

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

.. mc-cmd:: --all
   :optional:

   .. versionadded:: mc RELEASE.2024-07-31T15-58-33Z

   列出所有 LDAP 用户的全部访问密钥。

.. mc-cmd:: --self
   :optional:

   .. versionadded:: mc RELEASE.2024-07-31T15-58-33Z
      
   列出当前已认证用户的访问密钥。

.. mc-cmd:: --svcacc-only
   :optional:

   仅输出服务账户访问密钥。

   与 :mc-cmd:`~mc idp ldap accesskey ls --temp-only` 互斥。

.. mc-cmd:: --temp-only
   :optional:

   仅输出临时访问密钥。

   与 :mc-cmd:`~mc idp ldap accesskey ls --svcacc-only` 互斥。

.. mc-cmd:: --users-only
   :optional:

   仅输出用户 distinguished name。

示例
~~~~

列出所有访问密钥
++++++++++++++++

要返回所有访问密钥列表，你必须先以 ``admin`` 用户完成认证。
认证完成后，以下命令会返回 ``minio`` 部署上的全部 AD/LDAP 访问密钥。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey ls minio

.. note::

   如果用户没有 ``admin:ListUsers`` 权限，该命令仅返回已认证用户的访问密钥列表。

列出用户 Distinguished Name
+++++++++++++++++++++++++++

要返回某个部署的 DN 列表，你必须先以具有 ``admin:ListUsers`` 权限的用户完成认证。
认证完成后，以下命令会输出 ``minio`` 部署上的 AD/LDAP distinguished name。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey ls minio --users-only

列出临时访问密钥
++++++++++++++++

要返回某个部署的全部临时访问密钥列表，你必须先以具有 ``admin:ListUsers`` 权限的用户完成认证。
认证完成后，以下命令会输出 distinguished name 及其关联临时访问密钥的列表。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey ls minio --temp-only

列出某个用户的访问密钥
++++++++++++++++++++++

以下命令返回 ``minio`` 部署上用户 ``bobfisher`` 的 AD/LDAP 访问密钥。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey list minio/ uid=bobfisher,dc=min,dc=io

列出多个用户的访问密钥
++++++++++++++++++++++

以下命令返回 ``minio`` 部署上用户 ``bobfisher`` 和 ``cody3`` 的 AD/LDAP 访问密钥。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey list minio/ uid=bobfisher,dc=min,dc=io uid=cody3,dc=min,dc=io

列出已认证用户的访问密钥
++++++++++++++++++++++++

以下命令返回 ``minio`` 部署上当前已认证用户的 AD/LDAP 访问密钥。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey list minio/ 

.. note:: 

   如果已认证用户具有 ``admin:ListUsers`` 权限，则该命令会返回部署上所有用户及其访问密钥列表。

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
