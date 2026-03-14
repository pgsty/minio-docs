.. _minio-batch-framework-expire-job:

========
批量过期
========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. versionadded:: MinIO RELEASE.2023-12-02T10-51-33Z

MinIO 批处理框架允许你使用 YAML 格式的作业定义文件（“batch file”）创建、管理、监控和执行作业。
批处理作业直接在 MinIO 部署上运行，从而利用服务端处理能力，而不受运行 :ref:`MinIO Client <minio-client>` 的本地机器限制。

``expire`` 批处理作业会将 :ref:`minio-lifecycle-management-create-expiry-rule` 的行为应用到单个存储桶。
该作业根据提供的配置判断对象是否符合过期条件，且独立于任何已配置的过期规则。

行为
----

对象立即过期
~~~~~~~~~~~~

批量过期会作为批处理作业的一部分立即执行，这与 :ref:`passive scanner-based application of expiration rules <minio-lifecycle-management-scanner>` 不同。
具体来说，批量过期不会让位于应用 I/O，因此可能影响部署上常规读写操作的性能。

过期资格在批处理运行时确定
~~~~~~~~~~~~~~~~~~~~~~~~~~

批量过期按存储桶工作，并且每次运行都会一次性执行直至完成。
该作业在运行时判断对象是否符合过期条件，并且*不会*定期重新扫描或重新检查新对象。

如果要处理任何新近满足过期条件的对象，请重新运行该批处理作业。

过期规则仅检查最新对象
~~~~~~~~~~~~~~~~~~~~~~

批量过期作业只会使用每个对象的最新版本或“current”版本来匹配各条批量过期规则。


.. _minio-batch-framework-expire-job-ref:

``expire`` 批处理作业参考
-------------------------

.. list-table::
   :widths: 25 75
   :width: 100%

   * - 字段
     - 说明

   * - ``expire``
     - *必需* 
       
       过期作业类型的顶层字段。

   * - ``apiVersion``
     - *必需*
        
       设为 ``v1``。

   * - ``bucket``
     - *必需*
       
       指定该作业运行所在的存储桶名称。

   * - ``prefix``
     - *可选*
      
       指定该作业运行所在的存储桶前缀。

   * - ``rules``
     - *必需*
       
       一个包含一条或多条过期规则的数组，用于应用到指定 ``bucket`` 和 ``prefix`` （如有）中的对象。

   * - ``rules.[n].type``
     - *必需*

       支持以下两个值之一：

       - ``object`` - 仅应用于当前版本**不是** ``DeleteMarker`` 的对象。
       - ``deleted`` - 仅应用于当前版本**是** ``DeleteMarker`` 的对象。

       有关 ``DeleteMarker`` 或版本控制存储桶中删除操作的更完整文档，请参见 :ref:`minio-object-delete`。

   * - ``rules.[n].name``
     - *可选*

       指定用于过滤对象的匹配字符串。

       支持 glob 风格通配符（``*``、``?``）。

   * - ``rules.[n].olderThan``
     - *可选*

       指定对象年龄以过滤对象。
       该规则仅应用于年龄超过指定时间单位的对象。

       例如，``72h`` 或 ``3d`` 会选择年龄超过三天的对象。

   * - ``rules.[n].createdBefore``
     - *可选*

       指定一个 :rfc:`RFC3339 <3339>` 日期时间来过滤对象。

       该规则仅应用于在指定时间戳*之前*创建的对象。

   * - ``rules.[n].tags``
     - *可选*

       指定一个键值对数组，用于描述对象标签并据此过滤对象。
       ``value`` 条目支持 glob 风格通配符（``*``、``?``）。

       例如，以下配置会将该规则过滤为仅匹配具有相应标签的对象：

       .. code-block:: yaml

          tags:
            - key: archive
              value: True

       此键与 ``rules.[n].type: deleted`` 不兼容。

   * - ``rules.[n].metadata``
     - *可选*

       指定一个键值对数组，用于描述对象元数据并据此过滤对象。
       ``value`` 键支持 glob 风格通配符（``*``、``?``）。

       例如，以下配置会将该规则过滤为仅匹配具有相应元数据的对象：

       .. code-block:: yaml

          metadata:
            - key: content-type
              value: image/*

       此键与 ``rules.[n].type: deleted`` 不兼容。

   * - ``rules.[n].size``
     - *可选*

       指定对象大小范围以过滤对象。

       - ``lessThan`` - 匹配大小小于指定数值的对象（例如 ``MiB``、``GiB``）。
       - ``greaterThan`` - 匹配大小大于指定数值的对象（例如 ``MiB``、``GiB``）。

   * - ``rules.[n].purge.retainVersions``
     - *可选*

       指定在执行过期时要保留的对象版本数量。

       默认为 ``0``，即删除所有对象版本（最快）。

   * - ``notify.endpoint``
     - *可选*

       用于发送通知事件的预定义端点。

   * - ``notify.token``
     - *可选*

       用于访问 ``notify.endpoint`` 的可选 JSON Web Token (JWT)。

   * - ``retry.attempts``
     - *可选*

       在放弃之前完成该批处理作业的重试次数。

   * - ``retry.delay``
     - *可选*

       每次尝试之间的等待时间（``ms``）。

``expire`` 作业类型的 YAML 示例
----------------------------------------
       
使用 :mc:`mc batch generate` 创建基础 ``expire`` 批处理作业，再进行进一步定制。

.. literalinclude:: /includes/code/expire.yaml
   :language: yaml
