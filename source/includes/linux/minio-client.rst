.. start-mc-limit-flags-desc

.. mc-cmd:: --limit-download
   :optional:

   将客户端侧下载速率限制为不超过指定值，单位可以是 KiB/s、MiB/s 或 GiB/s。
   这只影响下载到运行 MinIO Client 的本地设备的速度。
   有效单位包括：
   
   - ``B`` 表示字节
   - ``K`` 表示千字节
   - ``M`` 表示兆字节
   - ``G`` 表示吉字节
   - ``T`` 表示太字节
   - ``Ki`` 表示二进制千字节
   - ``Mi`` 表示二进制兆字节
   - ``Gi`` 表示二进制吉字节
   - ``Ti`` 表示二进制太字节

   例如，如需将下载速率限制为不超过 1 GiB/s，请使用以下命令：

   .. code-block::

      --limit-download 1G

   如果未指定，MinIO 将使用不受限制的下载速率。

.. mc-cmd:: --limit-upload
   :optional:

   将客户端侧上传速率限制为不超过指定值，单位可以是 KiB/s、MiB/s 或 GiB/s。
   这只影响从运行 MinIO Client 的本地设备发起的上传速度。
   有效单位包括：
   
   - ``B`` 表示字节
   - ``K`` 表示千字节
   - ``M`` 表示兆字节
   - ``G`` 表示吉字节
   - ``T`` 表示太字节
   - ``Ki`` 表示二进制千字节
   - ``Mi`` 表示二进制兆字节
   - ``Gi`` 表示二进制吉字节
   - ``Ti`` 表示二进制太字节

   例如，如需将上传速率限制为不超过 1 GiB/s，请使用以下命令：

   .. code-block::

      --limit-upload 1G

   如果未指定，MinIO 将使用不受限制的上传速率。

.. end-mc-limit-flags-desc
