=========================
``mc admin bucket quota``
=========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin bucket quota

.. versionchanged:: RELEASE.2022-12-13T00-23-28Z

   ``mc admin bucket quota`` 已被以下命令替代：

   - :mc-cmd:`mc quota set`
   - :mc-cmd:`mc quota info`
   - :mc-cmd:`mc quota clear`

说明
----

.. start-mc-admin-bucket-quota-desc

:mc-cmd:`mc admin bucket quota` 命令用于管理按存储桶设置的存储配额。

.. end-mc-admin-bucket-quota-desc

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

.. _mc-admin-bucket-quota-units:

计量单位
~~~~~~~~

:mc-cmd:`mc admin bucket quota --hard` 标志
接受以下不区分大小写的后缀，用于表示所指定大小值的单位：

.. list-table::
   :header-rows: 1
   :widths: 20 80
   :width: 100%

   * - 后缀
     - 单位大小

   * - ``k``
     - KB（千字节，1000 字节）

   * - ``m``
     - MB（兆字节，1000 千字节）

   * - ``g``
     - GB（吉字节，1000 兆字节）

   * - ``t``
     - TB（太字节，1000 吉字节）

   * - ``ki``
     - KiB（二进制千字节，1024 字节）

   * - ``mi``
     - MiB（二进制兆字节，1024 KiB）

   * - ``gi``
     - GiB（二进制吉字节，1024 MiB）

   * - ``ti``
     - TiB（二进制太字节，1024 GiB）

省略后缀时，默认单位为 ``bytes``。

示例
----

为存储桶配置硬配额
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin bucket quota` 搭配
:mc-cmd:`~mc admin bucket quota --hard` 标志，为存储桶指定硬配额。
硬配额可防止存储桶大小增长超过指定限制。

.. code-block:: shell
   :class: copyable

   mc admin bucket quota TARGET/BUCKET --hard LIMIT

- 将 ``TARGET`` 替换为已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
  将 ``BUCKET`` 替换为要设置硬配额的存储桶名称。

- 将 ``LIMIT`` 替换为存储桶可增长到的最大大小。
  例如，要将硬限制设置为 10 TB，请指定 ``10t``。
  支持的单位请参见 :ref:`mc-admin-bucket-quota-units`。

获取存储桶配额配置
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin bucket quota` 获取存储桶当前的配额配置：

.. code-block:: shell
   :class: copyable

   mc admin bucket quota TARGET/BUCKET

将 ``TARGET`` 替换为已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
将 ``BUCKET`` 替换为要获取配额的存储桶名称。

清除已配置的存储桶配额
~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin bucket quota` 搭配
:mc-cmd:`~mc admin bucket quota --clear` 标志，清除存储桶上的所有配额。

.. code-block:: shell
   :class: copyable

   mc admin bucket quota TARGET/BUCKET --clear

- 将 ``TARGET`` 替换为已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
  将 ``BUCKET`` 替换为要清除配额的存储桶名称。

语法
----

:mc-cmd:`mc admin bucket quota` 使用以下语法：

.. code-block:: shell
   :class: copyable

   mc admin bucket quota TARGET [ARGUMENTS]

:mc-cmd:`mc admin bucket quota` 支持以下参数：

.. mc-cmd:: TARGET

   命令为其创建配额的存储桶的完整路径。
   指定 MinIO 部署的 :mc-cmd:`alias <mc alias>` 作为路径前缀。
   例如：

   .. code-block:: shell
      :class: copyable

      mc admin bucket quota play/mybucket

   省略其他所有参数可返回指定存储桶的当前配额设置。

.. mc-cmd:: --hard
   

   设置存储桶存储大小的最大限制。对于内容会超过存储桶已配置配额的传入
   ``PUT`` 请求，MinIO 服务器会予以拒绝。

   例如，若硬限制为 ``10GB``，当存储桶大小达到 ``10GB`` 时，将无法再添加
   任何对象。

   支持的单位大小请参见 :ref:`mc-admin-bucket-quota-units`。

.. mc-cmd:: --clear
   

   清除为该存储桶配置的所有配额。

   
