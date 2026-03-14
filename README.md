# MinIO 文档

[![Website: silo.pigsty.io](https://img.shields.io/badge/website-silo.pigsty.io-slategray?style=flat&logo=cilium&logoColor=white)](https://silo.pigsty.io)
[![CN: silo.pigsty.cc](https://img.shields.io/badge/网站-silo.pigsty.cc-slategray?style=flat&logo=cilium&logoColor=white)](https://silo.pigsty.cc)

> [!IMPORTANT]
> **这是由社区维护的 [minio/docs](https://github.com/minio/docs) 分支，由 [PGSTY](https://pigsty.io) 维护。** 本项目与 MinIO, Inc. **无关联**，也未获得 MinIO, Inc. 的认可或赞助。“MinIO”是 MinIO, Inc. 的商标，在此仅用于标识上游项目。
> 
> 与上游相比的改动很少：
>- 恢复了被移除的 [Console](https://silo.pigsty.cc/administration/minio-console.html) 文档
> - 修复了指向已下线 `docs.min.io` 的失效链接
> 
> 托管地址：[`silo.pigsty.cc`](https://silo.pigsty.cc)。按原始 [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/legalcode) 许可分发。



## 构建说明

MinIO 使用 [Sphinx](https://www.sphinx-doc.org/en/master/index.html)，基于 ReSTructured Text (rST) 生成静态 HTML 页面。

### 前置条件

- 任意 GNU/Linux 操作系统，或 macOS 12.3 及以上版本。
- python 3.10.x and python-pip
- python3.10-venv
- sphinx 6.2.1
- nodejs 14.5.0 或更高版本
- npm 16.19.1 或更高版本
- `git` 或兼容 git 的客户端

### 构建

> NOTE: 以下步骤在 macOS 上可用于测试，但生产构建建议使用 GNU/Linux。

1. 在本地克隆 docs 仓库。

```
git clone https://github.com/pgsty/minio-docs && cd minio-docs/
```

2. 创建新的 Python 虚拟环境。

```
python3 -m venv venv && source venv/bin/activate
```

3. 安装全部 python 和 nodejs 依赖

```
pip install -r requirements.txt && npm install && npm run build
```

4. 构建

```
make SYNC_SDK=true mindocs
```

`SYNC_SDK=true` 会从 MinIO 社区的 S3 库拉取与 SDK 相关的依赖。
后续构建时可以省略 `SYNC_SDK`。

5. 在 http://localhost:8000 查看生成的文档。

```
python -m http.server --directory build/YOUR_BRANCH/<PLATFORM>/html
```

# 同步 Operator CRD 文档

如果要专门导入 Operator CRD 文档，你必须安装：

- pandoc (latest stable)
- asciidoc (latest stable)

以及前文列出的所有其他前置条件。

运行

```
make sync-operator-crd
```

该脚本会执行三件事：

- 从 MinIO Operator Github 仓库下载并转换 `tenant-crd.adoc`
- 从 Operator Github 仓库下载 Operator Helm `values.yaml`
- 从 Operator Github 仓库下载 Tenant Helm `values.yaml`

对于 `tenant-crd.adoc`，它会先将 asciidoc 转换为 XML，再转换为 markdown。
最后会执行一些 `sed` 查找/替换，以便整理文件供 Sphinx 导入。

当有新的 Operator 发布需要写入文档，且该版本包含 CRD 变更时，可以运行该脚本。
在最新稳定版本预期没有或不需要文档变更时，这可以让我们更容易按周期同步这些文档，而不是在每次构建时都重新拉取。

# 许可证

本项目采用 [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/legalcode) 许可。关于如何为 MinIO Documentation 项目贡献内容的更多信息，请参见 [CONTRIBUTING.md](https://github.com/minio/docs/tree/master/CONTRIBUTING.md) 指南。

> NOTE: 本项目此前采用 AGPL3.0 许可。你可以在该提交中找到所有 AGPL3.0 许可代码：commit:[73772c7f8485809446cc890188a89ece1afb93f6](https://github.com/minio/docs/tree/73772c7f8485809446cc890188a89ece1afb93f6)
