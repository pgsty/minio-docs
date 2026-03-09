.. start-linux-hardware-checklist

.. list-table::
   :header-rows: 1
   :widths: 5 45 25 25
   :width: 100%

   * - 
     - 说明
     - 最低
     - 推荐

   * - :octicon:`circle`
     - 专用裸机或虚拟主机（“主机”）。

     - 4 台专用主机
     - 8 台以上专用主机

   * - :octicon:`circle`
     - :ref:`为每台主机配置专用本地直连驱动器 <minio-hardware-checklist-storage>`。

     - 每个 MinIO Server 4 块驱动器
     - 每个 MinIO Server 8 块以上驱动器

   * - :octicon:`circle`
     - :ref:`高速网络基础设施 <minio-hardware-checklist-network>`。
     - 25GbE
     - 100GbE

   * - :octicon:`circle`
     - 支持现代 SIMD 指令（AVX-512）的服务器级 CPU，例如 Intel® Xeon® Scalable 或更高规格。
     - 每台主机 8 个 CPU/socket 或 vCPU
     - 每台主机 16 个以上 CPU/socket 或 vCPU

   * - :octicon:`circle`
     - :ref:`可用内存 <minio-hardware-checklist-memory>` 应在每台服务器使用量的基础上保留合理余量，并满足或超过该值。
     - 每台主机 32GB 可用内存
     - 每台主机 128GB 以上可用内存

.. end-linux-hardware-checklist

.. start-k8s-hardware-checklist

.. list-table::
   :header-rows: 1
   :widths: 5 55 20 20
   :width: 100%

   * - 
     - 说明
     - 最低
     - 推荐

   * - :octicon:`circle`
     - 专用于承载 MinIO Tenant 的 Kubernetes worker 节点。

     - 每个 Tenant 4 个 worker
     - 每个 Tenant 8 个以上 worker

   * - :octicon:`circle`
     - :ref:`为 MinIO Tenant 配置专用 Persistent Volume <minio-hardware-checklist-storage>`。

     - 每个 MinIO Server pod 4 个 PV
     - 每个 MinIO Server pod 8 个以上 PV

   * - :octicon:`circle`
     - :ref:`高速网络基础设施 <minio-hardware-checklist-network>`。
     - 25GbE
     - 100GbE
       

   * - :octicon:`circle`
     - 支持现代 SIMD 指令（AVX-512）的服务器级 CPU，例如 Intel® Xeon® Scalable 或更高规格。
     - 每个 MinIO Pod 4 个 vCPU
     - 每个 MinIO Pod 8 个以上 vCPU

   * - :octicon:`circle`
     - :ref:`可用内存 <minio-hardware-checklist-memory>` 应在每台服务器使用量的基础上保留合理余量，并满足或超过该值。
     - 每个 worker 节点 32GB 可用内存
     - 每个 worker 节点 128GB 以上可用内存

.. end-k8s-hardware-checklist
