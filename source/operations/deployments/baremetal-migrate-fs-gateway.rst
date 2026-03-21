.. _minio-gateway-migration:

===================================
从 Gateway 或 Filesystem 模式迁移
===================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

背景
----

MinIO Gateway 及相关 filesystem 模式自 2020 年 7 月起进入功能冻结状态。
2022 年 2 月，MinIO 宣布 `弃用 MinIO Gateway <https://blog.min.io/deprecation-of-the-minio-gateway/?ref=docs>`__。
在该弃用公告中，MinIO 同时宣布该功能将在六个月后移除。

从 :minio-release:`RELEASE.2022-10-29T06-21-33Z` 起，MinIO Gateway 和相关 filesystem 模式代码已被移除。
仍在使用 `standalone` 或 `filesystem` MinIO 模式的部署，一旦升级到 MinIO 服务端 :minio-release:`RELEASE.2022-10-29T06-21-33Z` 或更高版本，在尝试启动 MinIO 时会报错。


概览
----

若要升级到 :minio-release:`RELEASE.2022-10-29T06-21-33Z` 或更高版本，原先使用 `standalone` 或 `filesystem` 部署模式的用户必须新建一个 :ref:`单机单盘 <minio-snsd>` 部署，并将设置和内容迁移到新部署中。

本文概述成功启动并迁移到新部署所需的步骤。

.. important:: 

   单机/文件系统 模式在截至 MinIO Server `RELEASE.2022-10-24T18-35-07Z <https://github.com/minio/minio/releases/tag/RELEASE.2022-10-24T18-35-07Z>`__ 的所有版本中仍可使用。
   若要继续使用 standalone 部署，请安装该 MinIO 服务端版本以及 MinIO Client `RELEASE.2022-10-29T10-09-23Z <https://github.com/minio/mc/releases/tag/RELEASE.2022-10-29T10-09-23Z>`__，或安装任意 `更早版本 <https://github.com/minio/minio/releases>`__ 及其对应的 MinIO 客户端。请注意，MinIO Client 的版本应更新，且尽可能接近 MinIO 服务端的版本。

   Filesystem 模式部署至少需要升级到 `RELEASE.2022-06-25T15-50-16Z <https://github.com/minio/minio/releases/tag/RELEASE.2022-06-25T15-50-16Z>`__，才能使用 MinIO 客户端 的导入和导出命令。
   对于截至 `RELEASE.2022-06-20T23-13-45Z <https://github.com/minio/minio/releases/tag/RELEASE.2022-06-20T23-13-45Z>`__ 的 filesystem 模式部署，可以通过在新部署上手动重建用户、策略、存储桶和其他资源完成迁移。


步骤
----

.. note:: 
   
   你可以通过环境变量和 :mc-cmd:`mc admin config set <mc admin config set>` 设置 MinIO 配置项。
   根据你当前的部署方式，可能需要同时获取这两类配置值。

   你可以使用 ``env | grep MINIO_`` 查看运行时设置；对于使用 MinIO systemd 服务的部署，也可以直接检查 ``/etc/default/minio`` 的内容。

#. 对于 filesystem 模式部署：

   如有需要，先升级现有部署。

   可接受的最低版本为：

   - MinIO `RELEASE.2022-06-25T15-50-16Z <https://github.com/minio/minio/releases/tag/RELEASE.2022-06-25T15-50-16Z>`__
   - MinIO Client `RELEASE.2022-06-26T18-51-48Z <https://github.com/minio/mc/releases/tag/RELEASE.2022-06-26T18-51-48Z>`__

   可接受的最高版本为：

   - MinIO `RELEASE.2022-10-24T18-35-07Z <https://github.com/minio/minio/releases/tag/RELEASE.2022-10-24T18-35-07Z>`__
   - MinIO Client `RELEASE.2022-10-29T10-09-23Z <https://github.com/minio/mc/releases/tag/RELEASE.2022-10-29T10-09-23Z>`__

#. 创建新的 单机单盘 MinIO 部署。

   按照适用于所选操作系统的 :ref:`安装说明 <deploy-minio-standalone>`，将安装配置为 单机单盘 (SNSD) 拓扑。

   部署位置可以是所选存储介质上的任意空目录。
   如果现有部署不在磁盘根目录上，则同一块磁盘上的新目录也可以用于新部署。
   如果现有 standalone 系统指向磁盘根目录，则新部署必须使用另一块独立磁盘。

   如果旧部署和新部署位于同一主机上：
   
   - 将新部署安装到不同于现有部署的路径。
   - 将新部署的 Console 和 API 端口设置为不同于现有部署的端口。

     以下命令行选项可在启动时设置端口：

     - :mc-cmd:`~minio server --address` 用于设置 API 端口。
     - :mc-cmd:`~minio server --console-address` 用于设置 Console 端口。

   - 对于由 ``systemd`` 管理的部署：

     - 复制现有 ``/etc/default/minio`` 环境文件，并使用唯一的新文件名。
     - 在新部署的服务文件中，更新 ``EnvironmentFile`` 以引用新的环境文件。

   以下步骤会同时使用两个部署中的 :mc:`mc` 命令行工具。
   *现有 MinIO Client* 指旧部署中的 :mc:`mc`。
   *新的 MinIO 客户端* 指新部署中的 :mc:`mc`。

#. 使用 :mc:`mc alias set` 和新的 MinIO 客户端 为上一步创建的部署添加别名。

   .. code-block:: shell
      :class: copyable
      
      mc alias set NEWALIAS PATH ACCESSKEY SECRETKEY

   - 使用新的 MinIO 客户端。
   - 将 ``NEWALIAS`` 替换为要为该部署创建的别名。
   - 将 ``PATH`` 替换为新部署的 IP 地址或主机名及端口。
   - 将 ``ACCESSKEY`` 和 ``SECRETKEY`` 替换为创建新部署时使用的凭证。

#. 根据部署类型迁移设置：

   - MinIO Gateway 是一个无状态代理服务，为多种后端存储系统提供 S3 API 兼容层。

   - Filesystem 模式部署为单个 MinIO server 进程和单个存储卷提供 S3 访问层。

   .. tab-set::

      .. tab-item:: Gateway

         迁移配置设置：

         如果你的部署使用 :ref:`环境变量 <minio-server-environment-variables>` 作为配置方式，请将现有部署 ``/etc/default/minio`` 文件中的环境变量复制到新部署的同名文件中。
         你可以省略所有 ``MINIO_CACHE_*`` 和 ``MINIO_GATEWAY_SSE`` 环境变量，因为这些变量已不再使用。                                                               

         如果你使用 :mc-cmd:`mc admin config set <mc admin config set>` 管理配置，请使用新的 MinIO 客户端将现有设置复制到新部署中。

      .. tab-item:: Filesystem mode

         .. note::

            以下 filesystem 模式步骤默认现有 MinIO Client 支持所需的导出命令。
            如果不支持，请在新部署上使用新的 MinIO 客户端 手动重建用户、策略、生命周期规则和存储桶。

         a. 导出现有部署的**配置**。

            使用现有 MinIO Client 运行 :mc-cmd:`mc admin config export <mc admin config export>`，导出现有 standalone MinIO 部署中定义的配置。

            .. code-block:: shell
               :class: copyable

               mc admin config export ALIAS > config.txt

            - 使用现有 MinIO Client。
            - 将 ``ALIAS`` 替换为现有 standalone 部署的别名，也就是你要从中导出配置的目标。 

         b. 使用新的 MinIO 客户端将现有 standalone 部署的**配置**导入到新部署。

            .. code-block:: shell
               :class: copyable

               mc admin config import ALIAS < config.txt

            - 使用新的 MinIO 客户端。
            - 将 ``ALIAS`` 替换为新部署的别名。

            如果 :mc-cmd:`~mc admin config import` 对某个配置键报错，请在对应行开头加上 ``#`` 注释掉，再重新尝试。
            完成迁移后，请根据目标 MinIO 服务端版本核对当前配置语法，并使用 :mc-cmd:`mc admin config set` 手动设置所需键值。

         c. 使用新的 MinIO 客户端 重启新部署的服务。

            .. code-block:: shell
               :class: copyable

               mc admin service restart ALIAS
   
            - 使用新的 MinIO 客户端。
            - 将 ``ALIAS`` 替换为新部署的别名。

         d. 使用现有 MinIO Client 导出现有 standalone 部署的**存储桶元数据**。

            以下命令会将现有部署中的存储桶元数据导出到一个 ``.zip`` 文件中。

            数据包括：

            - 存储桶目标
            - 生命周期规则
            - 通知
            - 配额
            - 锁
            - 版本控制

            导出内容仅包含存储桶元数据。
            此命令不会导出现有部署中的对象数据。

            .. code-block:: shell
               :class: copyable

               mc admin cluster bucket export ALIAS

            - 使用现有 MinIO Client。
            - 将 ``ALIAS`` 替换为现有部署的别名。

            此命令会生成 ``cluster-metadata.zip`` 文件，其中包含每个存储桶的元数据。

         e. 使用新的 MinIO 客户端将**存储桶元数据**导入到新部署。

            以下命令会读取导出的存储桶 ``.zip`` 文件内容，并在新部署上创建具有相同配置的存储桶。

            .. code-block:: shell
               :class: copyable

               mc admin cluster bucket import ALIAS cluster-metadata.zip

            - 使用新的 MinIO 客户端。
            - 将 ``ALIAS`` 替换为新部署的别名。

            该命令会依据现有部署导出的 .zip 文件中的元数据，在新部署上创建具有相同配置的存储桶。

         f. 使用现有 MinIO 客户端将现有 standalone 部署的**IAM 设置**导出到新部署。

            如果你使用的是外部身份与访问管理提供方，请在新部署中重建这些设置及所有关联策略。

            使用以下命令从现有部署导出 IAM 设置。
            此命令会导出：

            - 组和组映射
            - STS 用户和 STS 用户映射
            - 策略
            - 用户和用户映射

            .. code-block:: shell
               :class: copyable

               mc admin cluster iam export ALIAS

            - 使用现有 MinIO Client。
            - 将 ``ALIAS`` 替换为现有部署的别名。

            此命令会生成包含 IAM 数据的 ``ALIAS-iam-info.zip`` 文件。

         g. 使用新的 MinIO 客户端将**IAM 设置**导入到新部署。

            使用导出的文件在新部署上创建 IAM 设置。

            .. code-block:: shell
               :class: copyable

               mc admin cluster iam import ALIAS alias-iam-info.zip

            - 使用新的 MinIO 客户端。
            - 将 ``ALIAS`` 替换为新部署的别名。
            - 将 zip 文件名替换为现有部署导出的文件名。

#. 使用 :mc:`mc mirror` 迁移存储桶内容。

   在 standalone 部署上，使用现有 MinIO Client 运行带有 :mc-cmd:`~mc mirror --preserve` 和 :mc-cmd:`~mc mirror --watch` 参数的 :mc:`mc mirror`，将对象迁移到新的 |SNSD| 部署中。

   .. code-block:: shell
      :class: copyable

      mc mirror --preserve --watch SOURCE/BUCKET TARGET/BUCKET

   - 使用现有 MinIO Client。
   - 将 ``SOURCE/BUCKET`` 替换为现有 standalone 部署的别名和存储桶。
   - 将 ``TARGET/BUCKET`` 替换为新部署的别名和对应存储桶。

#. 停止所有 S3 或 POSIX 客户端向 standalone 部署写入数据。

#. 等待 ``mc mirror`` 完成所有存储桶的剩余操作。

#. 停止两个部署的服务。

#. 使用之前 standalone 部署所用的端口重启新的 MinIO 部署。
   
   确保应用所有环境变量和运行时配置设置，并验证新部署的行为是否符合预期。
