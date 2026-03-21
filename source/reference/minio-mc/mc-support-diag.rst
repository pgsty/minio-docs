===================
``mc support diag``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. mc:: mc support diag

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only


.. dropdown:: 命令历史

   用于创建诊断报告的命令会随版本演进而变化。

   .. list-table::
      :header-rows: 1
      :widths: 40 30 30
      :width: 100%
   
      * - MinIO Client Release
        - 命令
        - 说明
   
      * - RELEASE.2022-02-13T23-26-13Z
        - ``mc support diag``
        - 命令移动到 ``mc support``
   
      * - RELEASE.2020-11-17T00-39-14Z 
        - ``mc admin subnet health``
        - 命令改为 SUBNET 子命令
   
      * - RELEASE.2020-10-03T02-54-56Z
        - ``mc admin health``
        - 命令重命名为 health
   
      * - Original Command
        - ``mc admin obd``
        - 命令重命名为 ``mc admin health``


描述
----

.. start-mc-support-diag-desc

:mc-cmd:`mc support diag` 命令用于为 MinIO 部署生成健康报告。

.. end-mc-support-diag-desc

对于已注册到 MinIO 订阅网络（|subnet-short|）的部署，该命令会生成并上传健康报告以供分析。
你也可以启用 :mc-cmd:`~mc support callhome`，将每 24 小时生成并上传报告的流程自动化。

生成的健康报告供 MinIO Engineering 通过 SUBNET 使用，可能包含内部或私有数据点。
在将健康报告发送给第三方或发布到公开论坛之前，请谨慎检查。

MinIO 建议在集群首次创建时执行健康诊断，并在出现故障场景时再次执行。

使用 :mc-cmd:`mc support diag` 命令触发诊断测试。
对于已注册 SUBNET 的集群，该命令会将结果作为 SUBNET 健康报告的一部分上传。

对于 airgapped、受防火墙限制或其他无法从部署直接访问网络的环境，可以使用 :mc-cmd:`~mc support diag --airgap` 标志将报告保存到本地。
保存后，你可以手动将测试结果上传到 SUBNET。


示例输出
~~~~~~~~

.. code-block:: shell

   ● CPU Info ... ✔ 
   ● Disk Info ... ✔ 
   ● Net Info ... ✔ 
   ● Os Info ... ✔ 
   ● Mem Info ... ✔ 
   ● Process Info ... ✔ 
   ● Server Config ... ✔ 
   ● System Errors ... ✔ 
   ● System Services ... ✔ 
   ● System Config ... ✔ 
   ● Admin Info ... ✔ 
   *********************************************************************************
                                   WARNING!!
        ** THIS FILE MAY CONTAIN SENSITIVE INFORMATION ABOUT YOUR ENVIRONMENT ** 
        ** PLEASE INSPECT CONTENTS BEFORE SHARING IT ON ANY PUBLIC FORUM **
   *********************************************************************************
   mc: MinIO diagnostics report saved to myminio-health_20231111053323.json.gz

gzip 压缩输出包含所请求的健康信息。


示例
----

为集群生成健康数据并自动上传到 SUBNET
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

为别名为 ``minio1`` 的 MinIO 集群生成健康数据，并自动传输到 SUBNET。

.. code-block:: shell
   :class: copyable

   mc support diag minio1

仅在使用商业许可证（Commerical License）的部署中才会自动上传数据。

.. _minio-support-diagnostics-airgap:

为集群生成健康数据并手动上传
~~~~~~~~~~~~~~~~~~~~~~~~~~

为别名为 ``minio2`` 的 MinIO 部署生成诊断报告，并保存以便手动上传到 SUBNET：

.. code-block:: shell
   :class: copyable

   mc support diag minio2 --airgap

#. 运行命令下载 ``.gzip`` 文件
#. 登录 https://subnet.min.io 并选择 :guilabel:`Deployments` 页面
#. 选择该报告对应的部署
#. 点击 :guilabel:`Upload` 按钮
#. 拖放文件，或浏览到 ``.gzip`` 文件所在位置进行上传

使用严格匿名化将数据上传到 SUBNET
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

为别名为 ``myminio`` 的 MinIO 集群生成健康数据，并对包括主机名在内的所有敏感数据进行匿名化。

.. code-block:: shell
   :class: copyable

   mc support diag myminio --anonymize=strict


语法
----
      
该命令语法如下：

.. code-block:: shell

   mc [GLOBALFLAGS] support diag                   \
                            ALIAS                  \
                            [--airgap]             \
                            [--anonymize=<string>] \
                            [--api-key string]


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`。

.. mc-cmd:: --airgap
   :optional:

   用于无法访问 SUBNET 网络的环境（例如 airgapped、受防火墙限制或类似配置）。
   该参数会生成诊断报告并将其保存到运行命令时所在的位置。

   你必须手动将报告上传到 SUBNET。
   
   说明请参见 :ref:`airgap 示例 <minio-support-diagnostics-airgap>`。

   如果部署本身是 airgapped，但运行 :ref:`minio client <minio-client>` 的本地设备具备网络访问能力，则无需使用 ``--airgap`` 标志。

.. mc-cmd:: --anonymize

   .. versionadded:: mc RELEASE.2023-11-10T21-37-17Z

   MinIO 会对上传到 SUBNET 的数据做匿名化处理。
   从 mc ``RELEASE.2023-11-10T21-37-17Z`` 开始，MinIO *不会* 匿名化主机名。
   这是默认的 ``standard`` 匿名化模式。

   可用值为 ``=strict`` 或 ``=standard``。

   若要匿名化包括主机名在内的全部数据，请将该参数设置为 ``strict`` 模式。

   .. code-block:: shell
      :class: copyable

      mc support diag minio --anonymize=strict

.. mc-cmd:: --api-key
   :optional:

   使用账户在 SUBNET 中的 API key 值。
   
   仅在 airgapped 环境且 MinIO 尚未为该部署保存 API key 时才需要提供此值。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
