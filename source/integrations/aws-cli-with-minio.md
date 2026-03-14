# AWS CLI 与 MinIO Server [![Slack](https://slack.min.io/slack?type=svg)](https://slack.min.io)

AWS CLI 是一个用于管理 AWS 服务的统一工具。它也常用于在 AWS S3 中导入和导出数据。它可用于任何兼容 S3 的云存储服务。

在本教程中，我们将学习如何配置并使用 AWS CLI 通过 MinIO Server 管理数据。

## 1. 前提条件

从[这里](https://silo.pigsty.cc/operations/deployments/installation.html)安装 MinIO Server。

## 2. 安装

从 <https://aws.amazon.com/cli/> 安装 AWS CLI。

## 3. 配置

要配置 AWS CLI，请执行 `aws configure` 并填写 MinIO 密钥信息。

本示例中展示的访问凭据属于 <https://play.min.io:9000>。
这些凭据是公开的。你可以将该服务用于测试和开发。在生产部署中请替换为你自己的 MinIO 密钥。

```sh
aws configure
AWS Access Key ID [None]: Q3AM3UQ867SPQQA43P2F
AWS Secret Access Key [None]: zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG
Default region name [None]: us-east-1
Default output format [None]: ENTER
```

此外，还需要为 MinIO Server 启用 AWS Signature Version '4'。

```sh
aws configure set default.s3.signature_version s3v4
```

## 4. 命令

### 列出你的存储桶

```sh
aws --endpoint-url https://play.min.io:9000 s3 ls
2016-03-27 02:06:30 deebucket
2016-03-28 21:53:49 guestbucket
2016-03-29 13:34:34 mbtest
2016-03-26 22:01:36 mybucket
2016-03-26 15:37:02 testbucket
```

### 列出存储桶中的内容

```sh
aws --endpoint-url https://play.min.io:9000 s3 ls s3://mybucket
2016-03-30 00:26:53      69297 argparse-1.2.1.tar.gz
2016-03-30 00:35:37      67250 simplejson-3.3.0.tar.gz
```

### 创建存储桶

```sh
aws --endpoint-url https://play.min.io:9000 s3 mb s3://mybucket
make_bucket: s3://mybucket/
```

### 向存储桶添加对象

```sh
aws --endpoint-url https://play.min.io:9000 s3 cp simplejson-3.3.0.tar.gz s3://mybucket
upload: ./simplejson-3.3.0.tar.gz to s3://mybucket/simplejson-3.3.0.tar.gz
```

### 从存储桶删除对象

```sh
aws --endpoint-url https://play.min.io:9000 s3 rm s3://mybucket/argparse-1.2.1.tar.gz
delete: s3://mybucket/argparse-1.2.1.tar.gz
```

### 删除存储桶

```sh
aws --endpoint-url https://play.min.io:9000 s3 rb s3://mybucket
remove_bucket: s3://mybucket/
```
