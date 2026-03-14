.. _minio-mc-idp-ldap-policy-detach:

=============================
``mc idp ldap policy detach``
=============================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc idp ldap policy detach


描述
----

.. start-mc-idp-ldap-policy-detach-desc

:mc:`mc idp ldap policy detach` 命令可从实体分离一个或多个策略。

.. end-mc-idp-ldap-policy-detach-desc

.. tab-set::

   .. tab-item:: EXAMPLE

      以下示例从 ``myminio`` 部署中的用户 ``bobfisher`` 分离策略 ``userpolicy``。

      .. code-block:: shell
         :class: copyable

         mc idp ldap policy detach myminio                                                  \
                                   userpolicy                                               \
                                   --user='uid=bobfisher,ou=people,ou=hwengg,dc=min,dc=io' 

   .. tab-item:: SYNTAX

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap policy detach             \
                                          POLICYNAME         \
                                          [POLICY2] ...      \
                                          ALIAS              \
                                          [--user=`USER`]    \
                                          [--group=`GROUP`]


      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于配置 AD/LDAP 集成。
      - 将 ``POLICYNAME`` 替换为要从实体分离的策略。
        你可以列出多个要从实体分离的策略。
      - 必须在 ``--user`` 或 ``--group`` 标志中二选一。
        每个命令中只能使用一次该标志。
        不能在同一命令中同时使用这两个标志。

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   包含待分离策略实体的 MinIO 部署 :ref:`alias <alias>`。

   例如：

   .. code-block:: none

         mc idp ldap policy detach myminio                                                  \
                                   userpolicy                                               \
                                   --user='uid=bobfisher,ou=people,ou=hwengg,dc=min,dc=io'


示例
~~~~

以下示例从 ``myminio`` 部署中的 ``projectb`` 组分离两个策略 ``policy1`` 和 ``policy2``：

.. code-block:: shell
   :class: copyable

   mc idp ldap policy detach myminio                                                 \
                             policy1                                                 \
                             policy2                                                 \
                             --group='cn=projectb,ou=groups,ou=swengg,dc=min,dc=io'


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
