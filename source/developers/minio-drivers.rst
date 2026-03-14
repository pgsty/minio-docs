.. _minio-drivers:

================================
软件开发工具包（SDK）
================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

MinIO 提供以下软件开发工具包（SDK）：

- :ref:`Go <go-sdk>`
- :ref:`Python <python-sdk>`
- :ref:`Java <java-sdk>`
- :ref:`.NET <dotnet-sdk>`
- :ref:`JavaScript <javascript-sdk>`
- :ref:`Haskell <haskell-sdk>`
- :ref:`C++ <cpp-sdk>`
- :ref:`Rust <rust-sdk>`

.. _go-sdk:

Go
--

GitHub: `minio/minio-go <https://github.com/minio/minio-go>`__

最新版本：|go-sdk-version|

快速入门：:doc:`/developers/go/minio-go`

参考：:doc:`/developers/go/API`

通过 GitHub 下载

  .. code-block:: shell
     :class: copyable

     go get github.com/minio/minio-go/v7


.. _python-sdk:

Python
------

GitHub: `minio/minio-py <https://github.com/minio/minio-py>`__

最新版本：|python-sdk-version|

快速入门：:doc:`/developers/python/minio-py`

参考：:doc:`/developers/python/API`

安装
  - pip

    .. code-block:: shell
       :class: copyable

       pip3 install minio

  - 源码

    .. code-block:: shell
       :class: copyable

       git clone https://github.com/minio/minio-py
       cd minio-py
       python setup.py install


.. _java-sdk:

Java
----

GitHub: `minio/minio-java <https://github.com/minio/minio-java>`__

最新版本：|java-sdk-version|

快速入门：:ref:`minio-java-quickstart`

参考：:doc:`/developers/java/API`

安装
  - Maven

    .. code-block:: java
       :class: copyable
       :substitutions:

       <dependency>
           <groupId>io.minio</groupId>
           <artifactId>minio</artifactId>
           <version>|java-sdk-version|</version>
       </dependency>

  - Gradle

    .. code-block:: java
       :class: copyable
       :substitutions:

       dependencies {
           implementation("io.minio:minio:|java-sdk-version|")
       }

  - JAR

    从 `Sonatype Maven Central Repository <|java-jar-url|>`__ 下载与 SDK 版本 |java-sdk-version| 对应的最新 JAR 文件。

.. _dotnet-sdk:

.NET
----

GitHub: `minio/minio-dotnet <https://github.com/minio/minio-dotnet>`__

最新版本：|dotnet-sdk-version|

快速入门：:doc:`/developers/dotnet/minio-dotnet`

参考：:doc:`/developers/dotnet/API`

从 NuGet 下载
  在 NuGet Package Manager Console 中运行以下命令。

  .. code-block:: shell
     :class: copyable

     PM> Install-Package Minio


.. _javascript-sdk:

JavaScript
----------

GitHub: `minio/minio-js <https://github.com/minio/minio-js>`__

最新版本：|javascript-sdk-version|

快速入门：:doc:`/developers/javascript/minio-javascript`

参考：:doc:`/developers/javascript/API`

安装
  - NPM

    .. code-block:: shell
       :class: copyable

       npm install --save minio

  - 源码

    .. code-block:: shell
       :class: copyable

       git clone https://github.com/minio/minio-js
       cd minio-js
       npm install
       npm install -g


.. _haskell-sdk:

Haskell
-------

GitHub: `minio/minio-hs <https://github.com/minio/minio-hs>`__

最新版本：|haskell-sdk-version|

快速入门：:doc:`/developers/haskell/minio-haskell`

安装
  将 ``minio-hs`` 添加到项目 ``.cabal`` 的 ``dependencies`` 部分。

  或

  如果你使用 ``hpack``，请将 ``minio-hs`` 添加到 ``package.yaml`` 文件中。


.. _cpp-sdk:

C++
---

GitHub: `minio/minio-cpp <https://github.com/minio/minio-cpp>`__

参考：`MinIO C++ SDK Reference <https://minio-cpp.min.io/>`__

安装
  - ``vcpkg``

    .. code-block:: shell
       :class: copyable

       vcpkg install minio-cpp

  - 源码

    .. code-block:: shell
       :class: copyable

       git clone https://github.com/minio/minio-cpp
       cd minio-cpp
       wget --quiet -O vcpkg-master.zip https://github.com/microsoft/vcpkg/archive/refs/heads/master.zip
       unzip -qq vcpkg-master.zip
       ./vcpkg-master/bootstrap-vcpkg.sh
       ./vcpkg-master/vcpkg integrate install
       cmake -B ./build -DCMAKE_BUILD_TYPE=Debug -DCMAKE_TOOLCHAIN_FILE=./vcpkg-master/scripts/buildsystems/vcpkg.cmake
       cmake --build ./build --config Debug


  .. _rust-sdk:

Rust
----

GitHub: `minio/minio-rs <https://github.com/minio/minio-rs>`__

最新版本
  |rust-sdk-version|

参考：`MinIO Rust SDK Reference <https://docs.rs/minio/latest/minio/>`__

快速入门：:doc:`/developers/rust/minio-rust`

.. toctree::
   :titlesonly:
   :hidden:
   :maxdepth: 1

   /developers/go/minio-go
   /developers/python/minio-py
   /developers/dotnet/minio-dotnet
   /developers/java/minio-java
   /developers/javascript/minio-javascript
   /developers/haskell/minio-haskell
   /developers/rust/minio-rust
