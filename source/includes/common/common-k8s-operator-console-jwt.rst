使用以下命令获取登录 Operator Console 所需的 JSON Web Token (JWT)：

.. code-block:: shell
   :class: copyable

   kubectl get secret/console-sa-secret -n minio-operator -o json | jq -r '.data.token' | base64 -d

如果本机没有安装 ``jq`` 工具，
你可以只运行该命令中的 ``kubectl`` 部分（即 ``| jq`` 之前的部分），
然后在输出中找到 ``data.token`` 区段。
