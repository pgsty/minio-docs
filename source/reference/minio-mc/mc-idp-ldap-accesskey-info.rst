.. _minio-mc-idp-ldap-accesskey-info:

==============================
``mc idp ldap accesskey info``
==============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2


.. mc:: mc idp ldap accesskey info


描述
----

.. start-mc-idp-ldap-accesskey-info-desc

:mc:`mc idp ldap accesskey info` 输出指定访问密钥（一个或多个）的信息。

.. end-mc-idp-ldap-accesskey-info-desc

.. include:: /includes/common-minio-ad-ldap-params.rst
   :start-after: start-minio-ad-ldap-accesskey-creation
   :end-before: end-minio-ad-ldap-accesskey-creation

.. tab-set::

   .. tab-item:: 示例

         以下示例输出 ``minio`` 部署中访问密钥 ``mykey`` 的详细信息：

      .. code-block:: shell
         :class: copyable

         mc idp ldap accesskey info minio/ mykey

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap accesskey info      \
                                             ALIAS     \
                                             KEY       \
                                             [KEY2] ...


      - 将 ``ALIAS`` 替换为已配置 AD/LDAP 集成的 MinIO 部署的 :ref:`alias <alias>`。
      - 将 ``KEY`` 替换为要删除的访问密钥。
        你可以使用空格分隔多个访问密钥，以列出多个密钥。
        
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

   要输出其信息的已配置访问密钥。

   你可以使用空格分隔多个访问密钥，以列出多个密钥。


示例
~~~~

输出 ``minio`` 部署中访问密钥 ``mykey`` 和 ``mykey2`` 的信息。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey info minio/ mykey mykey2

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
