版本发布注记
============

这里汇总了 `pgsty/minio <https://github.com/pgsty/minio/releases>`__ 发布页面中已经发布的所有版本，并补充即将发布的 2026-04-17 版本草案。
下文按时间倒序整理每个 Release 的发布时间、核心改动、安全修复与关联提交，便于快速审计社区维护分支的演进。

.. contents:: 目录
   :local:
   :depth: 1

RELEASE.2026-04-17T00-00-00Z
----------------------------

2026-04-17: `https://github.com/pgsty/minio/releases/tag/RELEASE.2026-04-17T00-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2026-04-17T00-00-00Z>`__

本次版本聚焦安全加固与兼容性收敛，集中修复了 OIDC、LDAP STS、S3 Select、对象复制元数据、unsigned-trailer、Snowball 上传链路，以及依赖与 Go 工具链相关的多项安全问题，并同步完成 LDAP TLS 回归修复与社区分叉文档整理。

主要变更
^^^^^^^^

- 身份认证链路收紧：OIDC / WebIdentity 现在只接受来自 IdP ``JWKS`` 的非对称签名 ``ID Token``，``HS256`` 等对称签名 token 不再被接受；LDAP STS 统一隐藏“未知用户”和“密码错误”的区别，降低用户名枚举风险。
- LDAP STS 限流行为更新：限流现在同时按源 IP 与归一化用户名生效，成功请求不再错误消耗额度；默认仅使用 socket peer address 作为源地址，不再信任 ``X-Forwarded-For``、``X-Real-IP``、``Forwarded``，如需按真实客户端 IP 限流，需显式配置 ``MINIO_IDENTITY_LDAP_STS_TRUSTED_PROXIES``。
- 上传与写入路径更严格：presigned query 参数不能再与 ``unsigned-trailer`` 的 ``PUT`` / multipart 上传组合使用；Snowball auto-extract 在 ``unsigned-trailer`` 路径下也会执行完整签名校验，匿名或伪造签名请求将被拒绝。
- 复制元数据不再可伪造：普通 ``PUT`` / ``COPY`` 请求夹带的 ``X-Minio-Replication-*`` 内部复制头现在会被拒绝或忽略，只有可信复制链路才能写入相关内部元数据。
- S3 Select 错误语义更明确：CSV 和 line-delimited JSON 遇到超大记录时会直接返回 ``OverMaxRecordSize``，不再笼统返回 ``InternalError``；依赖旧错误码的客户端或告警规则需要同步调整。
- 运行时与依赖基线升级：修复 ``ldaps://`` 未正确应用 TLS 配置的回归问题，替换 ``minio/pkg/v3`` 为 ``pgsty/minio-pkg/v3``，并锁定若干易引入 breaking changes 的关键依赖；同时升级 ``go-jose``、``go.opentelemetry.io`` 与 Go ``1.26.2``，统一构建与发布基线。
- 文档与安全说明同步更新：刷新 ``SECURITY.md``、``VULNERABILITY_REPORT.md``、``docs/sts/ldap.md`` 等文档，新增安全通告索引，并将安全说明中的上游 ``minio/minio`` 引用统一切换为 ``pgsty/minio``。

修复的 CVE
^^^^^^^^^^

- `CVE-2026-34986 <https://github.com/advisories?query=CVE-2026-34986>`__：升级 ``go-jose`` 到 ``v4.1.4``，修复 JWT / JOSE 依赖中的已知安全问题。
- `CVE-2026-39883 <https://github.com/advisories/GHSA-hfvc-g4fc-pqhx>`__：升级 ``go.opentelemetry.io`` 依赖栈，修复 PATH hijacking 风险。
- `CVE-2026-33322 <https://github.com/advisories?query=CVE-2026-33322>`__：恢复严格的 JWKS-only OIDC JWT 验证路径，阻断 keyring 注入与算法混淆风险。
- `CVE-2026-33419 <https://github.com/advisories?query=CVE-2026-33419>`__：系统性强化 LDAP STS 认证、限流、源地址识别与记账逻辑，涉及 4 个后续修复提交。
- `CVE-2026-34204 <https://github.com/advisories?query=CVE-2026-34204>`__：拒绝不可信请求注入 ``X-Minio-Replication-*`` 元数据，防止对象被写入异常复制状态。
- `CVE-2026-39414 <https://github.com/advisories?query=CVE-2026-39414>`__：提前拒绝超大 S3 Select 记录，避免异常数据持续缓冲和解析。
- `GHSA-hv4r-mvr4-25vw <https://github.com/advisories/GHSA-hv4r-mvr4-25vw>`__：封堵 unsigned-trailer query auth 绕过。
- `GHSA-9c4q-hq6p-c237 <https://github.com/advisories/GHSA-9c4q-hq6p-c237>`__：加固 Snowball auto-extract 场景中的 unsigned-trailer 认证与签名校验。
- `CVE-2026-32280 <https://pkg.go.dev/vuln/GO-2026-4947>`__、`CVE-2026-32281 <https://pkg.go.dev/vuln/GO-2026-4946>`__、`CVE-2026-32283 <https://pkg.go.dev/vuln/GO-2026-4870>`__：升级 Go 到 ``1.26.2``，吸收上游工具链与标准库安全修复。

关联提交
^^^^^^^^

- `c878ca0 <https://github.com/pgsty/minio/commit/c878ca075678e943666ec2365afe95d992a0cc76>`__：fix: pin deps with breaking changes and fix LDAP TLS regression (#15)
- `e970ec5 <https://github.com/pgsty/minio/commit/e970ec5629068a1fc5938dddb4e3f1e70e1e2929>`__：fix: upgrade go-jose to v4.1.4 to patch CVE-2026-34986
- `a206510 <https://github.com/pgsty/minio/commit/a2065105a0e1b67f44b537e9d246ace2acc6aec0>`__：fix: CVE-2026-39883 upgrade go.opentelemetry.io
- `fd65f11 <https://github.com/pgsty/minio/commit/fd65f1199bac0317cc18fc9630dcee3f0c168024>`__：merge: PR #18 upgrade go-jose to v4.1.4 for CVE-2026-34986
- `bc087e4 <https://github.com/pgsty/minio/commit/bc087e40a3a4feaa7d9817cfc621e79bed5ad84a>`__：merge: PR #19 upgrade go.opentelemetry.io for CVE-2026-39883
- `f1f2239 <https://github.com/pgsty/minio/commit/f1f2239ef8f44de48f6730327643e454574cbcdd>`__：fix: CVE-2026-33322 restore JWKS-only OIDC JWT verification
- `6619d0c <https://github.com/pgsty/minio/commit/6619d0cd326b022dd34f7da4d637c5aca37bbd23>`__：fix: CVE-2026-33419 harden LDAP STS auth
- `fcb8f24 <https://github.com/pgsty/minio/commit/fcb8f242dee3394263e5b7a2cf02af87d34701f8>`__：fix: CVE-2026-34204 reject untrusted replication metadata
- `c5765dc <https://github.com/pgsty/minio/commit/c5765dcbbe28b2fb001c30ef44e95ff9ca026ca9>`__：fix: CVE-2026-39414 reject oversized S3 Select records
- `fa7c579 <https://github.com/pgsty/minio/commit/fa7c579bfbe2a98f2cb7d3ff1333d9393978fab1>`__：fix: GHSA-hv4r-mvr4-25vw block unsigned-trailer query auth bypass
- `b50ab58 <https://github.com/pgsty/minio/commit/b50ab5835bc6d04a9f760bbcfcca21d9fdb22fa7>`__：fix: GHSA-9c4q-hq6p-c237 harden Snowball unsigned-trailer auth
- `9a4b3cd <https://github.com/pgsty/minio/commit/9a4b3cd92f8fff72d31c3e41d68d878f39253eaa>`__：fix: CVE-2026-32280/CVE-2026-32281/CVE-2026-32283 upgrade Go to 1.26.2
- `c55b52c <https://github.com/pgsty/minio/commit/c55b52caf183db40adb3fdb56cb8b0f0067a8dd1>`__：fix: CVE-2026-33419 preserve LDAP STS rate limits on success
- `817a457 <https://github.com/pgsty/minio/commit/817a4570165b3073451b4b392a3aafcaafcdcea9>`__：fix: CVE-2026-33419 harden LDAP STS rate-limit source IP
- `084a154 <https://github.com/pgsty/minio/commit/084a15461a03944e50b37987c9446d4c2f87f9b8>`__：fix: CVE-2026-33419 tighten LDAP STS rate-limit accounting
- `16e34f9 <https://github.com/pgsty/minio/commit/16e34f900cf90b541561a450e2588332e17213b9>`__：docs: refresh security guidance and fork references

RELEASE.2026-03-25T00-00-00Z
----------------------------

2026-03-25: `https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-25T00-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-25T00-00-00Z>`__

这是一个以打包、稳定性与安全公告为主的维护版本，重点是完善交付镜像、修复 LDAP TLS 回归，并把已经锁定到安全版本的依赖明确纳入发布说明。

主要变更
^^^^^^^^

- 将 ``mcli/mc`` 打入 Docker 镜像并增加校验流程，改善镜像开箱体验。
- 修复 ``ldaps://`` 场景中的 LDAP TLS 回归，确保 TLS 配置能够正确透传。
- 移除从上游继承但社区分支不再使用的 CI/CD 工作流，减轻维护负担。
- 固定若干关键依赖，避免上游 breaking changes 继续向下游扩散。

修复的 CVE
^^^^^^^^^^

- `CVE-2026-24051 <https://pkg.go.dev/vuln/GO-2026-4394>`__：发布说明明确将 ``go.opentelemetry.io/otel/sdk`` 锁定在安全版本 ``v1.42.0``，规避 macOS 上因 PATH hijacking 导致的任意代码执行风险。
- `CVE-2025-10543 <https://pkg.go.dev/vuln/GO-2025-4173>`__：发布说明明确交付了 ``github.com/eclipse/paho.mqtt.golang v1.5.1``，修复超长 UTF-8 字符串编码错误导致的 MQTT 数据包内容异常。
- `CVE-2025-58181 <https://pkg.go.dev/vuln/GO-2025-4134>`__：发布说明明确交付了 ``golang.org/x/crypto v0.49.0``，修复 ``ssh`` GSSAPI 认证请求可触发的无界内存消耗问题。

关联提交
^^^^^^^^

- `f2f9a40 <https://github.com/pgsty/minio/commit/f2f9a40dce9597d5d1fa758b3316d41a86e73cff>`__：add mcli/mc from pgsty/mc to Docker image
- `ce1c537 <https://github.com/pgsty/minio/commit/ce1c537eb1dd6c4efa1cf75cf5df0e2c489c947a>`__：fix: pin deps with breaking changes and fix LDAP TLS regression (#15)
- `ee55e53 <https://github.com/pgsty/minio/commit/ee55e5391a5d5e4bd46d555a11b2215eb6ed836e>`__：remove upstream CI/CD workflows inherited from minio/minio

RELEASE.2026-03-21T00-00-00Z
----------------------------

2026-03-21: `https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-21T00-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-21T00-00-00Z>`__

这是一次围绕 Go 1.26.1 与依赖收敛展开的维护版。除了适配更严格的编译与 lint 检查外，这个版本也完成了本轮最关键的一次安全依赖刷新。

主要变更
^^^^^^^^

- 将构建环境从 Go ``1.26.0`` 升级到 Go ``1.26.1``。
- 全面刷新直接与间接依赖，收敛新工具链下的兼容性问题。
- 修正 Go 1.26.1 更严格检查暴露出来的一批 lint 与测试问题。

修复的 CVE
^^^^^^^^^^

- `CVE-2026-27137 <https://pkg.go.dev/vuln/GO-2026-4599>`__：Go stdlib 从 ``1.26.0`` 升级到 ``1.26.1``，修复 ``crypto/x509`` 对邮箱约束校验不完整的问题。
- `CVE-2026-27138 <https://pkg.go.dev/vuln/GO-2026-4600>`__：Go stdlib 从 ``1.26.0`` 升级到 ``1.26.1``，修复畸形证书触发 ``crypto/x509`` panic 的问题。
- `CVE-2026-25679 <https://pkg.go.dev/vuln/GO-2026-4601>`__：Go stdlib 从 ``1.26.0`` 升级到 ``1.26.1``，修复 ``net/url`` 对 IPv6 host literal 解析不严格的问题。
- `CVE-2026-27139 <https://pkg.go.dev/vuln/GO-2026-4602>`__：Go stdlib 从 ``1.26.0`` 升级到 ``1.26.1``，修复 ``os`` 中 ``FileInfo`` 可逃逸 ``Root`` 边界的问题。
- `CVE-2026-27142 <https://pkg.go.dev/vuln/GO-2026-4603>`__：Go stdlib 从 ``1.26.0`` 升级到 ``1.26.1``，修复 ``html/template`` 在 ``meta refresh`` 场景下 URL 未正确转义的 XSS 风险。
- `CVE-2026-26958 <https://pkg.go.dev/vuln/GO-2026-4503>`__：``filippo.io/edwards25519`` 从 ``v1.1.0`` 升级到 ``v1.2.0``，修复 ``MultiScalarMult`` 可能产生错误结果或未定义行为的问题。
- `CVE-2025-10543 <https://pkg.go.dev/vuln/GO-2025-4173>`__：``github.com/eclipse/paho.mqtt.golang`` 从 ``v1.5.0`` 升级到 ``v1.5.1``，修复超长 UTF-8 字符串编码错误导致的 MQTT 报文异常。
- `CVE-2026-24051 <https://pkg.go.dev/vuln/GO-2026-4394>`__：``go.opentelemetry.io/otel/sdk`` 从 ``v1.38.0`` 升级到 ``v1.42.0``，修复 macOS 上通过 PATH hijacking 触发任意代码执行的风险。
- `CVE-2026-33186 <https://pkg.go.dev/vuln/GO-2026-4762>`__：``google.golang.org/grpc`` 从 ``v1.77.0`` 升级到 ``v1.79.3``，修复 HTTP/2 ``:path`` 缺少前导斜杠时的授权绕过问题。

关联提交
^^^^^^^^

- `5abd9a8 <https://github.com/pgsty/minio/commit/5abd9a80f66f6fe2812d2eded18961a65712eb9e>`__：bump golang to 1.26.1 and update deps
- `377fc61 <https://github.com/pgsty/minio/commit/377fc616d9c5944d9d0aff081089d2d0c4ebb12c>`__：fix: satisfy stricter Go 1.26.1 linter checks

RELEASE.2026-03-14T12-00-00Z
----------------------------

2026-03-14: `https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-14T12-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-14T12-00-00Z>`__

这个版本完成了向社区维护 Console 分支的切换，并在切换过程中做了一轮较大幅度的依赖更新，为后续 Go 1.26.x 维护版打下基础。

主要变更
^^^^^^^^

- 切换到社区维护的 ``georgmangold/console v1.9.1``，替换不可持续维护的上游 Console 依赖。
- 大幅更新 ``go.mod`` 中的直接与间接依赖，使 Console 与新工具链组合能够稳定构建。
- 修复 ``grid_test.go`` 的 ``go vet`` 格式化指令问题，并调整部分测试以适配 Go 1.26 的 HTTP 行为变化。

修复的 CVE
^^^^^^^^^^

- `CVE-2025-47913 <https://pkg.go.dev/vuln/GO-2025-4116>`__：``golang.org/x/crypto`` 从 ``v0.37.0`` 升级到 ``v0.46.0``，修复 ``ssh/agent`` 在处理异常响应时可能导致客户端 panic 的问题。
- `CVE-2025-58181 <https://pkg.go.dev/vuln/GO-2025-4134>`__：``golang.org/x/crypto`` 从 ``v0.37.0`` 升级到 ``v0.46.0``，修复 ``ssh`` GSSAPI 认证请求可触发无界内存消耗的问题。
- `CVE-2025-47914 <https://pkg.go.dev/vuln/GO-2025-4135>`__：``golang.org/x/crypto`` 从 ``v0.37.0`` 升级到 ``v0.46.0``，修复 ``ssh/agent`` 对畸形身份消息缺乏边界校验导致的 panic 问题。
- `CVE-2025-47911 <https://pkg.go.dev/vuln/GO-2026-4440>`__：``golang.org/x/net`` 从 ``v0.39.0`` 升级到 ``v0.48.0``，修复 ``html.Parse`` 在特制输入下的二次复杂度解析问题。
- `CVE-2025-58190 <https://pkg.go.dev/vuln/GO-2026-4441>`__：``golang.org/x/net`` 从 ``v0.39.0`` 升级到 ``v0.48.0``，修复 ``golang.org/x/net/html`` 可能陷入无限解析循环的问题。

关联提交
^^^^^^^^

- `68521b3 <https://github.com/pgsty/minio/commit/68521b37f2697c79b7fe14676eaf687864a1cf26>`__：add github ci/cd pipeline
- `00f3cf7 <https://github.com/pgsty/minio/commit/00f3cf74fc4319f3d34c4e47c87ad39386318755>`__：RELEASE.2026-03-14T12-00-00Z with go 1.26.0

RELEASE.2026-02-14T12-00-00Z
----------------------------

2026-02-14: `https://github.com/pgsty/minio/releases/tag/RELEASE.2026-02-14T12-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2026-02-14T12-00-00Z>`__

这是社区分支早期的一个基础设施版本，除了恢复内嵌 Console 与建立 GitHub CI/CD 之外，也把 Go 运行时基线提升到 1.26.0，因此一并消化了上一代工具链中的一批安全问题。

主要变更
^^^^^^^^

- 恢复内嵌 Console，并更新 README 以明确社区维护分支的定位。
- 新增 GitHub CI/CD 流水线，为后续自动构建和多平台分发建立基础。
- 补充文档、Docker、GitHub 仓库与 ``pig`` 包管理器安装入口，完善发布入口。

修复的 CVE
^^^^^^^^^^

这些问题随着 Go 从 ``1.25.5`` 升级到 ``1.26.0`` 一并被修复，包括：

- `CVE-2025-68121 <https://pkg.go.dev/vuln/GO-2026-4337>`__：``crypto/tls`` 在会话恢复时可能错误接受已变更 CA 配置。
- `CVE-2025-61730 <https://pkg.go.dev/vuln/GO-2026-4340>`__：TLS 1.3 在跨加密级别 record 中可能错误处理握手消息。
- `CVE-2025-61726 <https://pkg.go.dev/vuln/GO-2026-4341>`__：``net/url`` 查询参数解析可导致内存耗尽。
- `CVE-2025-61728 <https://pkg.go.dev/vuln/GO-2026-4342>`__：``archive/zip`` 构建索引时可能触发过高 CPU 消耗。
- `CVE-2025-68119 <https://pkg.go.dev/vuln/GO-2026-4338>`__：``cmd/go`` 在调用外部 VCS 工具链时可能触发意外代码执行。
- `CVE-2025-61731 <https://pkg.go.dev/vuln/GO-2026-4339>`__：``#cgo pkg-config:`` 指令可导致任意文件写入。
- `CVE-2025-61732 <https://pkg.go.dev/vuln/GO-2026-4433>`__：``cmd/cgo`` 文档注释解析差异可能导致代码走私。

关联提交
^^^^^^^^

- `8630937 <https://github.com/pgsty/minio/commit/8630937e7d1c3426ae28508e06f7091d7bde3a49>`__：Restore embedded console and update README for community fork
- `5d57938 <https://github.com/pgsty/minio/commit/5d57938939008cd5eeafea4532a3995ed1a2f206>`__：add github ci/cd pipeline

RELEASE.2025-12-03T12-00-00Z
----------------------------

2025-12-15: `https://github.com/pgsty/minio/releases/tag/RELEASE.2025-12-03T12-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2025-12-03T12-00-00Z>`__

这是目前可追溯到的首个社区版发布，主要目标是建立社区维护分支的打包与分发基线，而不是在此前社区版本之上做增量修复。

主要变更
^^^^^^^^

- 基于 ``minio/pkger`` 建立社区版打包流程。
- 选择 MinIO 进入 maintenance mode 后的一版上游基线作为社区分支起点。
- 首次产出 ``apk``、``deb``、``rpm`` 等多种平台包，为后续持续发布建立基础。

修复的 CVE
^^^^^^^^^^

- 这是首个社区版发布，GitHub Release 未单独声明相对更早社区版本的安全修复清单；本页不追溯其相对上游维护模式基线的全部历史 CVE 差异。

关联提交
^^^^^^^^

- `d4cd4b4 <https://github.com/pgsty/minio/commit/d4cd4b433797e1cdcd2e46cc72df45b0897bdc0a>`__：RELEASE.2025-12-03T12-00-00Z with go 1.25.5
