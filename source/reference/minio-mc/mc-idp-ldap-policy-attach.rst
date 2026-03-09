.. _minio-mc-idp-ldap-policy-attach:

=============================
``mc idp ldap policy attach``
=============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc idp ldap policy attach


描述
----

.. start-mc-idp-ldap-policy-attach-desc

:mc:`mc idp ldap policy attach` 命令将一个或多个策略附加到实体。

.. end-mc-idp-ldap-policy-attach-desc

.. tab-set::

   .. tab-item:: EXAMPLE

         以下示例将策略 ``userpolicy`` 附加到 ``myminio`` 部署中的用户 ``bobfisher``：

      .. code-block:: shell
         :class: copyable

         mc idp ldap policy attach myminio                                                  \
                                   userpolicy                                               \
                                   --user='uid=bobfisher,ou=people,ou=hwengg,dc=min,dc=io'

   .. tab-item:: SYNTAX

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap policy attach             \
                                          POLICYNAME         \
                                          [POLICY2] ...      \
                                          ALIAS              \
                                          [--user=`USER`]    \
                                          [--group=`GROUP`]


      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于配置 AD/LDAP 集成。
      - 将 ``POLICYNAME`` 替换为要附加到实体的策略。
        你可以列出多个要附加到实体的策略。
      - 必须使用 ``--user`` 或 ``--group`` 标志之一。
        在命令中每个标志只能使用一次。
        不能在同一命令中同时使用这两个标志。

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，其中包含要附加策略的实体。

   例如：

   .. code-block:: none

         mc idp ldap policy attach myminio                                                  \
                                   userpolicy                                               \
                                   --user='uid=bobfisher,ou=people,ou=hwengg,dc=min,dc=io'


示例
~~~~

以下示例将两个策略 ``policy1`` 和 ``policy2`` 附加到 ``myminio`` 部署中的 ``projectb`` 组：

.. code-block:: shell
   :class: copyable

   mc idp ldap policy attach myminio                                                 \
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
