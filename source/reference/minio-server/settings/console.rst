.. _minio-server-envvar-console:

===================
MinIO Console 设置
===================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. versionchanged:: RELEASE.2025-05-24T17-08-30Z

   Console 现在仅提供对象浏览能力，类似于通过 :mc:`mc` 工具可用的能力。
   对于用户管理等管理类交互，请使用 :mc:`mc admin` 命令。

   本页中的部分设置可能已不再适用于较新的部署。

本页介绍用于管理 MinIO Console 访问与行为的设置。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

浏览器设置
----------

以下设置用于控制内嵌 MinIO Console 的行为。

MinIO Console
~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_BROWSER

         指定 ``off`` 以禁用内嵌 MinIO Console。

   .. tab-item:: 配置项

      此设置没有对应的配置变量。
      请改用环境变量。

动画
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_BROWSER_LOGIN_ANIMATION

         .. versionadded:: MinIO Server RELEASE.2023-05-04T21-44-30Z

         指定 ``off`` 以禁用 MinIO Console 的动画登录界面。
         默认为 ``on``。
   .. tab-item:: 配置项

      此设置没有对应的配置变量。
      请改用环境变量。

浏览器重定向
~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_BROWSER_REDIRECT

         .. versionadded:: MinIO Server RELEASE.2023-09-16T01-01-47Z

         指定是否将来自 Web 浏览器的请求自动重定向到 Console 地址。
         默认为 ``true``。

   .. tab-item:: 配置项

      此设置没有对应的配置变量。
      请改用环境变量。

浏览器重定向 URL
~~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_BROWSER_REDIRECT_URL

         指定 MinIO Console 监听传入连接的 Fully Qualified Domain Name (FQDN)。

         如果你希望 MinIO Console 仅通过反向代理服务对外提供，必须指定由该服务管理的主机名。

         例如，假设某个反向代理被配置为将 ``https://example.net/minio/`` 路由到 MinIO Console。
         你必须将此环境变量设置为与该主机名一致，这样 Console 才会使用该主机名进行监听并响应请求。

         如果省略此变量，Console 会在运行 MinIO Server 的主机所关联的所有 IP 地址或主机名上监听并响应。

   .. tab-item:: 配置项

      此设置没有对应的配置变量。
      请改用环境变量。

会话时长
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_BROWSER_SESSION_DURATION

         .. versionadded:: MinIO Server RELEASE.2023-08-23T10-07-06Z

         指定使用 MinIO Console 时浏览器会话的持续时间。

         MinIO 支持以下时间单位：

         - ``s`` - 秒，"60s"
         - ``m`` - 分钟，"60m"
         - ``h`` - 小时，"24h"
         - ``d`` - 天，"7d"

         默认为 ``12h``。

   .. tab-item:: 配置项

      此设置没有对应的配置变量。
      请改用环境变量。

日志查询 URL
~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_LOG_QUERY_URL

         指定 PostgreSQL 服务的 URL，MinIO 会将 :ref:`Audit logs <minio-logging-publish-audit-logs>` 写入该服务。
         内嵌 MinIO Console 提供日志搜索工具，可查询 PostgreSQL 服务中收集的日志。

   .. tab-item:: 配置项

      此设置没有对应的配置变量。
      请改用环境变量。

内容安全策略
~~~~~~~~~~~~

*可选*

将 MinIO Console 配置为在 HTTP 响应中生成 `Content-Security-Policy <https://en.wikipedia.org/wiki/Content_Security_Policy>`__ 头。
默认为 ``default-src 'self' 'unsafe-eval' 'unsafe-inline';``

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_BROWSER_CONTENT_SECURITY_POLICY

         .. code-block:: shell
            :class: copyable

            export MINIO_BROWSER_CONTENT_SECURITY_POLICY="default-src 'self' 'unsafe-eval' 'unsafe-inline';"


   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: browser csp_policy
         :delimiter: " "

         .. code-block:: shell
            :class: copyable

            mc admin config set browser \
               csp_policy="default-src 'self' 'unsafe-eval' 'unsafe-inline';" \
               [ARGUMENT=VALUE ...]


严格传输安全
~~~~~~~~~~~~

*可选*

将 MinIO Console 配置为在 HTTP 响应中生成 `Strict-Transport-Security <https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security>`__ 头。

要生成该头，你**必须**使用 :envvar:`MINIO_BROWSER_HSTS_SECONDS` 或 :mc-conf:`~browser.hsts_seconds` 设置持续时间。
其他 HSTS 设置是可选的。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_BROWSER_HSTS_SECONDS

         已配置策略生效的 ``max_age``（秒）。
         默认为 ``0``，即禁用。
         你**必须**配置*非零*时长，才能启用 ``Strict-Transport-Security`` 头。

         .. code-block:: shell
            :class: copyable

            export MINIO_BROWSER_HSTS_SECONDS=31536000

      .. envvar:: MINIO_BROWSER_HSTS_INCLUDE_SUB_DOMAINS

         设置为 ``on`` 可将已配置的 HSTS 策略同时应用到所有 MinIO Console 子域名。
         默认为 ``off``。

         .. code-block:: shell
            :class: copyable

            export MINIO_BROWSER_HSTS_INCLUDE_SUB_DOMAINS="on"

      .. envvar:: MINIO_BROWSER_HSTS_PRELOAD

         设置为 ``on`` 可指示客户端浏览器将 MinIO Console 域名加入其 HSTS 预加载列表。
         默认为 ``off``。

         .. code-block:: shell
            :class: copyable

            export MINIO_BROWSER_HSTS_PRELOAD="on"

   .. tab-item:: 配置项
      :sync: config

      以下配置项需要重启服务后才会生效。
      要重启服务，请使用 :mc-cmd:`mc admin service restart`。

      .. mc-conf:: browser hsts_seconds
         :delimiter: " "

         已配置策略生效的 ``max_age``（秒）。
         默认为 ``0``，即禁用。
         你**必须**配置*非零*时长，才能启用 ``Strict-Transport-Security`` 头。

         .. code-block:: shell
            :class: copyable

            mc admin config set browser \
               hsts_seconds="31536000" \
               [ARGUMENT=VALUE ...]

      .. mc-conf:: browser hsts_include_subdomains
         :delimiter: " "

         设置为 ``on`` 可将已配置的 HSTS 策略同时应用到所有 MinIO Console 子域名。
         默认为 ``off``。

         .. code-block:: shell
            :class: copyable

            mc admin config set browser \
               hsts_include_subdomains="on" \
               hsts_seconds="31536000" \
               [ARGUMENT=VALUE ...]

      .. mc-conf:: browser hsts_preload
         :delimiter: " "

         设置为 ``on`` 可指示客户端浏览器将 MinIO Console 域名加入其 HSTS 预加载列表。
         默认为 ``off``。

         .. code-block:: shell
            :class: copyable

            mc admin config set browser \
               hsts_preload="on" \
               hsts_seconds="31536000" \
               [ARGUMENT=VALUE ...]


示例
++++

以下示例展示了给定配置项对应的渲染后响应头。
等价的环境变量会生成相同结果。
所有示例均使用 ``31536000``，即一个自然年（365 天）的秒数。

``hsts_seconds``

  .. code-block:: shell
     :class: copyable

     mc admin config set ALIAS browser hsts_seconds=31536000

  .. code-block:: shell
     :class: copyable

     Strict-Transport-Security: max-age=31536000

``hsts_include_subdomains``

  .. code-block:: shell
     :class: copyable

     mc admin config set ALIAS browser hsts_seconds=31536000 hsts_include_subdomains=on

  .. code-block:: shell
     :class: copyable

     Strict-Transport-Security: max-age=31536000; includeSubDomains

``hsts_preload``

  .. code-block:: shell
     :class: copyable

     mc admin config set ALIAS browser hsts_seconds=31536000 hsts_include_subdomains=on hsts_preload=on

  .. code-block:: shell
     :class: copyable

     Strict-Transport-Security: max-age=31536000; includeSubDomains; preload


来源策略
~~~~~~~~

*可选*

将 MinIO Console 配置为在 HTTP 响应中生成 `Referrer-Policy <https://www.w3.org/TR/referrer-policy/>`__ 头。
默认为 ``strict-origin-when-cross-origin``。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_BROWSER_REFERRER_POLICY

         .. code-block:: shell
            :class: copyable

            export MINIO_BROWSER_REFERRER_POLICY="strict-origin-when-cross-origin"

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: browser referrer_policy
         :delimiter: " "

         .. code-block:: shell

            mc admin config set browser \
               referrer_policy="strict-origin-when-cross-origin" \
               [ARGUMENT=VALUE ...]


Prometheus 设置
-------------------

以下设置用于管理 MinIO 与你的 Prometheus 服务之间的交互方式。

Prometheus URL
~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_PROMETHEUS_URL

         指定已配置为 :ref:`抓取 MinIO 指标 <minio-metrics-collect-using-prometheus>` 的 Prometheus 服务 URL。

         MinIO Console 使用 ``minio-job`` Prometheus 抓取作业，将集群指标填充到 :guilabel:`Dashboard`。

         如果你使用独立的 MinIO Console 进程，则该变量对应 ``CONSOLE_PROMETHEUS_URL``。

   .. tab-item:: 配置项

      此设置没有对应的配置变量。
      请改用环境变量。

Prometheus Job ID
~~~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_PROMETHEUS_JOB_ID

         指定用于 :ref:`抓取 MinIO 指标 <minio-metrics-collect-using-prometheus>` 的自定义 Prometheus job ID。

         MinIO 默认为 ``minio-job``。

         如果你使用独立的 MinIO Console 进程，则该变量对应 ``CONSOLE_PROMETHEUS_JOB_ID``。

   .. tab-item:: 配置项

      此设置没有对应的配置变量。
      请改用环境变量。

Prometheus Auth Token
~~~~~~~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_PROMETHEUS_AUTH_TOKEN

         指定 Console 连接 Prometheus 服务时应使用的 :prometheus-docs:`basic auth token <guides/basic-auth/>`。

         例如，你使用的 basic auth token 可能如下所示：

         .. code-block:: text

            eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJwcm9tZXRoZXVzIiwic3ViIjoibWluaW8iLCJleHAiOjQ4NTAwMzg0MDJ9.GZCKR3d0FH2TCvNHSd39HaVfSuQVVV0s8glICBDmhT51V6CQ_hw8gTYlKHJmcpR8aHkqiJwCqcYJhaMmqwe00XY

         如果你使用独立的 MinIO Console 进程，则该变量对应 ``CONSOLE_PROMETHEUS_AUTH_TOKEN``。

   .. tab-item:: 配置项

      此设置没有对应的配置变量。
      请改用环境变量。
