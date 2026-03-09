.. _developers-object-lambda:

=================================
使用 Object Lambda 进行转换
=================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

MinIO 的 Object Lambda 使开发者能够按需以编程方式转换对象。
你可以根据具体用例按需转换对象，例如脱敏个人身份信息（PII）、使用其他来源的信息增强数据，或在不同格式之间进行转换。

概述
----

:ref:`Object Lambda handler <minio_object_lambda_handers>` 是一个小型代码模块，用于转换对象内容并返回结果。
与 :s3-docs:`Amazon S3 Object Lambda functions <transforming-objects.html>` 类似，你可以从应用程序发起 GET 请求来触发 MinIO Object Lambda handler 函数。
handler 从 MinIO 获取请求的对象，执行转换后将修改后的数据返回给 MinIO，再由 MinIO 发送回原始应用程序。
原始对象保持不变。

每个 handler 都是独立进程，且多个 handler 可以转换同一份数据。
这使你可以将同一个对象用于不同目的，而无需维护原始对象的多个版本。

.. _minio_object_lambda_handers:

Object Lambda Handlers
----------------------

你可以使用任何能够发送和接收 HTTP 请求的语言编写 handler 函数。
该函数必须能够：

- 监听 HTTP POST 请求。
- 使用 URL 获取原始对象。
- 返回转换后的内容和授权令牌。

创建函数
~~~~~~~~~

handler 函数应执行以下步骤：

#. 从传入的 POST 请求中提取对象详细信息。

   JSON 请求负载中的 ``getObjectContext`` 属性包含原始对象的详细信息。
   要构造响应，你需要以下值：

   .. list-table::
      :widths: 25 75
      :header-rows: 1

      * - 值
        - 说明

      * - ``inputS3Url``
        - 原始对象的 `presigned URL <https://minio.pigsty.io/developers/go/API.html#presigned-operations>`__。
	  调用应用程序会生成该 URL，并在原始请求中发送它。
	  这使 handler 无需通常所需的 MinIO 凭证即可访问原始对象。
          该 URL 的有效期为一小时。

      * - ``outputRoute``
        - 允许 MinIO 验证转换后对象目标位置的令牌。
	  在响应中的 ``x-amz-request-route`` header 返回该值。

      * - ``outputToken``
        - 允许 MinIO 验证响应的令牌。
	  在响应中的 ``x-amz-request-token`` header 返回该值。

#. 从 MinIO 获取原始对象。

   使用 presigned URL 从 MinIO 部署中获取对象。
   对象内容位于响应体中。

#. 按需转换对象。

   执行生成转换后对象所需的任意操作。
   由于调用应用程序正在等待响应，你可能希望避免潜在的长时间运行操作。

#. 构造包含以下信息的响应：

   - 转换后对象的内容。
   - 带有 ``outputRoute`` 令牌的 ``x-amz-request-route`` header。
   - 带有 ``outputToken`` 令牌的 ``x-amz-request-token`` header。

#. 将响应返回给 Object Lambda。

   MinIO 会验证响应，并将转换后的数据发送回原始调用应用程序。


.. admonition:: 响应头
   :class: note

   handler **必须** 在对应的响应头中包含 ``outputRoute`` 和 ``outputToken`` 值。
   这使 MinIO 能够正确验证来自 handler 的响应。


注册 Handler
~~~~~~~~~~~~

要使 MinIO 能够调用 handler，请使用以下 :ref:`MinIO server Object Lambda environment variables <minio-server-envvar-object-lambda-webhook>` 将 handler 函数注册为 webhook：

:envvar:`MINIO_LAMBDA_WEBHOOK_ENABLE_functionname <MINIO_LAMBDA_WEBHOOK_ENABLE>`
   为某个 handler 函数启用或禁用 Object Lambda。
   对于多个 handler，请为每个函数名设置该环境变量。

:envvar:`MINIO_LAMBDA_WEBHOOK_ENDPOINT_functionname <MINIO_LAMBDA_WEBHOOK_ENDPOINT>`
   为某个 handler 函数注册 endpoint。
   对于多个 handler，请为每个函数 endpoint 设置该环境变量。

MinIO 还支持以下用于已认证 webhook endpoint 的环境变量：

:envvar:`MINIO_LAMBDA_WEBHOOK_AUTH_TOKEN_functionanme <MINIO_LAMBDA_WEBHOOK_AUTH_TOKEN>`
   指定用于 webhook 认证的 opaque string 或 JWT 授权令牌。

:envvar:`MINIO_LAMBDA_WEBHOOK_CLIENT_CERT_functionname <MINIO_LAMBDA_WEBHOOK_CLIENT_CERT>`
   指定用于 webhook mTLS 认证的客户端证书。

:envvar:`MINIO_LAMBDA_WEBHOOK_CLIENT_KEY_functionname <MINIO_LAMBDA_WEBHOOK_CLIENT_CERT>`
   指定用于 webhook mTLS 认证的私钥。

重启 MinIO 以应用更改。

或者，也可以通过 :ref:`MinIO Client <minio-client>` 命令行工具配置 Object Lambda。
更多信息请参见 :ref:`minio-server-envvar-object-lambda-webhook`。

从应用程序触发
~~~~~~~~~~~~~~~

要从应用程序请求转换后的对象：

#. 连接到 MinIO 部署。

#. 通过添加 ``lambdaArn`` 参数并设置目标 handler 的 ARN，设置 Object Lambda 目标。

#. 为原始对象生成 `presigned URL <https://minio.pigsty.io/developers/go/API.html#presigned-operations>`__。

#. 使用生成的 URL 获取转换后的对象。

   MinIO 将请求发送到目标 Object Lambda handler。
   handler 将转换后的内容返回给 MinIO，MinIO 会验证该响应并将其返回给应用程序。


示例
----

使用 Python、Go 和 ``curl`` 转换对象内容：

- 创建并注册一个 Object Lambda handler。
- 创建一个存储桶和要转换的对象。
- 请求并显示转换后的对象内容。

前提条件：

- 已存在的 :ref:`MinIO <minio-installation>` 部署
- 可用的 Python（3.8+）和 Golang 开发环境
- :doc:`The MinIO Go SDK </developers/go/minio-go>`


创建 Handler
~~~~~~~~~~~~

示例 handler 使用 Python 编写，使用调用方生成的 `presigned URL <https://minio.pigsty.io/developers/go/API.html#presigned-operations>`__ 获取目标对象。
随后，handler 转换对象内容并返回新文本。
它使用 `Flask web framework <https://flask.palletsprojects.com/en/2.2.x/>`__ 和 Python 3.8+。

以下命令安装 Flask 和其他所需依赖：

.. code-block:: shell
   :class: copyable

   pip install flask requests

handler 调用 ``swapcase()`` 来切换原始文本中每个字母的大小写。
随后它将结果发送回 MinIO，再由 MinIO 返回给调用方。

.. code-block:: py
   :class: copyable

   from flask import Flask, request, abort, make_response
   import requests

   app = Flask(__name__)
   @app.route('/', methods=['POST'])
   def get_webhook():
      if request.method == 'POST':
         # Get the request event from the 'POST' call
         event = request.json

	 # Get the object context
         object_context = event["getObjectContext"]

         # Get the presigned URL
	 # Used to fetch the original object from MinIO
         s3_url = object_context["inputS3Url"]

         # Extract the route and request tokens from the input context
         request_route = object_context["outputRoute"]
         request_token = object_context["outputToken"]

         # Get the original S3 object using the presigned URL
         r = requests.get(s3_url)
         original_object = r.content.decode('utf-8')

         # Transform the text in the object by swapping the case of each char
         transformed_object = original_object.swapcase()

         # Return the object back to Object Lambda, with required headers
         # This sends the transformed data to MinIO
	 # and then to the user
         resp = make_response(transformed_object, 200)
         resp.headers['x-amz-request-route'] = request_route
         resp.headers['x-amz-request-token'] = request_token
         return resp

      else:
         abort(400)

   if __name__ == '__main__':
      app.run()


启动 Handler
+++++++++++

使用以下命令在本地开发环境中启动 handler：

.. code-block:: shell
   :class: copyable

   python lambda_handler.py

输出类似如下：

.. code-block:: shell

    * Serving Flask app 'lambda_handler'
    * Debug mode: off
   WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
    * Running on http://127.0.0.1:5000
   Press CTRL+C to quit


启动 MinIO
+++++++++++

handler 运行后，使用 :envvar:`MINIO_LAMBDA_WEBHOOK_ENABLE` 和 :envvar:`MINIO_LAMBDA_WEBHOOK_ENDPOINT` 环境变量启动 MinIO，以将该函数注册到 MinIO。
要标识具体的 Object Lambda handler，请将函数名追加到环境变量名后。

以下命令在本地开发环境中启动 MinIO：

.. code-block:: shell
   :class: copyable

   MINIO_LAMBDA_WEBHOOK_ENABLE_myfunction=on MINIO_LAMBDA_WEBHOOK_ENDPOINT_myfunction=http://localhost:5000 minio server /data

将 ``myfunction`` 替换为你的 handler 函数名，并将 ``/data`` 替换为本地部署中 MinIO 目录的位置。
输出类似如下：

.. code-block:: shell

   MinIO Object Storage Server
   Copyright: 2015-2023 MinIO, Inc.
   License: GNU AGPLv3 <https://www.gnu.org/licenses/agpl-3.0.html>
   Version: RELEASE.2023-03-24T21-41-23Z (go1.19.7 linux/arm64)

   Status:         1 Online, 0 Offline.
   API: http://192.168.64.21:9000  http://127.0.0.1:9000
   RootUser: minioadmin
   RootPass: minioadmin
   Object Lambda ARNs: arn:minio:s3-object-lambda::myfunction:webhook


测试 Handler
~~~~~~~~~~~~

要测试 Lambda handler 函数，请先创建一个待转换对象。
然后调用 handler，本例中使用 Go 函数生成的 presigned URL 配合 ``curl`` 调用。

#. 创建供 handler 转换的存储桶和对象。

   .. code-block:: shell
      :class: copyable

      mc alias set myminio/ http://localhost:9000 minioadmin minioadmin
      mc mb myminio/myfunctionbucket
      cat > testobject << EOF
      Hello, World!
      EOF
      mc cp testobject myminio/myfunctionbucket/

#. 调用 Handler

   以下 Go 代码使用 :doc:`The MinIO Go SDK </developers/go/minio-go>` 生成 presigned URL 并打印到 ``stdout``。

   .. code-block:: go
      :class: copyable

      package main

      import (
         "context"
         "log"
         "net/url"
         "time"
         "fmt"

         "github.com/minio/minio-go/v7"
         "github.com/minio/minio-go/v7/pkg/credentials"
      )

      func main() {

         // Connect to the MinIO deployment
         s3Client, err := minio.New("localhost:9000", &minio.Options{
            Creds:  credentials.NewStaticV4("my_admin_user", "my_admin_password", ""),
            Secure: false,
         })
         if err != nil {
            log.Fatalln(err)
         }

         // Set the Lambda function target using its ARN
         reqParams := make(url.Values)
         reqParams.Set("lambdaArn", "arn:minio:s3-object-lambda::myfunction:webhook")

         // Generate a presigned url to access the original object
         presignedURL, err := s3Client.PresignedGetObject(context.Background(), "myfunctionbucket", "testobject", time.Duration(1000)*time.Second, reqParams)
         if err != nil {
            log.Fatalln(err)
         }

         // Print the URL to stdout
         fmt.Println(presignedURL)
      }

   在上述代码中，替换以下值：

   - 将 ``my_admin_user`` 和 ``my_admin_password`` 替换为 MinIO 部署的用户凭证。
   - 将 ``myfunction`` 替换为在 ``MINIO_LAMBDA_WEBHOOK_ENABLE`` 和 ``MINIO_LAMBDA_WEBHOOK_ENDPOINT`` 环境变量中设置的相同函数名。

   要获取转换后的对象，执行该 Go 代码并使用 ``curl`` 生成 GET 请求：

   .. code-block:: shell
      :class: copyable

      curl -v $(go run presigned.go)

   ``curl`` 会运行 Go 代码，然后通过对 presigned URL 发起 GET 请求来获取对象。
   输出类似如下：

   .. code-block:: shell

      *   Trying 127.0.0.1:9000...
      * Connected to localhost (127.0.0.1) port 9000 (#0)
      > GET /myfunctionbucket/testobject?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=minioadmin%2F20230406%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20230406T184749Z&X-Amz-Expires=1000&X-Amz-SignedHeaders=host&lambdaArn=arn%3Aminio%3As3-object-lambda%3A%3Amyfunction%3Awebhook&X-Amz-Signature=68fe7e03929a7c0da38255121b2ae09c302840c06654d1b79d7907d942f69915 HTTP/1.1
      > Host: localhost:9000
      > User-Agent: curl/7.81.0
      > Accept: */*
      >
      * Mark bundle as not supporting multiuse
      < HTTP/1.1 200 OK
      < Content-Security-Policy: block-all-mixed-content
      < Strict-Transport-Security: max-age=31536000; includeSubDomains
      < Vary: Origin
      < Vary: Accept-Encoding
      < X-Amz-Id-2: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
      < X-Amz-Request-Id: 17536CF16130630E
      < X-Content-Type-Options: nosniff
      < X-Xss-Protection: 1; mode=block
      < Date: Thu, 06 Apr 2023 18:47:49 GMT
      < Content-Length: 14
      < Content-Type: text/plain; charset=utf-8
      <
      hELLO, wORLD!
      * Connection #0 to host localhost left intact
