.. _minio-support-encryption:

============
加密文件
============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

说明
----

你可以对 :mc:`mc support inspect` 命令的输出进行加密，以便在将文件传输到 |SUBNET| 时提升安全性。

加密
----

你可以使用 ``--encrypt`` 标志对输出的 zip 文件进行加密，以提升安全性。
MinIO 提供了一个二进制工具用于解密该文件。

使用加密标志后，输出中会提供一个解密密钥。
输出类似如下：

.. code-block:: shell

   $ mc support inspect --encrypt play/test123/test*/*/part.*
   mc: Encrypted file data successfully downloaded as inspect.ad2b43d8.enc
   mc: Decryption key: ad2b43d847fdb14e54c5836200177f7158b3f745433525f5d23c0e0208e50c9948540b54

   mc: The decryption key will ONLY be shown here. It cannot be recovered.
   mc: The encrypted file can safely be shared without the decryption key.
   mc: Even with the decryption key, data stored with encryption cannot be accessed.

如输出所示，MinIO 只会显示这一次加密密钥，之后将无法再次显示或恢复。

.. _minio-support-decryption:

解密
----

MinIO 提供了解密工具，用于处理由 :mc:`mc support inspect` 生成的文件。

要安装解密工具，请先安装 `Go <https://golang.org/dl/>`_，然后运行

.. code-block:: shell
   :class: copyable

   go install github.com/minio/minio/docs/debugging/inspect@latest

安装 inspect 解密二进制文件后，使用以下命令解密文件：

.. code-block:: shell

   inspect -key=<decryptionKeyFromOutput> <file.enc>

将 ``<decryptionKeyFromOutput>`` 替换为生成诊断文件时提供的解密密钥。
将 ``<file.enc>`` 替换为下载后的文件名，可以包含相对路径或绝对路径。

``-key`` 标志是可选的。如果未提供，程序会通过交互式提示要求输入密钥。
文件名中包含了解密密钥的一部分。
这有助于确认该文件应使用哪个密钥。

解密过程会输出一个未加密的 ``.zip`` 文件。
