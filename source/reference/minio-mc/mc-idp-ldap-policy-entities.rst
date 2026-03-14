.. _minio-mc-idp-ldap-policy-entities:

===============================
``mc idp ldap policy entities``
===============================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc idp ldap policy entities


说明
----

.. start-mc-idp-ldap-policy-entities-desc

:mc:`mc idp ldap policy entities` 命令显示用户、组和/或策略的映射关系列表。

.. end-mc-idp-ldap-policy-entities-desc

.. tab-set::

   .. tab-item:: 示例

      以下示例列出 ``myminio`` 部署中某个特定策略、一组用户组以及部分用户的所有映射关系。

      具体包括：

      - 映射到 ``finteam-policy`` 策略的用户。
      - 分配给 ``uid=bobfisher,ou=people,ou=hwengg,dc=min,dc=io`` 用户的策略。
      - 分配给 ``cn=projectb,ou=groups,ou=swengg,dc=min,dc=io`` 组的策略。

      .. code-block:: shell
         :class: copyable

         mc idp ldap policy entities myminio                                                  \
                                     --policy finteam-policy                                  \
                                     --user 'uid=bobfisher,ou=people,ou=hwengg,dc=min,dc=io'  \
                                     --group 'cn=projectb,ou=groups,ou=swengg,dc=min,dc=io'

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap policy entities                       \
                                          ALIAS                          \
                                          [--group `value`, -g `value`]  \
                                          [--policy value]               \
                                          [--user `value`, -u `value`]

      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于配置 AD/LDAP 集成。
      - 在命令中可按需多次使用 ``--user``、``--group`` 和/或 ``--policy`` 标志。
      - 对于每个标志，输出会列出映射到指定策略、用户或组的实体。
      - 省略所有标志将返回所有策略的映射关系列表。

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要显示实体映射关系的 MinIO 部署 :ref:`alias <alias>`。

   例如：

   .. code-block:: none

      mc idp ldap policy entities myminio

.. mc-cmd:: --group
   :optional:

   返回与指定组关联的用户和策略列表。
   重复此标志可返回多个组的列表。

.. mc-cmd:: --policies
   :optional:

   返回与指定策略关联的用户和组列表。
   重复此标志可返回多个策略的列表。

.. mc-cmd:: --user
   :optional:

   返回该用户所属的组列表，以及与每个组关联的策略。
   输出仅包含已分配策略的组。

   重复此标志可返回多个用户的列表。

示例
~~~~

以下示例列出 ``myminio`` 部署中映射到两个策略 ``policy1`` 和 ``policy2`` 的实体，以及映射到 ``projectb`` 组的实体：

.. code-block:: shell
   :class: copyable

   mc idp ldap policy entities myminio                                                 \
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
