.. start-sse-dek

MinIO 使用 |EK| 生成数据加密密钥（DEK）。
具体来说，:minio-git:`MinIO Key Encryption Service (KES) <kes>` 会以 |EK|
作为“根”密钥，向 KMS 请求新的加密密钥。

KES 会返回 DEK 的明文形式 *以及* 其经过 |EK| 加密后的表示。
MinIO 将加密后的表示作为对象元数据的一部分进行存储。

.. end-sse-dek

.. start-sse-kek

MinIO 使用确定性算法生成唯一的 256 位密钥加密密钥（KEK）。
该密钥派生算法使用伪随机函数，并以明文 |DEK|、随机生成的初始化向量以及由
存储桶名称、对象名称等值构成的上下文作为输入。

MinIO 会在每次加密或解密操作时生成 KEK，并且 *绝不会* 将 KEK 存储到磁盘上。

.. end-sse-kek

.. start-sse-oek

MinIO 会生成随机且唯一的 256 位对象加密密钥（OEK），并使用该密钥加密对象。
MinIO 不会将 OEK 的明文形式存储到磁盘上。
在加密或解密操作期间，OEK 的明文仅驻留在 RAM 中。

.. end-sse-oek

.. start-minio-mc-sse-options

.. mc-cmd:: --enc-kms

   使用客户端管理的密钥，通过服务端
   :ref:`SSE-KMS 加密 <minio-sse>` 对对象进行加密或解密。
   
   该参数接受格式为 ``KEY=VALUE`` 的键值对。

   .. list-table::
      :stub-columns: 1
      :widths: 30 70
      :width: 100%

      * - ``KEY``
        - 对象的完整路径，格式为 ``alias/bucket/path/object.ext``。

          你也可以只指定顶层路径，以便对该路径下的所有操作使用同一个加密密钥。

      * - ``VALUE``
        - 指定外部 KMS 上已有的数据密钥。
        
          关于如何创建数据密钥，请参见 :mc-cmd:`mc admin kms key create` 参考文档。

   例如：

   .. code-block:: shell

      --enc-kms "myminio/mybucket/prefix/object.obj=mybucketencryptionkey"

   你可以通过重复该参数来指定多个加密密钥。

   也可以指定某个前缀路径，对该路径下所有匹配对象应用加密：

   .. code-block:: shell

      --enc-kms "myminio/mybucket/prefix/=mybucketencryptionkey"

.. mc-cmd:: --enc-s3
   :optional:

   使用 KMS 管理的密钥，通过服务端 :ref:`SSE-S3 加密 <minio-sse>`
   对对象进行加密或解密。
   指定对象的完整路径，格式为 ``alias/bucket/prefix/object``。

   例如：

   .. code-block:: shell

      --enc-s3 "myminio/mybucket/prefix/object.obj"

   你可以多次指定该参数，以表示不同的待加密对象：

   .. code-block:: shell

      --enc-s3 "myminio/mybucket/foo/fooobject.obj" --enc-s3 "myminio/mybucket/bar/barobject.obj"

   也可以指定某个前缀路径，对该路径下所有匹配对象应用加密：

   .. code-block:: shell

      --enc-s3 "myminio/mybucket/foo"

.. start-minio-mc-sse-c-only

.. mc-cmd:: --enc-c
   :optional:

   使用客户端管理的密钥，通过服务端 :ref:`SSE-C 加密 <minio-sse>`
   对对象进行加密或解密。
   
   该参数接受格式为 ``KEY=VALUE`` 的键值对。

   .. list-table::
      :stub-columns: 1
      :widths: 30 70
      :width: 100%

      * - ``KEY``
        - 对象的完整路径，格式为 ``alias/bucket/path/object.ext``。

          你也可以只指定顶层路径，以便对该路径下的所有操作使用同一个加密密钥。

      * - ``VALUE``
        - 指定用于 SSE-C 加密的密钥，可以是 32 字节 RawBase64 编码密钥，
          *也可以是* 64 字节十六进制编码密钥。
          
          Raw Base64 编码 **不接受** 带 ``=`` 填充的密钥。
          请去掉填充，或使用支持 RAW 格式的 Base64 编码器。

   - ``KEY`` - 对象的完整路径，格式为 ``alias/bucket/path/object``。
   - ``VALUE`` - 用于加密对象的 32 字节 RAW Base64 编码数据密钥。

   例如：

   .. code-block:: shell

      # RawBase64-Encoded string "mybucket32byteencryptionkeyssec"
      --enc-c "myminio/mybucket/prefix/object.obj=bXlidWNrZXQzMmJ5dGVlbmNyeXB0aW9ua2V5c3NlYwo"

   你可以通过重复该参数来指定多个加密密钥。

   也可以指定某个前缀路径，对该路径下所有匹配对象应用加密：

   .. code-block:: shell

      --enc-c "myminio/mybucket/prefix/=bXlidWNrZXQzMmJ5dGVlbmNyeXB0aW9ua2V5c3NlYwo"

   .. note::

      MinIO 强烈不建议在生产负载中使用 SSE-C 加密。
      请改用 ``--enc-kms`` 参数启用 SSE-KMS，或使用 ``--enc-s3`` 参数启用
      SSE-S3。

.. end-minio-mc-sse-options
