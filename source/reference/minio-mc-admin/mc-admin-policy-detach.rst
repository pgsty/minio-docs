==========================
``mc admin policy detach``
==========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin policy detach

语法
----

.. start-mc-admin-policy-detach-desc

从 :ref:`MinIO 管理的用户或组 <minio-users>` 中移除一个或多个 IAM 策略。

.. end-mc-admin-policy-detach-desc

必须且只能指定 :mc-cmd:`~mc admin policy detach --user` 或 :mc-cmd:`~mc admin policy detach --group` 其中之一。


.. tab-set::

   .. tab-item:: 示例

      以下命令从别名为 ``myminio`` 的部署中为用户 ``james`` 解绑 ``readonly`` 策略。

      .. code-block:: shell
         :class: copyable

         mc admin policy detach myminio readonly --user james   

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc admin policy detach TARGET                         \
                                POLICY                         \
                                [POLICY...]                    \
                                [--user USER | --group GROUP]


      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


.. important::

   此命令仅用于管理 :ref:`MinIO 管理 <minio-users>` 用户的策略关联。

   如需管理 OpenID 管理的用户策略，请参见 :ref:`minio-external-identity-management-openid`。

   如需从 Active Directory/LDAP 用户或组解绑策略，请使用 :mc-cmd:`mc idp ldap policy detach`。

参数
~~~~

:mc-cmd:`mc admin policy detach` 命令接受以下参数：

.. mc-cmd:: TARGET
   :required:

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，其中包含你要解绑一个或多个策略的用户或组。

.. mc-cmd:: POLICY
   :required:

   要从用户或组解绑的策略名称。
   你可以通过空格分隔多个策略名称，一次解绑多个策略。

   MinIO 部署默认包含以下 :ref:`内置策略 <minio-policy-built-in>`：

   - :userpolicy:`readonly` 
   - :userpolicy:`readwrite`
   - :userpolicy:`diagnostics`
   - :userpolicy:`writeonly`

.. mc-cmd:: --user
   :optional:

   要解绑策略的身份用户名。
   只能指定一个用户。

   必须包含 ``--user`` 或 ``--group`` 标志之一。
   不能将 ``--user`` 与 ``--group`` 标志同时使用。

.. mc-cmd:: --group
   :optional:

   要解绑策略的组名称。
   只能指定一个组。

   组内所有成员都会失去该组关联策略所授予的权限，除非这些权限也由用户所属的其他策略或组授予。

   必须包含 ``--group`` 或 ``--user`` 标志之一。
   不能将 ``--group`` 与 ``--user`` 标志同时使用。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

从别名为 ``myminio`` 的部署中为用户 ``james`` 解绑 ``readonly`` 策略。

.. code-block:: shell
   :class: copyable

   mc admin policy detach myminio readonly --user james

从别名为 ``myminio`` 的部署中为组 ``legal`` 解绑 ``audit-policy`` 和 ``acct-policy`` 策略。

.. code-block:: shell
   :class: copyable

   mc admin policy detach myminio audit-policy acct-policy --group legal
