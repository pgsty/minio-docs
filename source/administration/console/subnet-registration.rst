.. _minio-console-subscription:

======
SUBNET
======

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2


您可以使用 MinIO Console 执行 MinIO 中若干与许可证和订阅相关的功能，例如：

- 查看 MinIO 部署当前使用的许可证。
- 订阅商业许可证，其中包含对 |SUBNET| 的访问权限。
- 管理部署的 Enterprise 许可证。
- 使用可与 MinIO Engineering 共享的支持工具。
- 查看不同许可证选项之间的差异。

许可证
------

MinIO 提供三种许可证选项：

#. 基于 :minio-git:`GNU AGPLv3 license <mc/blob/master/LICENSE>` 的开源版本
#. Enterprise Lite，一种 `commercial license <https://min.io/pricing?ref=docs>`__，包含由 MinIO Engineers 直接提供的支持
#. Enterprise Plus，一种 `commercial license <https://min.io/pricing?ref=docs>`__，包含由 MinIO Engineers 直接提供的支持、较长的发布周期、更短的 SLA 以及其他优势

:guilabel:`License` 页面显示部署当前的许可证状态。
您还可以开始注册流程，以开通付费订阅或将该部署添加到现有订阅中。

采用 AGPLv3 许可的部署必须遵守该许可证条款。
MinIO 无法判定您的应用程序对 MinIO 的使用是否符合 AGPLv3 的许可证要求。
您应当依赖自己的法律顾问或许可证专家，对应用程序进行审计，并确保其符合 MinIO 以及所有与您的应用程序集成或交互的其他开源项目的许可证要求。

对于会触发 AGPLv3 义务的应用程序（例如需要将应用开源），MinIO Commercial Licensing 是最佳选择。
在未验证其使用方式的情况下使用 MinIO 或任何其他采用 OSS 许可证代码的应用程序，风险由使用者自行承担。

健康
----

:guilabel:`Health` 部分提供了一个界面，用于对 MinIO 部署运行健康诊断。
对于连接到 Internet 的集群，报告会自动上传到 SUBNET。
      
生成的健康报告供 MinIO Engineering 通过 |subnet| 使用，其中可能包含诸如主机名等内部或私有数据点。
在将健康报告发送给第三方或发布到公开论坛之前，请务必谨慎评估。

如有需要，您可以从该页面下载最新报告。

性能
----

:guilabel:`Performance` 部分提供了一个界面，用于对部署执行性能测试。
测试结果可作为部署在 S3 ``GET`` 和 ``PUT`` 请求下性能表现的一般性参考。

如需更完整的性能测试，可考虑结合使用预发布应用环境中的负载测试以及 MinIO :minio-git:`WARP <warp>` 工具。

Profile
-------

:guilabel:`Profile` 部分提供了一个界面，用于对部署执行系统分析。
结果可以帮助了解给定节点上运行的 MinIO 服务进程。

生成的报告供 MinIO Engineering 通过 |subnet| 使用。
独立使用或由第三方使用这些分析结果进行诊断和修复，风险由您自行承担。

Inspect
-------

:guilabel:`Inspect` 部分提供了一个界面，用于捕获与一个或多个对象相关的纠删码元数据。
MinIO Engineering 可能会要求提供此输出，作为 |subnet| 中诊断的一部分。

生成的对象可使用 MinIO 的 :minio-git:`debugging tool <minio/tree/master/docs/debugging#decoding-metadata>` 读取。
独立使用或由第三方使用该输出进行诊断或修复，风险由您自行承担。
您还可以选择对该对象进行加密，使其仅在调试工具链中包含所生成的加密密钥时才能被读取。

Call Home
---------

.. versionadded:: Console v0.24.0

Call Home 是一个可选功能，已注册到 |SUBNET| 的部署可以自动将每日健康诊断报告或实时错误日志发送到 SUBNET。
这些报告可为工程支持团队在响应支持请求时提供诊断记录、日志记录，或同时提供两者。

MinIO 安装后默认禁用 Call Home 选项。

.. important:: 

   Call Home 需要有效的 Enterprise 许可证。

使用 :guilabel:`Call Home` 部分可启用或禁用将每日一次的健康诊断报告或实时错误日志上传到 SUBNET。
健康报告和实时日志是彼此独立的功能，您可以分别启用或禁用。
如有需要，您也可以同时启用诊断报告和日志。
