=======================
``mc support callhome``
=======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. mc:: mc support logs disable
.. mc:: mc support logs enable
.. mc:: mc support logs status
   
.. mc:: mc support callhome

描述
----

.. start-mc-support-callhome-desc

:mc-cmd:`mc support callhome` 命令用于启用或禁用将部署诊断信息发送到 |SUBNET|。

.. end-mc-support-callhome-desc

所有 ``mc support`` 命令都需要有效的 SUBNET 订阅。

启用后，MinIO 会将诊断信息发送到 SUBNET。

无论注册状态如何，MinIO 默认禁用此功能。
你必须显式启用 ``callhome`` 功能，信息上传才会开始。

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

语法
----

.. mc-cmd:: enable
   :fullpath:

   开始将部署的诊断信息、日志或两者同时发送到 SUBNET。

   .. code-block:: shell
               
      mc support callhome enable    \
                          ALIAS     \
                          [--logs]  \
                          [--diag]

   .. note::

      SUBNET 不再支持 ``--logs`` 和 ``--diag`` 标志，并将在未来版本中移除。

.. mc-cmd:: disable
   :fullpath:

   停止将部署的诊断信息、日志或两者同时发送到 SUBNET。

   .. code-block:: shell
               
      mc support callhome disable  \
                          ALIAS    \
                          [--logs] \
                          [--diag]

   .. note::

      SUBNET 不再支持 ``--logs`` 和 ``--diag`` 标志，并将在未来版本中移除。

.. mc-cmd:: status
   :fullpath:

   输出某个部署当前是否将诊断信息、日志或两者同时发送到 SUBNET。

   .. code-block:: shell
               
      mc support callhome status   \
                          ALIAS    \
                          [--diag]

   .. note::

      SUBNET 不再支持 ``--diag`` 标志，并将在未来版本中移除。
            
参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`。

.. mc-cmd:: --diag
   :optional:

   .. note::

      SUBNET 不再支持此选项，并将在未来版本中移除。

   每 24 小时向 SUBNET 发送部署诊断信息，或停止发送。

示例
----

启用 ``callhome`` 上报
~~~~~~~~~~~~~~~~~~~~~~

为已注册到 SUBNET 且 :ref:`alias <alias>` 为 ``minio1`` 的部署启用向 SUBNET 发送诊断信息。

.. code-block:: shell
   :class: copyable
 
   mc support callhome enable minio1

禁用 ``callhome`` 上报
~~~~~~~~~~~~~~~~~~~~~~

为已注册到 SUBNET 且 :ref:`alias <alias>` 为 ``minio1`` 的部署禁用向 SUBNET 发送诊断信息。

.. code-block:: shell
   :class: copyable

   mc support callhome disable minio1


显示当前 ``callhome`` 设置
~~~~~~~~~~~~~~~~~~~~~~~~~~

显示别名为 ``minio1`` 的部署是否向 SUBNET 发送信息。

.. code-block:: shell
   :class: copyable
 
   mc support callhome status minio1

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
