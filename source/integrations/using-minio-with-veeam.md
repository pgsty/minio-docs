# 将 MinIO 与 Veeam 搭配使用

在使用 Veeam Backup and Replication 时，可以将 MinIO 这类 S3 兼容对象存储用作备份的容量层。这会将 Veeam 基础设施中的存储解耦，并让你继续掌控自己的数据。借助 MinIO 在部署与管理上的易用性，Veeam 备份管理员可以轻松部署自己的对象存储用于容量分层。

## 前置条件

- 安装以下一个或两个产品：支持 S3 兼容对象存储的 Veeam Backup and Replication（例如 9.5.4），以及 Veeam Backup for Office365（VBO）
- 按照 <https://silo.pigsty.cc/index.html#procedure> 完成 MinIO 对象存储部署
- Veeam 要求到对象存储的连接使用 TLS。可按 <https://silo.pigsty.cc/operations/network-encryption.html> 进行配置
- 必须在 Veeam 之外、且在接入前先创建好 S3 存储桶、Access Key 和 Secret Key。
- 为 Veeam 对接的 MinIO endpoint 配置 MinIO Client：<https://silo.pigsty.cc/reference/minio-mc.html>

## 为 Veeam Backup and Replication 设置 S3 兼容对象存储

### 为 Veeam 备份创建 bucket

为 Veeam Backup 创建 bucket，例如：

```
mc mb myminio/veeambackup
```

> NOTE: 对于启用 Immutability 的 Veeam Backup，请在创建 bucket 时启用 object lock，例如：

```
mc mb -l myminio/veeambackup
```

> Object lock 依赖 MinIO Server 启用 erasure coding。更多信息见 <https://silo.pigsty.cc/operations/concepts/erasure-coding.html>。

### 将 MinIO 添加为 Veeam 的对象存储

按照 Veeam 文档中的步骤将 MinIO 添加为对象存储：<https://helpcenter.veeam.com/docs/backup/vsphere/adding_s3c_object_storage.html?ver=100>

对于启用 Immutability 的 Veeam Backup，选择希望备份保持不可变的天数

![Choose Immutability Days for Object Store](/images/integrations/veeam/object_store_immutable_days.png)

### 创建 Scale-out Backup Repository

- 在 Backup Infrastructure 视图下，点击 Scale-out Repositories，然后在功能区点击 Add Scale-out Repository 按钮。

- 按照屏幕向导完成配置

- 在 Capacity Tier 页面，勾选 Extend scale-out backup repository capacity with object storage 复选框并选择对象存储。如果你希望作业运行后可立即测试备份数据，请在对象存储选择下勾选 "Copy" 并取消勾选 "Move"。

### 创建备份作业

#### 使用 Veeam Backup and Replication 备份虚拟机

- 在导航窗格的 Home > Jobs > Backup 下，点击功能区中的 Backup Job 按钮并选择 Virtual Machine。然后按屏幕向导继续。

- 在 Storage 页面，选择前面已配置的 Scale-out Backup Repository。

- 继续创建备份作业。在 Summary 页面，勾选 Run the Job when I click Finish 复选框并点击 Finish 按钮。备份作业会立即启动。这会为该作业中的 VM 创建一次 Active Full 备份。

- 由于我们在创建 SOBR 时选择了 Copy 模式，备份在性能层创建后会立即复制到容量层。

- 对于启用 Immutability 的 Veeam Backup，你可以选择按还原点数量或按天数将备份设为不可变。

![Choose Immutability Options for Backups](/images/integrations/veeam/backup_job_immutable_days.png)

#### 使用 VBO 备份 Office 365

- 为 VBO 备份创建一个新 bucket

```
mc mb -l myminio/vbo
```

- 在 Backup Infrastructure 下，右键 Object Storage Repositories 并选择 "Add object storage"

![Adding Object Storage to VBO Step 1](/images/integrations/veeam/1_add_object_store.png)

- 按照上文 Veeam Backup and Replication 的向导继续执行，这两个产品的步骤相同

- 在 Backup Infrastructure -> Backup Repositories 下，右键并选择 "Add Backup Repository"

- 按向导操作。在 "Object Storage Backup Repository" 部分，选择你上面创建的 MinIO 对象存储

![Adding Object Storage to VBO Backup Repository](/images/integrations/veeam/6_add_sobr_with_object_store.png)

- 创建备份作业时，选择你上面创建的备份仓库。

## 测试设置

下次备份作业运行时，你可以使用 `mc admin trace myminio` 命令，确认流量正在进入 MinIO 节点。对于 Veeam Backup and Replication，需要先等待备份在性能层完成，然后数据才会迁移到容量层（即 MinIO）。

```
20:09:10.216 [200 OK] s3.GetObject veeam-minio01:9000/vbo/Veeam/Backup365/vbotest/Organizations/6571606ecbc4455dbfe23b83f6f45597/Webs/ca2d0986229b4ec88e3a217ef8f04a1d/Items/efaa67764b304e77badb213d131beab6/f4f0cf600f494c3eb702d8eafe0fabcc.aac07493e6cd4c71845d2495a4e1e19b 139.178.68.158    9.789ms      ↑ 90 B ↓ 8.5 KiB
20:09:10.244 [200 OK] s3.GetObject veeam-minio01:9000/vbo/Veeam/Backup365/vbotest/RepositoryLock/cad99aceb50c49ecb9e07246c3b9fadc_bfd985e5deec4cebaf481847f2c34797 139.178.68.158    16.21ms      ↑ 90 B ↓ 402 B
20:09:10.283 [200 OK] s3.PutObject veeam-minio01:9000/vbo/Veeam/Backup365/vbotest/CommonInfo/WebRestorePoints/18f1aba8f55f4ac6b805c4de653eb781 139.178.68.158    29.787ms     ↑ 1005 B ↓ 296 B
```
