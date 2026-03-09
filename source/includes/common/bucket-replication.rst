.. start-create-bucket-replication-rule-cli-desc

使用 :mc:`mc replicate add` 命令，为每个 MinIO 部署添加新的复制规则。

.. code-block:: shell
   :class: copyable

   mc replicate add ALIAS/BUCKET \
      --remote-bucket 'https://USER:PASSWORD@HOSTNAME:PORT/BUCKET' \
      --replicate "delete,delete-marker,existing-objects"

- 将 ``ALIAS`` 替换为源 MinIO 部署的 :ref:`别名 <alias>`。
  该名称 *必须* 与上一步创建远端目标时指定的存储桶名称一致。

- 将 ``BUCKET`` 替换为源部署上要作为复制源的存储桶名称。

- 使用 ``--remote-bucket`` 指定 ``ALIAS/BUCKET`` 要复制到的远端 MinIO 部署和存储桶。

  ``USER:PASSWORD`` 必须对应远端部署上具有
  :ref:`所需复制权限 <minio-bucket-replication-serverside-twoway-permissions>` 的用户。

  ``HOSTNAME:PORT`` 必须能解析到远端部署上可访问的 MinIO 实例。
  ``BUCKET`` 必须已存在，并满足其他所有
  :ref:`复制要求 <minio-bucket-replication-requirements>`。

- ``--replicate "delete,delete-marker,existing-objects"`` 标志会启用以下复制功能：
  
  - :ref:`删除复制 <minio-replication-behavior-delete>`
  - :ref:`现有对象复制 <minio-replication-behavior-existing-objects>`
  
  有关更完整的文档，请参阅 :mc-cmd:`mc replicate add --replicate`。
  省略任意字段即可禁用对应组件的复制。

可按需为 :mc:`mc replicate add` 指定其他受支持的可选参数。

.. end-create-bucket-replication-rule-cli-desc

.. start-validate-bucket-replication-cli-desc

在其中一个部署上，使用 :mc:`mc cp` 将新对象复制到已启用复制的存储桶中。

.. code-block:: shell
   :class: copyable

   mc cp ~/foo.txt ALIAS/BUCKET

使用 :mc:`mc ls` 验证目标存储桶中存在该对象：

.. code-block:: shell
   :class: copyable

   mc ls ALIAS/BUCKET

.. end-validate-bucket-replication-cli-desc
