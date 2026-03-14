================
部署检查清单
================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

以下检查清单为验证 MinIO 部署是否具备生产就绪条件提供高层指导。

这些检查清单未必完全符合你特定部署拓扑或架构的精确要求，其定位是作为构建可靠生产部署的尽力而为指南。

|subnet| 用户可以 `登录 <https://subnet.min.io/?ref=docs>`__ 并新建 issue，请求生产前部署评审。
通过 SUBNET 与 MinIO Engineering 协作，可确保为高性能且可靠的部署提供端到端支持。

社区用户可以在 `MinIO Community Slack <https://slack.min.io>`__ 寻求支持。
社区支持仅为 best-effort，不提供响应时间相关 SLA。

检查清单：

- :ref:`硬件检查清单 <minio-hardware-checklist>`
- :ref:`安全检查清单 <minio-security-checklist>`
- :ref:`软件检查清单 <minio-software-checklists>`

.. toctree::
   :titlesonly:
   :hidden:
   :glob:

   /operations/checklists/*
