主页：[教程介绍](../README.md)，上一节：[第01节](sec01.md)，下一节：[第03节](sec03.md)

# 2 准备工作 

## 在 Linux 上安装 Gtk4

本节教程描述如何在 Linux 发行版上安装 Gtk4。

有两种方式安装 Gtk4：

- 使用 Linux 自带的包管理系统安装，例如 apt
- 从源文件编译安装

### 使用包管理器安装

这是最简单的安装方式，我已经在 Ubuntu 24.04 长期支持版上成功安装了 Gtk4。

只需要输入命令就能安装：
`$ sudo apt install libgtk-4-dev`

安装这个开发工具包（libgtk-4-dev）很重要，否则，你无法编译出任何基于 GTK4 运行的程序。

Fedora、Debian、Arch、Gentoo 和 OpenSUSE 的 GTK4 开发包也可以通过对应的包管理器进行安装，请参考 [Installing GTK from packages](https://www.gtk.org/docs/installations/linux#installing-gtk-from-packages)。

> 译者注：如果你已经通过此方法安装了 Gtk4，那么无需再阅读下面的内容。

### 使用源码编译安装

如果你想使用正在开发中的GTK版本，需要从源码开始构建它。详细步骤参阅官方在线文档《GTK4 API Reference》中的 [构建GTK库](https://docs.gtk.org/gtk4/building.html) 章节。

> 译者注：作者用源码安装了 Gtk4 还是在 2021 年。因此，以下信息尤其是每个软件的版本是旧信息。


**1 安装位置**
我在 `$HOME/local` 目录下安装了 Gtk4。这是一个私人用户区。如果要安装在系统区，官方文档给出的示例是在 `/opt/gtk4` 目录。

不要将它安装到默认的 `/usr/local`。很多不是基于 Gtk4 构建的 Ubuntu 应用程序会使用此目录下系统自带的 Gtk 库。因此，安装在默认位置风险很高，很可能会发生不好的事情。

> 大多数必需的库都包含在 Ubuntu 中，可以直接用 apt 命令安装它们，不需要从源码安装。您可以跳过下面有关依赖库安装（Glib、Pango、Gdk-pixbuf）的小节。

**2 Glib 安装**
用命令 `pkg-config --modversion glib-2.0` 检查系统中自带的的库的版本，Glib 必须是 2.66.0 以上的版本。如果它低于必要的版本（比如您的 Ubuntu 是20.04LTS 之前），请从源码安装。

从源码安装 Glib。我安装了 2.86.0，这是当时（2025年10月）的最新版本。

```bash
# 下载 Glib 源码
$ wget https://download.gnome.org/sources/glib/2.86/glib-2.86.0.tar.xz

# 解压并提取文件
$ tar -Jxf glib-2.86.*
```

编译 Glib 可能需要额外的一些库

```bash
# 用 meson 来找到它们
$ meson --prefix $HOME/local _build

# 使用 apt-get 来安装这些依赖：
$ sudo apt-get install -y  libpcre2-dev libffi-dev
```

装完依赖库之后即可编译 Glib：

```bash
$ rm -rf _build
$ meson --prefix $HOME/local _build
$ ninja -C _build
$ ninja -C _build install
```

由于后面的安装还需要 Glib，所以我们可以设置一些环境变量，以便后面的编译过程能够找到 Glib，把下面的内容保存为文件 `env.sh`：

```bash
# compiler
CPPFLAGS="-I$HOME/local/include"
LDFLAGS="-L$HOME/local/lib"
PKG_CONFIG_PATH="$HOME/local/lib/pkgconfig:$HOME/local/lib/x86_64-linux-gnu/pkgconfig"
export CPPFLAGS LDFLAGS PKG_CONFIG_PATH
# linker
LD_LIBRARY_PATH="$HOME/local/lib/x86_64-linux-gnu/"
PATH="$HOME/local/bin:$PATH"
export LD_LIBRARY_PATH PATH
# gsetting
export GSETTINGS_SCHEMA_DIR=$HOME/local/share/glib-2.0/schemas
```

然后使用 . (dot) 或者 source 命令将这些环境变量导入到当前的bash：

```bash
$ . env.sh        # 方式1
$ source env.sh   # 方式2
```

它会执行 `env.sh` 中的命令来更改当前 shell 中的环境变量。

**Pango安装**

下载和解压：

    $ wget https://download.gnome.org/sources/pango/1.57/pango-1.57.0.tar.xz
    $ tar -Jxf pango-1.57.*

可以使用 meson 确定依赖库，然后安装所有依赖库，然后编译安装 Pango：

    $ meson --prefix $HOME/local _build
    $ ninja -C _build
    $ ninja -C _build install

上面的命令将 Pango-1.0.gir 安装在 `$HOME/local/share/gir-1.0` 目录。如果您安装 Pango 时没有指定 `--prefix` 选项，那么它将位于 `/usr/local/share/gir-1.0`。此目录 (/usr/local/share) 由一些应用程序使用。这些应用程序通过环境变量 `XDG_DATA_DIRS` 找到该目录。它是一个文本文件，保存了“共享”目录的列表，如“/usr/share”、“usr/local/share”等。现在需要将 `$HOME/local/share` 添加到 `XDG_DATA_DIRS` 中，否则后面编译会出错。

    $ export XDG_DATA_DIRS=$HOME/local/share:$XDG_DATA_DIRS

**安装 Gdk-pixbuf**

下载和解压：

    $ wget https://download.gnome.org/sources/gdk-pixbuf/2.42/gdk-pixbuf-2.42.2.tar.xz
    $ tar -Jxf gdk-pixbuf-2.42.2.tar.xz


和之前一样，安装依赖包，然后编译并安装它们。


### 安装 Gtk4

如果你想安装最新的版本，可以克隆最新的仓库：

    $ git clone https://gitlab.gnome.org/GNOME/gtk.git

如果想要安装最新的稳定版本，那么可以从 [Gnome source website](https://download.gnome.org/sources/gtk/) 下载。目前最新版本是 4.21.0 (2025年10月)。

编译安装：

    $ meson --prefix $HOME/local _build
    $ ninja -C _build
    $ ninja -C _build install


### 修改 env.sh

在你退出登录之后，之前手动设置的临时环境变量会消失，需要重新添加，因此修改 `env.sh`：

    # compiler
    CPPFLAGS="-I$HOME/local/include"
    LDFLAGS="-L$HOME/local/lib"
    PKG_CONFIG_PATH="$HOME/local/lib/pkgconfig:$HOME/local/lib/x86_64-linux-gnu/pkgconfig:
    $HOME/local/share/pkgconfig"
    export CPPFLAGS LDFLAGS PKG_CONFIG_PATH
    # linker
    LD_LIBRARY_PATH="$HOME/local/lib/x86_64-linux-gnu/"
    PATH="$HOME/local/bin:$PATH"
    export LD_LIBRARY_PATH PATH
    # gir
    XDG_DATA_DIRS=$HOME/local/share:$XDG_DATA_DIRS
    export XDG_DATA_DIRS
    # gsetting
    export GSETTINGS_SCHEMA_DIR=$HOME/local/share/glib-2.0/schemas
    # girepository-1.0
    export GI_TYPELIB_PATH=$HOME/local/lib/x86_64-linux-gnu/girepository-1.0

在安装 Gtk4 库之前，使用 . (dot) 或 source 命令运行。

您可能认为可以将它们添加到您的 `.profile` 中。但这是一个错误的决定。永远不要将它们写入您的`.profile`。只有在编译和运行 Gtk4 应用程序时才需要上述环境变量，否则没有必要。如果您更改了上述环境变量并运行 Gtk3 应用程序，可能会导致严重损坏。

### 编译 Gtk4 应用程序

编译 Gtk4 程序之前，需要执行上述的 `env.sh` 导入相关环境变量：

    $ . env.sh

之后即可以直接编译。例如要编译 `sample.c`，请键入以下内容。

    $ gcc `pkg-config --cflags gtk4` sample.c `pkg-config --libs gtk4`

要了解如何编译 Gtk4 应用程序，请参阅第 3 节（GtkApplication 和 GtkApplicationWindow）及之后的部分。


**编译测试**  尝试编译教程中编写的 `tfe` 文本编辑器来测试 Gtk4 开发包是否安装正确。在Linux系统中下载教程仓库，进入目录 `src/tfe7`。编译和运行：

```
    $ meson _build
    ... ...
    Project name: tfe
    Project version: undefined
    C compiler for the host machine: cc
    ... ...

    $ ninja -C _build
    ... ...

    $ ninja -C _build install
    ninja: Entering directory `_build'
    [0/1] Installing files.
    Installing tfe to /usr/local/bin
    ... ...

    $ tfe # 执行
```

之后 `tfe` 文本编辑器会显示出来。说明编译和执行已经成功了。

## 怎样下载这个仓库

有两个方法：压缩包和 git。最容易的方法是整体以 `zip` 压缩文件下载下来。不过，如果你用 `git` 工具克隆这个仓库，更新你本地的内容会更方便，用 `git pull` 命令就行。 

## 教程中的案例

程序示例都在仓库的 `src` 目录里。例如，教程的第一个例子是 `pr1.c`，它的路径就是 `src/misc/pr1.c`。所以你不需要自己去手敲代码。

主页：[教程介绍](../README.md)，上一节：[第01节](sec01.md)，下一节：[第03节](sec03.md)
