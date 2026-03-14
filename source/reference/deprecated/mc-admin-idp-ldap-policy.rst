.. _minio-mc-admin-idp-ldap-policy:

============================
``mc admin idp ldap policy``
============================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin idp ldap policy

.. versionchanged:: RELEASE.2023-05-26T23-31-54Z

   ``mc admin idp ldap policy`` 及其子命令已由 :mc-cmd:`mc idp ldap policy` 替代。

说明
----

.. start-mc-admin-idp-ldap-policy-desc

:mc-cmd:`mc admin idp ldap policy` 命令用于查看策略与关联组或用户之间的映射关系。

.. end-mc-admin-idp-ldap-policy-desc


:mc-cmd:`mc admin idp ldap policy` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 说明

   * - :mc-cmd:`mc admin idp ldap policy attach`
     - 将策略附加到实体

   * - :mc-cmd:`mc admin idp ldap policy detach`
     - 从实体分离策略

   * - :mc-cmd:`mc admin idp ldap policy entities`
     - 列出策略与实体的映射

语法
----

.. mc-cmd:: attach

   将一个或多个策略附加到实体。

   .. tab-set::

      .. tab-item:: 示例

         以下示例将两个策略 ``policy1`` 和 ``policy2`` 附加到 ``myminio`` 部署上的 ``projectb`` 组。

         .. code-block:: shell
            :class: copyable

             mc admin idp ldap policy attach myminio/                                               \
                                             policy1                                                \
                                             policy2                                                \
                                             --group='cn=projectb,ou=groups,ou=swengg,dc=min,dc=io'


         以下示例将策略 ``userpolicy`` 附加到 ``myminio`` 部署上的用户 ``bobfisher``。

         .. code-block:: shell
            :class: copyable

             mc admin idp ldap policy attach myminio/                                               \
                                             mypolicy                                               \
                                             policy2                                                \
                                             --user='uid=bobfisher,ou=people,ou=hwengg,dc=min,dc=io'
      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable
 
            mc [GLOBALFLAGS] admin idp ldap policy attach     \
                                            POLICYNAME        \
                                            [POLICY2] ...     \
                                            ALIAS             \
                                            [--user=`USER`]   \
                                            [--group=`GROUP`]

         - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于配置 AD/LDAP 集成。
         - 将 ``POLICYNAME`` 替换为要附加到实体的策略。
           你可以列出多个策略并附加到该实体。
         - 必须使用 ``--user`` 或 ``--group`` 其中之一。
           每个命令中只能使用一次该标志。
           不能在同一命令中同时使用这两个标志。


.. mc-cmd:: detach

   从实体分离一个或多个策略。

   .. tab-set::

      .. tab-item:: 示例

         以下示例从 ``myminio`` 部署上的 ``projectb`` 组分离两个策略 ``policy1`` 和 ``policy2``。

         .. code-block:: shell
            :class: copyable

             mc admin idp ldap policy detach myminio/                                               \
                                             policy1                                                \
                                             policy2                                                \
                                             --group='cn=projectb,ou=groups,ou=swengg,dc=min,dc=io'


         以下示例从 ``myminio`` 部署上的用户 ``bobfisher`` 分离策略 ``userpolicy``。

         .. code-block:: shell
            :class: copyable

             mc admin idp ldap policy detach myminio/                                               \
                                             mypolicy                                               \
                                             policy2                                                \
                                             --user='uid=bobfisher,ou=people,ou=hwengg,dc=min,dc=io'
      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable
 
            mc [GLOBALFLAGS] admin idp ldap policy detach     \
                                            POLICYNAME        \
                                            [POLICY2] ...     \
                                            ALIAS             \
                                            [--user=`USER`]   \
                                            [--group=`GROUP`]

         - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于配置 AD/LDAP 集成。
         - 将 ``POLICYNAME`` 替换为要从实体分离的策略。
           你可以列出多个策略并从该实体分离。
         - 必须使用 ``--user`` 或 ``--group`` 其中之一。
           每个命令中只能使用一次该标志。
           不能在同一命令中同时使用这两个标志。

.. mc-cmd:: entities

   显示用户、组和/或策略的映射列表。

   .. tab-set::

      .. tab-item:: 示例

         以下示例列出 ``myminio`` 部署中某个特定策略、一组组以及部分用户的全部映射关系。

         具体包括：
         - 映射到 ``finteam-policy`` 策略的用户。
         - 分配给用户 ``uid=bobfisher,ou=people,ou=hwengg,dc=min,dc=io`` 的策略
         - 分配给组 ``cn=projectb,ou=groups,ou=swengg,dc=min,dc=io`` 的策略

         .. code-block:: shell
            :class: copyable

             mc admin idp ldap policy entities myminio/                                            \
                                          --policy finteam-policy                                  \
                                          --user 'uid=bobfisher,ou=people,ou=hwengg,dc=min,dc=io'  \
                                          --group 'cn=projectb,ou=groups,ou=swengg,dc=min,dc=io' 

      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp ldap policy entities                \
                                            ALIAS                          \
                                            [--user `value`, -u `value`]   \
                                            [--group `value`, -g `value`]  \
                                            [--policy value]

         - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于配置 AD/LDAP 集成。
         - 在命令中可按需多次使用 ``--user``、``--group`` 和/或 ``--policy`` 标志。
         - 对于每个标志，输出会列出映射到指定策略、用户或组的实体。
         - 省略所有标志可返回所有策略的映射列表。


全局标志
--------

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
