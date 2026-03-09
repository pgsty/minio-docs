==========================
``mc admin policy attach``
==========================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc admin policy attach

语法
----

.. start-mc-admin-policy-attach-desc

将一个或多个 IAM 策略附加到 :ref:`MinIO 管理的用户或组 <minio-users>`。

.. end-mc-admin-policy-attach-desc

.. versionchanged:: RELEASE.2023-05-27T05-56-19Z

   要成功附加策略，所引用的用户或组必须存在。

必须且只能指定 :mc-cmd:`~mc admin policy attach --user` 或 :mc-cmd:`~mc admin policy attach --group` 二者之一。


.. tab-set::

   .. tab-item:: 示例

      以下命令将 ``readonly`` 策略附加到 :term:`alias` 为 ``myminio`` 的部署中的用户 ``james``。

      .. code-block:: shell
         :class: copyable

         mc admin policy attach myminio readonly --user james  

   .. tab-item:: 语法

      该命令使用以下语法：

      .. code-block:: shell
         :class: copyable

         mc admin policy attach                       \
                         TARGET                       \
                         POLICY                       \
                         [POLICY...]                  \
                         [--user USER | --group GROUP] 


      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

.. important::

   此命令仅用于管理 :ref:`MinIO 管理 <minio-users>` 用户的策略关联。

   如需为 OpenID 管理的用户附加策略，请参见 :ref:`minio-external-identity-management-openid`。

   如需为 Active Directory/LDAP 用户或组附加策略，请使用 :mc-cmd:`mc idp ldap policy attach`。

参数
~~~~

:mc-cmd:`mc admin policy attach` 命令接受以下参数：

.. mc-cmd:: TARGET
   :required:

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，该部署中包含要附加一个或多个策略的用户或组。

.. mc-cmd:: POLICY
   :required:

   要附加到用户或组的策略名称。 
      
   你可以通过空格分隔每个策略名称，一次附加多个策略。

   MinIO 部署默认包含以下 :ref:`内置策略 <minio-policy-built-in>`：

   - :userpolicy:`readonly` 
   - :userpolicy:`readwrite`
   - :userpolicy:`diagnostics`
   - :userpolicy:`writeonly`

.. mc-cmd:: --user
   :optional:

   要附加一个或多个策略的用户身份名称。
   只能指定一个用户。

   你必须包含 ``--user`` 或 ``--group`` 标志之一。
   ``--user`` 标志不能与 ``--group`` 标志同时使用。

.. mc-cmd:: --group
   :optional:

   要附加一个或多个策略的组身份名称。
   只能指定一个组。

   属于该组的所有用户都会继承与该组关联的策略。

   你必须包含 ``--group`` 或 ``--user`` 标志之一。
   ``--group`` 标志不能与 ``--user`` 标志同时使用。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

将 ``readonly`` 策略附加到 alias 为 ``myminio`` 的部署中的用户 ``james``。

.. code-block:: shell
   :class: copyable

   mc admin policy attach myminio readonly --user james

将 ``audit-policy`` 和 ``acct-policy`` 策略附加到 alias 为 ``myminio`` 的部署中的组 ``legal``。

.. code-block:: shell
   :class: copyable

   mc admin policy attach myminio audit-policy acct-policy --group legal
