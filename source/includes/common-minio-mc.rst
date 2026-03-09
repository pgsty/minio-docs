.. start-minio-mc-globals

此命令支持 :ref:`全局标志 <minio-mc-global-options>` 中的任意选项。

.. end-minio-mc-globals

.. start-minio-mc-json-globals

.. mc-cmd:: --json
   :optional:

   启用 `JSON lines <http://jsonlines.org/>`_ 格式的控制台输出。

   例如：

   .. code-block:: shell
      :class: copyable

      mc --json COMMAND

.. end-minio-mc-json-globals

.. start-minio-mc-no-flags

此命令仅支持全局标志。

.. end-minio-mc-no-flags

.. start-minio-mc-s3-compatibility

:program:`mc` 命令行工具以兼容 AWS S3 API 为目标构建，并针对 MinIO 和 AWS S3
进行了测试，以验证预期的功能与行为。

对于其他 S3 兼容服务，MinIO 不提供任何保证，因为这些服务的 S3 API 实现未知，
因此不在支持范围内。虽然 :program:`mc` 命令 *可能* 仍能按文档说明工作，但此类
用法需要你自行承担风险。

.. end-minio-mc-s3-compatibility

.. start-minio-syntax

- 方括号 ``[]`` 表示可选参数。
- 同一行中的参数彼此相互依赖。
- 使用管道符 ``|`` 分隔的参数彼此互斥。

请先将示例复制到文本编辑器中并按需修改，再在终端 / shell 中运行命令。

.. end-minio-syntax
