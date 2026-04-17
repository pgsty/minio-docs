Release Note
============

This page collects all published versions from the `pgsty/minio releases page <https://github.com/pgsty/minio/releases>`__ and also includes the draft notes for the upcoming 2026-04-17 release.
The entries below are ordered from newest to oldest and summarize each release's publication date, major changes, security fixes, and related commits.

.. contents:: Contents
   :local:
   :depth: 1

RELEASE.2026-04-17T00-00-00Z
----------------------------

2026-04-17: `https://github.com/pgsty/minio/releases/tag/RELEASE.2026-04-17T00-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2026-04-17T00-00-00Z>`__

This release focuses on security hardening and compatibility tightening. It bundles fixes across OIDC, LDAP STS, S3 Select, replication metadata handling, unsigned-trailer flows, the Snowball upload path, and multiple dependency- and Go toolchain-related security issues, while also incorporating the LDAP TLS regression fix and a cleanup of community-fork documentation.

Major Changes
^^^^^^^^^^^^^

- Tighten the identity-authentication flow: OIDC / WebIdentity now accepts only asymmetrically signed ``ID Token`` values backed by the IdP ``JWKS``; symmetrically signed tokens such as ``HS256`` are no longer accepted. LDAP STS also now hides the distinction between unknown-user and bad-password failures to reduce username-enumeration risk.
- Update LDAP STS rate limiting: limits now apply to both source IP and normalized username, and successful requests no longer consume quota incorrectly. By default MinIO now uses only the socket peer address as the source and no longer trusts ``X-Forwarded-For``, ``X-Real-IP``, or ``Forwarded``; to rate-limit by real client IP, configure ``MINIO_IDENTITY_LDAP_STS_TRUSTED_PROXIES`` explicitly.
- Make upload and write paths stricter: presigned query parameters can no longer be combined with ``unsigned-trailer`` ``PUT`` or multipart uploads. Snowball auto-extract now also performs full signature validation on the ``unsigned-trailer`` path and rejects anonymous or forged-signature requests.
- Prevent replication metadata spoofing: internal ``X-Minio-Replication-*`` headers attached to ordinary ``PUT`` / ``COPY`` requests are now rejected or ignored, and only trusted replication flows may write the related internal metadata.
- Clarify S3 Select error semantics: oversized CSV and line-delimited JSON records now return ``OverMaxRecordSize`` directly instead of the generic ``InternalError``; clients or alerting rules that depend on the old error code should be adjusted.
- Upgrade the runtime and dependency baseline: fix the regression where ``ldaps://`` did not correctly apply TLS settings, replace ``minio/pkg/v3`` with ``pgsty/minio-pkg/v3``, and pin several critical dependencies that are prone to breaking changes. The release also upgrades ``go-jose``, ``go.opentelemetry.io``, and Go ``1.26.2`` to unify the build and release baseline.
- Refresh documentation and security guidance: update ``SECURITY.md``, ``VULNERABILITY_REPORT.md``, ``docs/sts/ldap.md``, and related documents, add a security advisory index, and switch upstream ``minio/minio`` references in the security guidance over to ``pgsty/minio``.

Fixed CVEs
^^^^^^^^^^

- `CVE-2026-34986 <https://github.com/advisories?query=CVE-2026-34986>`__: upgrade ``go-jose`` to ``v4.1.4`` and fix known security issues in the JWT / JOSE dependency chain.
- `CVE-2026-39883 <https://github.com/advisories/GHSA-hfvc-g4fc-pqhx>`__: upgrade the ``go.opentelemetry.io`` dependency stack to fix the PATH-hijacking risk.
- `CVE-2026-33322 <https://github.com/advisories?query=CVE-2026-33322>`__: restore the strict JWKS-only OIDC JWT verification path to block keyring injection and algorithm-confusion risk.
- `CVE-2026-33419 <https://github.com/advisories?query=CVE-2026-33419>`__: systematically harden LDAP STS authentication, rate limiting, source-address identification, and accounting logic across four follow-up fixes.
- `CVE-2026-34204 <https://github.com/advisories?query=CVE-2026-34204>`__: reject injection of ``X-Minio-Replication-*`` metadata by untrusted requests to prevent objects from being written with invalid replication state.
- `CVE-2026-39414 <https://github.com/advisories?query=CVE-2026-39414>`__: reject oversized S3 Select records early to avoid continued buffering and parsing of abnormal inputs.
- `GHSA-hv4r-mvr4-25vw <https://github.com/advisories/GHSA-hv4r-mvr4-25vw>`__: close the unsigned-trailer query-auth bypass.
- `GHSA-9c4q-hq6p-c237 <https://github.com/advisories/GHSA-9c4q-hq6p-c237>`__: harden unsigned-trailer authentication and signature validation in Snowball auto-extract scenarios.
- `CVE-2026-32280 <https://pkg.go.dev/vuln/GO-2026-4947>`__, `CVE-2026-32281 <https://pkg.go.dev/vuln/GO-2026-4946>`__, and `CVE-2026-32283 <https://pkg.go.dev/vuln/GO-2026-4870>`__: upgrade Go to ``1.26.2`` and absorb the upstream toolchain and stdlib security fixes.

Related Commits
^^^^^^^^^^^^^^^

- `c878ca0 <https://github.com/pgsty/minio/commit/c878ca075678e943666ec2365afe95d992a0cc76>`__: fix: pin deps with breaking changes and fix LDAP TLS regression (#15)
- `e970ec5 <https://github.com/pgsty/minio/commit/e970ec5629068a1fc5938dddb4e3f1e70e1e2929>`__: fix: upgrade go-jose to v4.1.4 to patch CVE-2026-34986
- `a206510 <https://github.com/pgsty/minio/commit/a2065105a0e1b67f44b537e9d246ace2acc6aec0>`__: fix: CVE-2026-39883 upgrade go.opentelemetry.io
- `fd65f11 <https://github.com/pgsty/minio/commit/fd65f1199bac0317cc18fc9630dcee3f0c168024>`__: merge: PR #18 upgrade go-jose to v4.1.4 for CVE-2026-34986
- `bc087e4 <https://github.com/pgsty/minio/commit/bc087e40a3a4feaa7d9817cfc621e79bed5ad84a>`__: merge: PR #19 upgrade go.opentelemetry.io for CVE-2026-39883
- `f1f2239 <https://github.com/pgsty/minio/commit/f1f2239ef8f44de48f6730327643e454574cbcdd>`__: fix: CVE-2026-33322 restore JWKS-only OIDC JWT verification
- `6619d0c <https://github.com/pgsty/minio/commit/6619d0cd326b022dd34f7da4d637c5aca37bbd23>`__: fix: CVE-2026-33419 harden LDAP STS auth
- `fcb8f24 <https://github.com/pgsty/minio/commit/fcb8f242dee3394263e5b7a2cf02af87d34701f8>`__: fix: CVE-2026-34204 reject untrusted replication metadata
- `c5765dc <https://github.com/pgsty/minio/commit/c5765dcbbe28b2fb001c30ef44e95ff9ca026ca9>`__: fix: CVE-2026-39414 reject oversized S3 Select records
- `fa7c579 <https://github.com/pgsty/minio/commit/fa7c579bfbe2a98f2cb7d3ff1333d9393978fab1>`__: fix: GHSA-hv4r-mvr4-25vw block unsigned-trailer query auth bypass
- `b50ab58 <https://github.com/pgsty/minio/commit/b50ab5835bc6d04a9f760bbcfcca21d9fdb22fa7>`__: fix: GHSA-9c4q-hq6p-c237 harden Snowball unsigned-trailer auth
- `9a4b3cd <https://github.com/pgsty/minio/commit/9a4b3cd92f8fff72d31c3e41d68d878f39253eaa>`__: fix: CVE-2026-32280/CVE-2026-32281/CVE-2026-32283 upgrade Go to 1.26.2
- `c55b52c <https://github.com/pgsty/minio/commit/c55b52caf183db40adb3fdb56cb8b0f0067a8dd1>`__: fix: CVE-2026-33419 preserve LDAP STS rate limits on success
- `817a457 <https://github.com/pgsty/minio/commit/817a4570165b3073451b4b392a3aafcaafcdcea9>`__: fix: CVE-2026-33419 harden LDAP STS rate-limit source IP
- `084a154 <https://github.com/pgsty/minio/commit/084a15461a03944e50b37987c9446d4c2f87f9b8>`__: fix: CVE-2026-33419 tighten LDAP STS rate-limit accounting
- `16e34f9 <https://github.com/pgsty/minio/commit/16e34f900cf90b541561a450e2588332e17213b9>`__: docs: refresh security guidance and fork references

RELEASE.2026-03-25T00-00-00Z
----------------------------

2026-03-25: `https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-25T00-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-25T00-00-00Z>`__

This is a maintenance release centered on packaging, stability, and security disclosure. It improves the shipping artifacts, fixes an LDAP TLS regression, and explicitly documents the secure dependency set carried by the release.

Major Changes
^^^^^^^^^^^^^

- Bundle ``mcli/mc`` into the Docker image and add checksum verification for a better out-of-the-box image experience.
- Fix the LDAP TLS regression affecting ``ldaps://`` deployments so TLS settings are correctly honored.
- Remove inherited upstream CI/CD workflows that are no longer used in the community-maintained fork.
- Pin several critical dependencies to avoid further fallout from upstream breaking changes.

Fixed CVEs
^^^^^^^^^^

- `CVE-2026-24051 <https://pkg.go.dev/vuln/GO-2026-4394>`__: the release notes explicitly call out ``go.opentelemetry.io/otel/sdk v1.42.0``, which avoids the macOS PATH-hijacking arbitrary code execution issue.
- `CVE-2025-10543 <https://pkg.go.dev/vuln/GO-2025-4173>`__: the release notes explicitly ship ``github.com/eclipse/paho.mqtt.golang v1.5.1``, fixing incorrect MQTT packet encoding for oversized UTF-8 strings.
- `CVE-2025-58181 <https://pkg.go.dev/vuln/GO-2025-4134>`__: the release notes explicitly ship ``golang.org/x/crypto v0.49.0``, fixing unbounded memory consumption in ``ssh`` GSSAPI authentication handling.

Related Commits
^^^^^^^^^^^^^^^

- `f2f9a40 <https://github.com/pgsty/minio/commit/f2f9a40dce9597d5d1fa758b3316d41a86e73cff>`__: add mcli/mc from pgsty/mc to Docker image
- `ce1c537 <https://github.com/pgsty/minio/commit/ce1c537eb1dd6c4efa1cf75cf5df0e2c489c947a>`__: fix: pin deps with breaking changes and fix LDAP TLS regression (#15)
- `ee55e53 <https://github.com/pgsty/minio/commit/ee55e5391a5d5e4bd46d555a11b2215eb6ed836e>`__: remove upstream CI/CD workflows inherited from minio/minio

RELEASE.2026-03-21T00-00-00Z
----------------------------

2026-03-21: `https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-21T00-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-21T00-00-00Z>`__

This maintenance release is built around the Go 1.26.1 upgrade and a broad dependency refresh. Beyond stricter compiler and linter compatibility fixes, it also delivers the most substantial security dependency refresh in the current release line.

Major Changes
^^^^^^^^^^^^^

- Upgrade the build environment from Go ``1.26.0`` to Go ``1.26.1``.
- Refresh direct and indirect dependencies to converge on the newer toolchain.
- Fix linter and test issues exposed by the stricter Go 1.26.1 checks.

Fixed CVEs
^^^^^^^^^^

- `CVE-2026-27137 <https://pkg.go.dev/vuln/GO-2026-4599>`__: Go stdlib ``1.26.0`` -> ``1.26.1`` fixes incomplete email-constraint enforcement in ``crypto/x509``.
- `CVE-2026-27138 <https://pkg.go.dev/vuln/GO-2026-4600>`__: Go stdlib ``1.26.0`` -> ``1.26.1`` fixes a ``crypto/x509`` panic triggered by malformed certificates.
- `CVE-2026-25679 <https://pkg.go.dev/vuln/GO-2026-4601>`__: Go stdlib ``1.26.0`` -> ``1.26.1`` fixes insufficient validation of IPv6 host literals in ``net/url``.
- `CVE-2026-27139 <https://pkg.go.dev/vuln/GO-2026-4602>`__: Go stdlib ``1.26.0`` -> ``1.26.1`` fixes ``FileInfo`` metadata escaping the ``Root`` boundary in ``os``.
- `CVE-2026-27142 <https://pkg.go.dev/vuln/GO-2026-4603>`__: Go stdlib ``1.26.0`` -> ``1.26.1`` fixes missing URL escaping in ``html/template`` for ``meta refresh`` content.
- `CVE-2026-26958 <https://pkg.go.dev/vuln/GO-2026-4503>`__: ``filippo.io/edwards25519`` ``v1.1.0`` -> ``v1.2.0`` fixes incorrect or undefined ``MultiScalarMult`` behavior.
- `CVE-2025-10543 <https://pkg.go.dev/vuln/GO-2025-4173>`__: ``github.com/eclipse/paho.mqtt.golang`` ``v1.5.0`` -> ``v1.5.1`` fixes incorrect MQTT packet encoding for oversized UTF-8 strings.
- `CVE-2026-24051 <https://pkg.go.dev/vuln/GO-2026-4394>`__: ``go.opentelemetry.io/otel/sdk`` ``v1.38.0`` -> ``v1.42.0`` fixes the macOS PATH-hijacking arbitrary code execution issue.
- `CVE-2026-33186 <https://pkg.go.dev/vuln/GO-2026-4762>`__: ``google.golang.org/grpc`` ``v1.77.0`` -> ``v1.79.3`` fixes authorization bypass caused by a missing leading slash in the HTTP/2 ``:path`` pseudo-header.

Related Commits
^^^^^^^^^^^^^^^

- `5abd9a8 <https://github.com/pgsty/minio/commit/5abd9a80f66f6fe2812d2eded18961a65712eb9e>`__: bump golang to 1.26.1 and update deps
- `377fc61 <https://github.com/pgsty/minio/commit/377fc616d9c5944d9d0aff081089d2d0c4ebb12c>`__: fix: satisfy stricter Go 1.26.1 linter checks

RELEASE.2026-03-14T12-00-00Z
----------------------------

2026-03-14: `https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-14T12-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2026-03-14T12-00-00Z>`__

This release switches the project to the community-maintained Console fork and performs a sizeable dependency refresh to establish the base for the later Go 1.26.x maintenance releases.

Major Changes
^^^^^^^^^^^^^

- Switch to the community-maintained ``georgmangold/console v1.9.1`` fork in place of the unmaintainable upstream Console dependency.
- Refresh a large portion of the direct and indirect dependency graph so the new Console and toolchain combination builds cleanly.
- Fix the ``go vet`` format directive issue in ``grid_test.go`` and adjust tests for the HTTP behavior changes in Go 1.26.

Fixed CVEs
^^^^^^^^^^

- `CVE-2025-47913 <https://pkg.go.dev/vuln/GO-2025-4116>`__: ``golang.org/x/crypto`` ``v0.37.0`` -> ``v0.46.0`` fixes a panic in ``ssh/agent`` when handling malformed responses.
- `CVE-2025-58181 <https://pkg.go.dev/vuln/GO-2025-4134>`__: ``golang.org/x/crypto`` ``v0.37.0`` -> ``v0.46.0`` fixes unbounded memory consumption in ``ssh`` GSSAPI authentication parsing.
- `CVE-2025-47914 <https://pkg.go.dev/vuln/GO-2025-4135>`__: ``golang.org/x/crypto`` ``v0.37.0`` -> ``v0.46.0`` fixes a panic in ``ssh/agent`` caused by malformed identity messages.
- `CVE-2025-47911 <https://pkg.go.dev/vuln/GO-2026-4440>`__: ``golang.org/x/net`` ``v0.39.0`` -> ``v0.48.0`` fixes quadratic parsing complexity in ``html.Parse`` for crafted inputs.
- `CVE-2025-58190 <https://pkg.go.dev/vuln/GO-2026-4441>`__: ``golang.org/x/net`` ``v0.39.0`` -> ``v0.48.0`` fixes an infinite parsing loop in ``golang.org/x/net/html``.

Related Commits
^^^^^^^^^^^^^^^

- `68521b3 <https://github.com/pgsty/minio/commit/68521b37f2697c79b7fe14676eaf687864a1cf26>`__: add github ci/cd pipeline
- `00f3cf7 <https://github.com/pgsty/minio/commit/00f3cf74fc4319f3d34c4e47c87ad39386318755>`__: RELEASE.2026-03-14T12-00-00Z with go 1.26.0

RELEASE.2026-02-14T12-00-00Z
----------------------------

2026-02-14: `https://github.com/pgsty/minio/releases/tag/RELEASE.2026-02-14T12-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2026-02-14T12-00-00Z>`__

This early infrastructure-focused community release restores the embedded Console, introduces GitHub CI/CD, and lifts the Go baseline to 1.26.0, which also absorbs a batch of security fixes from the older toolchain generation.

Major Changes
^^^^^^^^^^^^^

- Restore the embedded Console and refresh the README to clarify the community fork position.
- Add GitHub CI/CD workflows as the base for automated builds and multi-platform delivery.
- Add quick links for docs, Docker, the GitHub repository, and installation through the ``pig`` package manager.

Fixed CVEs
^^^^^^^^^^

These issues were absorbed as part of the Go ``1.25.5`` -> ``1.26.0`` upgrade:

- `CVE-2025-68121 <https://pkg.go.dev/vuln/GO-2026-4337>`__: ``crypto/tls`` could incorrectly accept mutated CA configuration during session resumption.
- `CVE-2025-61730 <https://pkg.go.dev/vuln/GO-2026-4340>`__: TLS 1.3 could process handshake messages incorrectly across encryption-level boundaries.
- `CVE-2025-61726 <https://pkg.go.dev/vuln/GO-2026-4341>`__: ``net/url`` query parsing could be abused for memory exhaustion.
- `CVE-2025-61728 <https://pkg.go.dev/vuln/GO-2026-4342>`__: ``archive/zip`` could consume excessive CPU while building archive indexes.
- `CVE-2025-68119 <https://pkg.go.dev/vuln/GO-2026-4338>`__: ``cmd/go`` could trigger unexpected code execution when invoking external VCS tooling.
- `CVE-2025-61731 <https://pkg.go.dev/vuln/GO-2026-4339>`__: the ``#cgo pkg-config:`` directive could be abused for arbitrary file writes.
- `CVE-2025-61732 <https://pkg.go.dev/vuln/GO-2026-4433>`__: ``cmd/cgo`` comment parsing discrepancies could enable code smuggling.

Related Commits
^^^^^^^^^^^^^^^

- `8630937 <https://github.com/pgsty/minio/commit/8630937e7d1c3426ae28508e06f7091d7bde3a49>`__: Restore embedded console and update README for community fork
- `5d57938 <https://github.com/pgsty/minio/commit/5d57938939008cd5eeafea4532a3995ed1a2f206>`__: add github ci/cd pipeline

RELEASE.2025-12-03T12-00-00Z
----------------------------

2025-12-15: `https://github.com/pgsty/minio/releases/tag/RELEASE.2025-12-03T12-00-00Z <https://github.com/pgsty/minio/releases/tag/RELEASE.2025-12-03T12-00-00Z>`__

This is the earliest traceable community release. Its purpose is to establish the community packaging and distribution baseline rather than to deliver incremental fixes over an earlier community release.

Major Changes
^^^^^^^^^^^^^

- Build the community packaging flow around ``minio/pkger``.
- Choose a maintenance-mode upstream MinIO baseline as the starting point for the community-maintained fork.
- Produce the first ``apk``, ``deb``, and ``rpm`` artifacts for ongoing community releases.

Fixed CVEs
^^^^^^^^^^

- This is the first community release. The GitHub Release does not provide a delta-style security-fix list against an earlier community version, and this page does not attempt to reconstruct the full historical CVE delta against the upstream maintenance baseline.

Related Commits
^^^^^^^^^^^^^^^

- `d4cd4b4 <https://github.com/pgsty/minio/commit/d4cd4b433797e1cdcd2e46cc72df45b0897bdc0a>`__: RELEASE.2025-12-03T12-00-00Z with go 1.25.5
