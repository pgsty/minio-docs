.. _minio-mc-idp-ldap-accesskey-rm:

============================
``mc idp ldap accesskey rm``
============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2


.. mc:: mc idp ldap accesskey rm
.. mc:: mc idp ldap accesskey remove


描述
----

.. start-mc-idp-ldap-accesskey-rm-desc

:mc:`mc idp ldap accesskey rm` 从本地服务器中删除指定的访问密钥。

.. end-mc-idp-ldap-accesskey-rm-desc

:mc:`mc idp ldap accesskey rm` 也称为 :mc:`mc idp ldap accesskey remove`。

.. include:: /includes/common-minio-ad-ldap-params.rst
   :start-after: start-minio-ad-ldap-accesskey-creation
   :end-before: end-minio-ad-ldap-accesskey-creation

.. tab-set::

   .. tab-item:: 示例

         以下示例从 ``minio`` 部署中删除访问密钥 ``mykey``：

      .. code-block:: shell
         :class: copyable

         mc idp ldap accesskey rm minio/ mykey

   .. tab-item:: 语法

      该命令使用以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap accesskey rm              \
                                          ALIAS              \
                                          KEY


      - 将 ``ALIAS`` 替换为已配置 AD/LDAP 集成的 MinIO 部署的 :ref:`alias <alias>`。
      - 将 ``KEY`` 替换为要删除的访问密钥。

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

   要删除的已配置访问密钥。

示例
~~~~

从 ``minio`` 部署中删除访问密钥 ``mykey``。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey rm minio/ mykey

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
