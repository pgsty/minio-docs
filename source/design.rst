:orphan:

========
设计页
========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

概览
----

此页面旨在辅助 MinIO 文档的 UI/UX 工作。你可以将其视为一个汇集了各类对象和标题的“厨房水槽”页面，以便更直观地查看当前所有元素的呈现效果。凡是出现在本页上的内容，都会在文档的其他位置存在。

如果你并未参与 MinIO 的 UI/UX 工作，那么此页面没有任何有用内容。
请返回 :doc:`索引 </index>`。

常用标记
--------

文本标记
~~~~~~~~

*这是斜体文本*

**这是粗体文本**

``这是等宽文本``

`这是超链接文本 <https://min.io>`__

.. code-block:: shell
   :class: copyable

   echo "This is a code block. It includes as small copy hover in the top-right."

   echo "Syntax highlighting is automatic via pygments."

容器
~~~~

.. container::

   此文本位于一个容器中。容器有助于对文本进行自定义渲染。
   你可以为容器添加自定义 class 来测试 CSS：

   .. code-block:: shell

      .. container::
         :class: class-name

Sphinx Design
-------------

以下组件来自
:github:`sphinx-design <executablebooks/sphinx-design>`

选项卡
~~~~~~

MinIO 使用 ExecutableBooks 的
:github:`sphinx-design <executablebooks/sphinx-design>` 库来实现选项卡。

.. tab-set::

   .. tab-item:: 纯文本

      这是纯文本内容

   .. tab-item:: 文本与代码

      这是带有代码的纯文本内容：

      .. code-block:: shell
         :class: copyable

         mc admin info ALIAS

你可以让选项卡保持同步。例如，如果页面上有多个包含 Console 和 CLI 选项卡的步骤说明，
则可以在每个 `tab-item` 上使用 `:sync: key1` 选项。
当用户选择其中一个选项卡时，页面上所有其他带有相同 key 的选项卡也会同步切换。

.. tab-set::

   .. tab-item:: Console
      :sync: gui

      这是纯文本内容

   .. tab-item:: 命令行
      :sync: cli

      这是带有代码的纯文本内容：

      .. code-block:: shell
         :class: copyable

         mc admin info ALIAS

卡片
~~~~

.. card:: 卡片标题

   这是卡片头部
   ^^^
   这是卡片内部的内容。

   卡片可以包含不同类型的内容。

   .. code-block:: shell

      echo "This is some code block"

   +++
   这是卡片底部

.. card:: 此卡片可点击
   :link: https://min.io

   点击此卡片会跳转到 https://min.io

.. card:: 此卡片可点击
   :link: objects
   :link-type: ref

   点击此卡片会跳转到 ``objects`` 引用锚点所在的位置。

.. card-carousel:: 4

   .. card:: 轮播卡片 1

      这是轮播中的第一项

   .. card:: 轮播卡片 2

      这是轮播中的第二项

   .. card:: 轮播卡片 3

      这是轮播中的第三项

   .. card:: 轮播卡片 4

      这是轮播中的第四项

网格
~~~~

.. grid:: 1 1 3 3
   :outline:
   :gutter: 2

   .. grid-item::

      网格中的第一项

   .. grid-item::

      网格中的第二项

   .. grid-item::

      网格中的第三项

      .. grid:: 1 1 1 1
         :outline:

         .. grid-item::

            子网格项 1

         .. grid-item::

            子网格项 2

         .. grid-item::

            子网格项 3

.. grid:: 2 2 3 3
   :gutter: 3

   .. grid-item-card:: 卡片 1

      卡片 1 内容

   .. grid-item-card:: 卡片 2

      卡片 2 内容

   .. grid-item::
      :child-direction: row
      :child-align: spaced

      .. grid:: 1 1 1 1
         :gutter: 3
         :padding: 0

         .. grid-item-card:: 子卡片 1

            子卡片 1 内容

         .. grid-item-card:: 子卡片 2

            子卡片 2 内容

         .. grid-item-card:: 子卡片 3

            子卡片 3 内容

标题 1
------

.. cond:: mindocs

   .. include:: /includes/common/common-design.rst

这是一级标题下的内容。该标题包含一个用于链接的锚点标签。
本页的目录配置为最多显示 2 级标题。
该标题应显示在右侧 TOC 中。

标题 2
~~~~~~

这是二级标题下的内容。该标题包含一个用于链接的锚点标签。
本页的目录配置为最多显示 2 级标题。
该标题应显示在右侧 TOC 中。

标题 3
++++++

这是三级标题下的内容。该标题包含一个用于链接的锚点标签。
本页的目录配置为最多显示 2 级标题。
该标题*不应*显示在右侧 TOC 中。

标题 4
======

这是四级标题下的内容。该标题包含一个用于链接的锚点标签。
本页的目录配置为最多显示 2 级标题。
该标题*不应*显示在右侧 TOC 中。

提示块
------

MinIO 文档使用以下提示块类型。
提示块的 HTML 代码大致如下：

.. code-block:: shell

   <div class="admonition [warning|important|note|custom]>
      <p class="admonition-title"></p>
   </div>

附加 class 会在定义提示块时设置，并且可以是任意字符串。
Sphinx 为 ``warning``、``note`` 和 ``custom`` 提供了默认样式。

Note 提示块
~~~~~~~~~~~

note 提示块的渲染效果如下：

.. note::

   此文本位于 note 正文中。其中包含一些
   ``monospaced``、**bold** 和 *italics*。

   这是一个指向文档中其他页面的 :doc:`链接 </index>`。

   这是一个指向外部页面的 `链接 <https://min.io>`__。

你可以为 note 标题设置自定义文本：

.. admonition:: 带有 ``monospaced`` 文本的自定义标题
   :class: note

   此文本位于 note 正文中。其中包含一些
   ``monospaced``、**bold** 和 *italics*。

   这是一个指向文档中其他页面的 :doc:`链接 </index>`。

   这是一个指向外部页面的 `链接 <https://min.io>`__。

用于标记版本变更：

.. versionchanged:: RELEASE.2022-07-15T09-20-55Z

   ``mc license register`` 取代了 ``mc support register`` 命令。

Important 提示块
~~~~~~~~~~~~~~~~

important 提示块的渲染效果如下：

.. important::

   此文本位于 important 正文中。其中包含一些
   ``monospaced``、**bold** 和 *italics*。

   这是一个指向文档中其他页面的 :doc:`链接 </index>`。

   这是一个指向外部页面的 `链接 <https://min.io>`__。


你可以为 important 标题设置自定义文本：

.. admonition:: 带有 ``monospaced`` 文本的 important 标题
   :class: important

   此文本位于 important 正文中。其中包含一些
   ``monospaced``、**bold** 和 *italics*。

   这是一个指向文档中其他页面的 :doc:`链接 </index>`。

   这是一个指向外部页面的 `链接 <https://min.io>`__。

Warning 提示块
~~~~~~~~~~~~~~

warning 提示块的渲染效果如下：

.. warning::

   此文本位于 warning 正文中。其中包含一些
   ``monospaced``、**bold** 和 *italics*。

   这是一个指向文档中其他页面的 :doc:`链接 </index>`。

   这是一个指向外部页面的 `链接 <https://min.io>`__。


你可以为 warning 标题设置自定义文本：

.. admonition:: 带有 ``monospaced`` 文本的 warning 标题
   :class: warning

   此文本位于 warning 正文中。其中包含一些
   ``monospaced``、**bold** 和 *italics*。

   这是一个指向文档中其他页面的 :doc:`链接 </index>`。

   这是一个指向外部页面的 `链接 <https://min.io>`__。

通用提示块
~~~~~~~~~~

通用提示块可以应用任意 class。
如果我们希望以非常具体的设计来展示提示块，这会很有用。


.. admonition:: admonition-title
   :class: class-name

   此文本位于提示块正文中。其中包含一些
   ``monospaced``、**bold** 和 *italics*。

   这是一个指向文档中其他页面的 :doc:`链接 </index>`。

   这是一个指向外部页面的 `链接 <https://min.io>`__。

列表
----

列表表格
~~~~~~~~

Sphinx 提供了专门的标记语法来生成整洁的表格，而不是使用 ASCII 风格的表格定义。

下面的 ``.. list-table`` 具有单个表头行和多列：

.. list-table::
   :header-rows: 1
   :widths: 25 25 25 25
   :width: 100%

   * - 行标题 1
     - 行标题 2
     - 行标题 3
     - 行标题 4

   * - 列项 1
     - 列项 2
     - 列项 3
     - 列项 4

   * - 列项 1
     - 列项 2
     - 列项 3
     - 列项 4

   * - 列项 1
     - 列项 2
     - 列项 3
     - 列项 4

下面的 ``.. list-table`` 使用了 stub 列，其中第一列包含“header”或标题：

.. list-table::
   :stub-columns: 1
   :widths: 25 25 25 25
   :width: 100%

   * - 行标题 1
     - 列项 1
     - 列项 2
     - 列项 3

   * - 行标题 2
     - 列项 1
     - 列项 2
     - 列项 3

   * - 行标题 3
     - 列项 1
     - 列项 2
     - 列项 3

   * - 行标题 4
     - 列项 1
     - 列项 2
     - 列项 3

无序列表与有序列表
~~~~~~~~~~~~~~~~~~

这是一个无序列表：

- 条目 A
- 条目 B
   - 条目 B.1
   - 条目 B.2
- 条目 C
   - 条目 C.1
   - 条目 C.2
   - 条目 C.3
      - 条目 C.3.1
      - 条目 C.3.2

这是一个有序列表：

1) 条目 A

2) 条目 B

  1) 条目 B.1

  2) 条目 B.2

3) 条目 C

  3) 条目 C.1

  4) 条目 C.2

  5) 条目 C.3

    1) 条目 C.3.1

    2) 条目 C.3.2

定义列表
~~~~~~~~

Sphinx 标记语法包含用于生成描述列表（Description List）及各类描述细节的语法。
这类内容通常*不会*带有锚点，因此用途会受到一定限制。
不过，它们适合用来创建视觉上区分明显的列表，便于快速滚动浏览。
它们在参考文档中使用得很频繁。

描述列表标题 1
  这是该标题的描述正文。

  定义列表中的另一段内容

描述列表标题 2
  这是该标题的描述正文。

  定义列表中的另一段内容

描述列表标题 3
  这是该标题的描述正文。

  定义列表中的另一段内容

引用定义
--------

Sphinx 支持创建自定义的引用类型 directive。
我们在整个文档中使用了其中若干种。
下面的章节包含一些定义示例，开头的列表会链接到各个定义。

- :mc:`foo`
- :mc-cmd:`foo bar`
- :mc-cmd:`foo bar baz`
- :data:`foo`
- :data:`foo.bar`
- :std:option:`--foo`


.. mc:: foo

这里实际上有一个用于链接但不用于显示的顶层定义。
这是有意为之（至少目前如此）。

.. mc-cmd:: bar
   :fullpath:

   用于定义 CLI 命令。

   .. mc-cmd:: bin

      用于定义 CLI 命令的各种参数

   .. mc-cmd:: baz

      用于定义 CLI 命令的某个选项

.. data:: foo

   一个可供引用的通用数据项。

   .. data:: bar

      这些定义是嵌套的，并且可以链接。

.. std:option:: --foo

   用于定义 CLI 命令的全局参数。

   示例：

   .. code-block::

      :std:option:`--json <mc.--json>`


图片
----

.. image:: /images/minio-console/minio-console.png
   :width: 600px
   :alt: MinIO Console Landing Page 为已认证用户提供对象浏览器视图
   :align: center
