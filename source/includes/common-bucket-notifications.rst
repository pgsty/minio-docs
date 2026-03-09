.. start-bucket-notification-find-arn

.. admonition:: 识别存储桶通知的 ARN
   :class: note

   此前创建端点时，你已定义 ``<IDENTIFIER>``，用于分配给存储桶通知目标 ARN。
   以下步骤会返回该部署上已配置的 ARN。
   请通过查找你指定的 ``<IDENTIFIER>`` 来识别此前创建的 ARN。
   
   **查看 JSON 输出**
   
   #. 复制并运行以下命令，将 ``ALIAS`` 替换为该部署的 :ref:`别名 <alias>`。

      .. code-block:: shell
         :class: copyable

         mc admin info --json ALIAS

   #. 在 JSON 输出中，查找 ``info.sqsARN`` 键。
   
      你需要的 ARN 就是该键中与所指定 ``<IDENTIFIER>`` 匹配的那个值。
      
      例如，|ARN|。

   **使用 jq 从 JSON 中解析该值**
    
   #. `安装 jq <https://stedolan.github.io/jq/>`_
   #. 复制并运行以下命令，将 ``ALIAS`` 替换为该部署的 :ref:`别名 <alias>`。

      .. code-block:: shell
         :class: copyable

         mc admin info --json ALIAS | jq  .info.sqsARN

      该命令会返回用于通知的 ARN，例如 |ARN|。

.. end-bucket-notification-find-arn
