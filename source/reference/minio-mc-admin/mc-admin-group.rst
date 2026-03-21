===================
``mc admin group``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin group

说明
-----------

.. start-mc-admin-group-desc

:mc:`mc admin group` 命令用于管理 MinIO 部署上的组。

.. end-mc-admin-group-desc

:ref:`组 <minio-groups>` 是 :ref:`用户
<minio-users>` 的集合。每个组都可以分配一个或多个
:ref:`策略 <minio-policy>`，用于明确列出允许或拒绝组成员访问的
操作和资源。对于具有相同访问模式和工作负载的用户，组提供了一种
简化的共享权限管理方式。

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

组与基于策略的访问控制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 使用 基于策略的访问控制 (PBAC) 对已成功在部署上
*认证* 的用户执行*授权*。每条策略都包含规则，用于规定该部署上
允许或拒绝的操作/资源。可以为一个组分配一个或多个 :ref:`策略
<minio-policy>`。属于该组的用户会继承该组已分配的策略。用户的
总权限集合包括其显式分配的策略，*以及* 通过组成员关系继承的策略。

新创建的组默认*不*包含任何策略。要配置组的已分配策略，请使用
:mc-cmd:`mc admin policy attach` 命令。

有关 MinIO 用户和组的更多信息，请参见
:ref:`minio-users` 和 :ref:`minio-groups`。有关 MinIO 策略的更多信息，
请参见 :ref:`MinIO Policy Based Access Control <minio-policy>`。

.. admonition:: ``Deny`` 会覆盖 ``Allow``
   :class: note

   MinIO 遵循 IAM 标准：在同一操作或资源上，``Deny`` 规则会覆盖 ``Allow`` 规则。
   例如，如果某个用户显式分配的策略对某个操作/资源包含 ``Allow`` 规则，
   而该用户所属的某个组所分配策略对同一操作/资源包含 ``Deny`` 规则，
   则 MinIO 只会应用 ``Deny`` 规则。

   有关 IAM 策略评估逻辑的更多信息，请参见 IAM 文档中的
   :iam-docs:`Determining Whether a Request is Allowed or Denied Within an Account
   <reference_policies_evaluation-logic.html#policy-eval-denyallow>`。

示例
--------

创建新组
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin group add` 在兼容 S3 的主机上创建新组：

.. code-block:: shell
   :class: copyable

   mc admin group add ALIAS GROUPNAME MEMBER [MEMBER...]

- 将 :mc-cmd:`ALIAS <mc admin group add TARGET>` 替换为兼容 S3 主机的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`GROUPNAME <mc admin group add GROUPNAME>` 替换为要创建的
  组名。

- 将 :mc-cmd:`MEMBER <mc admin group add MEMBERS>` 替换为 S3 主机上
  *至少* 一个 :mc-cmd:`用户 <mc admin user>`。多个成员可按列表方式指定：
  ``MEMBER1 MEMBER2 MEMBER3``

列出可用组
~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin group ls` 列出兼容 S3 主机上的所有组：

.. code-block:: shell
   :class: copyable

   mc admin group ls ALIAS

- 将 :mc-cmd:`ALIAS <mc admin group ls TARGET>` 替换为兼容 S3 主机的
  :mc-cmd:`alias <mc alias>`。


查看组详情
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin group info` 查看兼容 S3 主机上的组详细信息：

.. code-block:: shell
   :class: copyable

   mc admin group info ALIAS GROUPNAME

- 将 :mc-cmd:`ALIAS <mc admin group info TARGET>` 替换为兼容 S3 主机的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`GROUPNAME <mc admin group info GROUPNAME>` 替换为
  组名。

删除组
~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin group rm` 从兼容 S3 主机删除组：

.. code-block:: shell
   :class: copyable

   mc admin group rm ALIAS GROUPNAME

- 将 :mc-cmd:`ALIAS <mc admin group rm TARGET>` 替换为兼容 S3 主机的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`GROUPNAME <mc admin group rm GROUPNAME>` 替换为
  组名。

禁用组
~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin group disable` 禁用兼容 S3 主机上的组：

.. code-block:: shell
   :class: copyable

   mc admin group disable ALIAS GROUPNAME

- 将 :mc-cmd:`ALIAS <mc admin group disable TARGET>` 替换为兼容 S3 主机的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`GROUPNAME <mc admin group disable GROUPNAME>` 替换为组名。

启用组
~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin group enable` 启用兼容 S3 主机上的组：

.. code-block:: shell
   :class: copyable

   mc admin group enable ALIAS GROUPNAME

- 将 :mc-cmd:`ALIAS <mc admin group enable TARGET>` 替换为兼容 S3 主机的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`GROUPNAME <mc admin group enable GROUPNAME>` 替换为组名。


快速参考
---------------

:mc-cmd:`mc admin group add TARGET GROUPNAME MEMBERS <mc admin group add>`
   将用户添加到 MinIO 部署上的组。如果组不存在，则创建该组。

:mc-cmd:`mc admin group info TARGET GROUPNAME <mc admin group info>`
   返回 MinIO 部署上某个组的详细信息。

:mc-cmd:`mc admin group ls TARGET <mc admin group ls>`
   返回 MinIO 部署上所有组的列表。

:mc-cmd:`mc admin group rm TARGET GROUPNAME <mc admin group rm>`
   删除 MinIO 部署上的组。

:mc-cmd:`mc admin group enable TARGET GROUPNAME <mc admin group enable>`
   启用 MinIO 部署上的组。用户只能继承已启用组分配的
   :ref:`策略 <minio-policy>`。

:mc-cmd:`mc admin group disable TARGET GROUPNAME <mc admin group disable>`
   禁用 MinIO 部署上的组。用户不能继承已禁用组分配的 :ref:`策略
   <minio-policy>`。

语法
------

.. mc-cmd:: add
   :fullpath:

   将现有用户添加到组中。如果组不存在，该命令会创建组。
   命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin group add TARGET GROUPNAME MEMBERS

   该命令接受以下参数：

   .. mc-cmd:: TARGET

      已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，命令会在该部署上
      向新建或现有组添加用户

   .. mc-cmd:: GROUPNAME

      组名。如果组尚不存在，该命令会创建组。可使用
      :mc-cmd:`mc admin group ls` 查看部署上的现有组。

      组名不能包含 ``=``（等号）或 ``,``（逗号）字符。

   .. mc-cmd:: MEMBERS

      要添加到组中的用户名。

      该用户*必须*存在于 :mc-cmd:`~mc admin group add TARGET` MinIO
      部署上。可使用 :mc-cmd:`mc admin user ls` 查看部署上的可用
      用户。

.. mc-cmd:: info
   :fullpath:

   返回目标部署上该组的详细信息，例如组内所有 :ref:`用户 <minio-users>`
   以及分配的 :ref:`策略 <minio-policy>`。命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin group info TARGET GROUPNAME

   该命令接受以下参数：

   .. mc-cmd:: TARGET

      已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，用于从该部署中
      获取组信息。

   .. mc-cmd:: GROUPNAME

      组名。

.. mc-cmd:: ls, list
   :fullpath:

   列出目标 MinIO 部署上的所有组。命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin group ls TARGET

   该命令接受以下参数：

   .. mc-cmd:: TARGET

      已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，用于从该部署中
      获取组列表。

.. mc-cmd:: rm, remove
   :fullpath:

   删除目标 MinIO 部署上的组。删除组*不会*
   删除该组中的任何成员用户。请使用
   :mc-cmd:`mc admin user rm` 从组中移除用户。

   命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin group rm TARGET GROUPNAME

   该命令接受以下参数：

   .. mc-cmd:: TARGET

      已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，命令会在该部署上
      删除组。

   .. mc-cmd:: GROUPNAME

      要删除的组名。

.. mc-cmd:: enable
   :fullpath:

   启用目标 MinIO 部署上的组。用户只能从已启用组继承
   :ref:`策略 <minio-policy>`。
   组在创建时默认处于启用状态。命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin group enable TARGET GROUPNAME

   该命令接受以下参数：

   .. mc-cmd:: TARGET

      已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，命令会在该部署上
      启用组。

   .. mc-cmd:: GROUPNAME

      要启用的组名。

.. mc-cmd:: disable
   :fullpath:

   禁用目标 MinIO 部署上的组。用户不能从已禁用组继承
   :ref:`策略 <minio-policy>`。命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin group disable TARGET GROUPNAME

   该命令接受以下参数：

   .. mc-cmd:: TARGET

      已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，命令会在该部署上
      禁用组。

   .. mc-cmd:: GROUPNAME

      要禁用的组名。
