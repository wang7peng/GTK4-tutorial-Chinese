主页：[教程介绍](../README.md)，上一节：[第15节](sec15.md)，下一节：[第17节](sec17.md)

# 如何构建 tfe (文本文件编辑器)

## 如何编译和运行文本编辑器 'tfe'.

首先，源代码都在 [Gtk4-tutoril 仓库](https://github.com/ToshioCP/Gtk4-tutorial)。
在 [上一章节](sec15.md) 的末尾写了下载它们的方法。

以下是编译和执行的指导。

- 需要工具 meson 和 ninja [^1].
[^1]: 在 debian 上直接 `sudo apt install -y meson`，会顺带装上 ninja
- 如果你的 `gtk4` 是从源码安装的，你需要设置环境变量以适应你的安装。
- （敲 `cd` 命令）进入到 `src/tfe5` 目录。
- 敲入 `meson setup _build` 进行配置。
- 敲入 `ninja -C _build` 进行编译。然后，应用程序 `tfe` 会生成到 `_build` 目录下。
- 要执行它，敲入 `_build/tfe` 。

运行后出现窗口。
上面有四个按钮，新建 `New` 、打开 `Open` 、 保存 `Save` 和 关闭功能 `Close` 。

- 点击 `Open` 按钮，会出现文件选择对话框。
选择列表中的一个文件，然后单击 `Open` 按钮。然后该文件会被读取并在一个新页面上显示内容。
- 编辑文件，单击 `Save` 按钮，会将文本内容保存到原文件中。
- 点击 `Close` 按钮，笔记本页面将消失。
- 再次点 `Close` 按钮， **无题的**（Untitled）笔记本页面将消失，同时应用程序将退出。

这是一个非常简单的编辑器。
你在上面增加更多的功能是一种很好的练习。

## 统计行，单词和字符的总数

~~~
$ LANG=C wc tfe5/meson.build tfe5/tfeapplication.c tfe5/tfe.gresource.xml tfe5/tfenotebook.c tfe5/tfenotebook.h tfetextview/tfetextview.c tfetextview/tfetextview.h tfe5/tfe.ui
   10    17   294 tfe5/meson.build
  110   334  3601 tfe5/tfeapplication.c
    6     9   153 tfe5/tfe.gresource.xml
  144   390  3668 tfe5/tfenotebook.c
   15    21   241 tfe5/tfenotebook.h
  235   821  8473 tfetextview/tfetextview.c
   32    54   624 tfetextview/tfetextview.h
   61   100  2073 tfe5/tfe.ui
  613  1746 19127 total
~~~

\* 其他统计方法

~~~
$ sudo apt install -y cloc
$ cloc -q tfe5/meson.build tfe5/tfeapplication.c tfe5/tfe.gresource.xml tfe5/tfenotebook.c tfe5/tfenotebook.h tfetextview/tfetextview.c tfetextview/tfetextview.h tfe5/tfe.ui
github.com/AlDanial/cloc v 1.96  T=0.01 s (565.8 files/s, 43352.5 lines/s)
-------------------------------------------------------------------------------
Language                     files          blank        comment           code
-------------------------------------------------------------------------------
C                                3             77             15            397
Qt                               1              1              0             60
C/C++ Header                     2             14              1             32
Meson                            1              4              0              6
XML                              1              0              0              6
-------------------------------------------------------------------------------
SUM:                             8             96             16            501
-------------------------------------------------------------------------------
~~~

主页：[教程介绍](../README.md)，上一节：[第15节](sec15.md)，下一节：[第17节](sec17.md)