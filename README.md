# GTK4新手教程

本教程是 [Gtk4-tutorial](https://github.com/ToshioCP/Gtk4-tutorial) 的中文版本。

#### 仓库内容

本教程将教你如何使用C语言和Gtk4库开发程序。本教程主要面向初学者，因此只会设计Gtk4中比较基础的部分。
教程目录在这篇文章的末尾，内容组织如下：

- 第3至23节介绍一些基础控件的使用，会编写一个简单的编辑器 `tfe` (Text File Editor)。
- 第24至27节介绍与绘图相关的 GtkDrawingArea。
- 第28节介绍了拖放功能。
- 第29至33节介绍列表模型和列表视图，包括 GtkListView，GtkGridView 和 GtkColumnView。另外还会介绍 GtkExpression。

#### Gtk4文档

你可以从 [Gtk API 文档](https://docs.gtk.org/gtk4/index.html) 和 [Gnome 开发者文档](https://developer.gnome.org/) 获得更多相关资料.

这两个网站是2021年8月上线的。旧文档可以访问 [Gtk Reference Manual](https://developer-old.gnome.org/gtk4/stable/) 和 [Gnome Developer Center](https://developer-old.gnome.org/)。

如果你想了解 GObject 和类型系统, 可以参考 [GObject tutorial](https://github.com/ToshioCP/Gobject-tutorial)。GObject 相关的细节非常易懂，而且对于我们编写 Gtk4 程序很有帮助。

#### 参与贡献

本教程还未完成，尽管所有的代码都在 GTK 4（4.10.1版）的环境下通过了测试，还是可能有一些 Bug。如果你在教程和例子里发现了 Bug、错误或者文字等问题，请通知我。你可以去英文版仓库的 [issue](https://github.com/ToshioCP/Gtk4-tutorial/issues) 发帖，也可以自己纠正后将更新文件后推送到 [pull request](https://github.com/ToshioCP/Gtk4-tutorial/pulls)[^1]。注意，在更正时请只修改 src 目录下的文件，别动 `gfm` 和 `html` 文件夹下的文件。源文件被改了后，执行命令 `rake` 就能重新生成 GFM文件；执行命令 `rake html` 就能 自动更新 HTML 文件。

[^1]: 译者注：中文版会跟进更新。

如果有任何问题，都可以在 issue 中发布。任何提问都有助于提升本教程的质量。

#### 如何获取教程的HTML和PDF版本

如果你想要 HTML 或者 PDF 版本，用 `rake` 命令创建它们，这里 ruby 语言环境下的 “make” 命令。输入 `rake html` 生成 HTML文件，输入 `rake pdf` 生成 PDF 文件。详细信息参考文档 [How to build GTK 4 Tutorial](https://github.com/ToshioCP/Gtk4-tutorial/blob/main/gfm/Readme_for_developers.md)"。[^2]

[^2]: 目前中文版暂不提供HTML和PDF版本。

## 目录

\* 译者注：没有链接说明还未翻译。

1. [先决条件和许可](src/sec01.md)
2. [准备工作](src/sec02.md)
3. [GtkApplication 和 GtkApplicationWindow](src/sec03.md)
4. [控件介绍 (1)](src/sec04.md)
5. [控件介绍 (2)](src/sec05.md)
6. [字符串和内存管理](src/sec06.md)
7. [控件介绍 (3)](src/sec07.md)
8. [定义子对象](src/sec08.md)
9. [UI 文件和 GtkBuilder](src/sec09.md)
10. [构建系统](src/sec10.md)
11. [初始化和销毁实例](src/sec11.md)
12. 信号
13. TfeTextView 中的函数
14. GtkNotebook 中的函数
15. [Tfe 主程序](src/sec15.md)
16. [如何构建 tfe](src/sec16.md) (文本文件编辑器)
17. [菜单和行为](src/sec17.md)
18. 状态行为
19. 菜单和行为的 UI 文件
20. 复合控件和对话框
21. GtkFontDialogButton and GSettings
22. Tfe窗口
23. Pango, CSS and Application
24. GtkDrawingArea 和 Cairo
25. 周期性事件
26. 自定义绘图
27. Tiny turtle graphics interpreter
28. 拖放组件
29. GtkListView
30. GtkGridView 和激活信号
31. GtkExpression
32. GtkColumnView
33. GtkSignalListItemFactory