# 发布新平台

## 概述

文档 URL 采用路径 `docs.min.io/community|enterprise/$program/`
注意：我们不再使用 `$platform`。

其中：
- `$program` 为 `minio`、`kes` 或类似项
- `$platform` 为 `kubernetes/upstream`、`kubernetes/openshift`、`macos`、`container`、`windows`、`linux` 或类似项
- 并非每个 `$program` 都有按 `$platform` 区分的文档版本

## 步骤

为新的 `$program` 或 `$platform` 发布文档时，必须完成以下步骤：

1. 更新本仓库中的 `makefile`，纳入新文档
   
   - 使用现有的 `$platform` 或 `$program` 段落作为模板。
   - 这会为新的 `$platform` 或 `$program` 文档创建专用构建命令。

2. 在本仓库的 `source/default-conf.py` 文件中，为新的 `$program` 或 `$platform` 添加一个 `elif` 段落
   
   - 使用现有 `elif` 段落作为参考模板。
   - 这样可以在构建中排除不必要的文件。
   - 使用 `url-excludes.yaml` 文件指定要从该平台构建中排除的 URL

3. 更新本仓库中的 `build-docs.sh`，以便每次合并到 `main` 分支时自动为 Web 服务器构建文档

   - 在第二行 `make` 中添加该平台或程序。
   - 使用现有段落作为模板，创建清理并添加新文档路径的命令。
   - Kubernetes 平台应放在 `/docs/kubernetes/$platform` 下   
   - 你可以通过本地运行进行测试（可能需要 `sudo sh ./build-docs.sh`）。
     然后可以使用 `python -m http.server --directory /var/www/docs/minio/` 并测试 URL

4. 更新文档主导航栏（`/source/_templates/content-navigation.html`），纳入 `$program` 和/或 `$platform`

   视需要与网站设计团队协作。

5. 更新 `sitemap_index.xml` 文件

   - 联系网站设计团队成员，在 min.io 根站点服务器上的 sitemap_index.xml 中添加新的 `$program` 或 `$platform` sitemap.xml 路径。

6. 更新 Algolia crawler
   
   - 在 https://crawler.algolia.com 的 **minio** Algolia crawler 配置中，添加新的 `$program` 或 `$platform` sitemap.xml 路径。
   - 从列表中选择 **minio** 索引，然后在左侧导航中选择 Editor 选项卡
   - 将路径添加到 `discoveryPatterns` 段落
   - 将路径添加到 `pathsToMatch` 段落
   - 点击 **Save**
  
7. 重新索引文档站点
   
   文档发布后，在 https://crawler.algolia.com 中选择 **Restart crawling**，手动启动 **minio** 索引 crawler。
