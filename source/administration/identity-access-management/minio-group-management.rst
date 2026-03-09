.. _minio-groups:

================
组管理
================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

概述
--------

*组* 是 :ref:`用户 <minio-users>` 的集合。每个组
都可以分配一个或多个 :ref:`策略 <minio-policy>`，
这些策略会显式列出允许或拒绝组成员访问的操作和资源。

例如，考虑以下几个组。每个组都分配了一个
:ref:`内置策略 <minio-policy-built-in>` 或受支持的
:ref:`策略操作 <minio-policy-actions>`。每个组还分配了一个或
多个用户。每个用户的完整权限集合由其
显式分配的权限 *以及* 从其所属各组继承的权限共同组成。
对于用户被分配或继承的策略中未显式允许的任何资源或操作，MinIO 默认都会 *拒绝* 访问。

.. list-table::
   :header-rows: 1
   :widths: 20 40 40
   :width: 100%

   * - 组
     - 策略
     - 成员

   * - ``Operations``
     - | ``finance`` 存储桶上的 :userpolicy:`readwrite`
       | ``audit`` 存储桶上的 :userpolicy:`readonly`
     
     - ``john.doe``, ``jane.doe``

   * - ``Auditing``
     - | ``audit`` 存储桶上的 :userpolicy:`readonly`
     - ``jen.doe``, ``joe.doe``

   * - ``Admin``
     - :policy-action:`admin:*`
     - ``greg.doe``, ``jen.doe``

组为具有相同访问模式和工作负载的用户之间管理共享权限提供了一种更简便的方法。
客户端 *不能* 使用组作为身份向 MinIO 部署进行认证。


:mc:`mc admin group` 命令支持在 MinIO 部署上创建和管理组。
有关用法示例，请参阅该命令的参考文档。
