主页：[教程介绍](../README.md)，上一节：[第02节](sec02.md)，下一节：[第04节](sec04.md)

# 3 GtkApplication 和 GtkApplicationWindow

## GtkApplication

### GtkApplication 和 g\_application\_run

通常人们编写编程代码来创建应用程序。那么什么是应用程序呢？应用程序通常是使用库（libraries）运行的软件，库包括操作系统、框架等。在 Gtk4 编程中，GtkApplication 是使用 Gtk 库运行的程序（或可执行文件）。

创建 GtkApplication 的一个最基本的方法如下：

- 创建一个 GtkApplication 实例
- 运行应用程序

就这么简单。下面的程序实现了上面所描述的内容：

~~~C
 1 #include <gtk/gtk.h>
 2 
 3 int
 4 main (int argc, char **argv) {
 5   GtkApplication *app;
 6   int stat;
 7 
 8   app = gtk_application_new ("com.github.ToshioCP.pr1", G_APPLICATION_DEFAULT_FLAGS);
 9   stat =g_application_run (G_APPLICATION (app), argc, argv);
10   g_object_unref (app);
11   return stat;
12 }
~~~

第一行的 `include` 指令引入了 Gtk 库。 `main` 函数是 C 语言程序的入口函数。
变量 `app` 是一个<u>指向 GtkApplication 实例</u>的指针。
函数 `gtk_application_new` 创建了一个 GtkApplication 实例，然后返回了实例的指针。GtkApplication 实例其实是一个 C 语言中的结构体，其中保存了关于这个应用的所有信息。该函数的参数会在后面说明。函数 `g_application_run` 运行该实例定义的应用程序（我们常说这个函数运行了 `app`，实际上 `app` 不是应用程序，而是指向应用程序实例的指针。但这样说一般也不会发生任何混淆）。

我在这里使用了词语 **实例**。实例（instance）、类（class）和对象（object）是面向对象编程中的术语。
我用这些词代表同样的情况。但是在这个教程中，我会经常用 “对象” 而不是 “实例” 。这意味着你遇到对象和实例词语时可以当作一回事。
对象是一个有点模棱两可的词。广义上说，对象的含义比实例更宽。
因此，请读者小心上下文准确理解 “对象” 的含义。在许多情况下，对象和实例是通用的。

函数 `gtk_application_new` 有两个参数。

- 应用程序 ID （com.github.ToshioCP.pr1）。
它用于按系统区分应用程序。格式为 reverse-DNS，更多信息参见 [GNOME 开发人员文档 – 应用程序 ID 信息](https://developer.gnome.org/documentation/tutorials/application-id.html) 。

- 应用程序标志 （G\_APPLICATION\_DEFAULT\_FLAGS）。
如果应用程序没有带任何参数就运行，则标志为 G\_APPLICATION\_DEFAULT\_FLAGS。否则需要换成其他标志。
有关更多信息，请参阅 [GIO API 参考](https://docs.gtk.org/gio/flags.ApplicationFlags.html) 。

注意：如果您的 GLib-2.0 版本低于 2.74，请使用 `G_APPLICATION_FLAGS_NONE` 表示第 2 个参数。
它是一个旧标志，从 2.74 版本之后被弃用，由 `G_APPLICATION_DEFAULT_FLAGS` 代替。

使用下面的命令编译程序，`pr1.c` 是上面程序的文件名。

~~~
$ gcc `pkg-config --cflags gtk4` pr1.c `pkg-config --libs gtk4`
~~~

> 如果你已经下载了这个仓库，不需要自己创建这个文件。在仓库的 `src/misc/pr1.c` 有同名文件。
教材中所有的案例代码都在 `src` 目录下。

C 编译器生成了可执行文件 `a.out`，现在可以执行它：

~~~
$ ./a.out

(a.out:13533): GLib-GIO-WARNING **: 15:30:17.449: Your application does not implement
g_application_activate() and has no handlers connected to the "activate" signal.
It should do one of these.
$
~~~

哦，竟然产生了一条错误消息。但毫无疑问， 这条错误消息表明 GtkApplication 对象已经被运行了。
现在，让我们来看看错误信息是什么意思。

### 信号

该错误消息告诉我们：

1. 应用 GtkApplication 没有实现 `g_application_activate()`，
2. 它没有连接到“activate”信号的处理程序，并且
3. 你至少需要解决其中一个问题。

这两个错误原因都与信号有关。因此，我将首先向您解释什么是**信号**（signal）

在时间驱动的编程模型中，发生某事时会发出信号。例如，一个窗口被创建，一个窗口被销毁等等。
前面应用程序被激活或启动 [^1] 时，发出了一个名为 “activate” 的信号。
如果信号连接到一个函数，该函数称为信号处理程序（signal handler）或简称为处理程序（handler），然后在对应信号发出时调用对应的 handler。

[^1]: 激活（activated）和启动（started）稍微有点不同，但是目前你可以把他们两个当作一回事。

流程是这样的：

1. 有事发生。
2. 如果它与某个信号有关，则发出该信号。
3. 如果信号已连接到处理程序，则调用处理程序。

信号在对象中定义。例如，“activate”信号属于 GApplication 对象，即GtkApplication 对象的父对象。

GApplication 对象是 GObject 对象的子对象。GObject 是所有对象层次结构中的顶层对象。

~~~
GObject -- GApplication -- GtkApplication
<---parent                      --->child
~~~

子对象从其父对象继承信号、函数、属性等。所以，GtkApplication 也有“activate”信号。

现在我们可以解决`pr1.c`中的问题了。我们需要将“activate”信号连接到一个处理程序。我们使用函数 `g_signal_connect` 将信号连接到处理程序。

~~~C
 1 #include <gtk/gtk.h>
 2 
 3 static void
 4 app_activate (GApplication *app, gpointer *user_data) {
 5   g_print ("GtkApplication is activated.\n");
 6 }
 7 
 8 int
 9 main (int argc, char **argv) {
10   GtkApplication *app;
11   int stat;
12 
13   app = gtk_application_new ("com.github.ToshioCP.pr2", G_APPLICATION_DEFAULT_FLAGS);
14   g_signal_connect (app, "activate", G_CALLBACK (app_activate), NULL);
15   stat =g_application_run (G_APPLICATION (app), argc, argv);
16   g_object_unref (app);
17   return stat;
18 }
19 
~~~

首先我们定义了一个 handler `app_activate`，它只打印一个信息。`g_print` 函数定义在 GLib 里，它和 C 标准库的 `printf` 很像。
在 `main` 函数中, 我们在 `g_application_run` 之前添加了函数 `g_signal_connect` 以连接信号处理器。函数 `g_signal_connect` 有四个参数：

1. 信号所属的实例
2. 信号的名称
3. 一个 handler【处理程序】(也称为callback【回调函数】), 必须使用宏 `G_CALLBACK` 转换类型
4. 传入 handler 的数据。如果无需传入数据，填 NULL 。

在 [GObject API 参考手册](https://docs.gtk.org/gobject/func.signal_connect.html) 中有描述 `g_signal_connect`。
准确的说，`g_signal_connect` 是一个宏（不是一个 C 函数）

~~~c
#define g_signal_connect (
    instance,
    detailed_signal,
    c_handler,
    data
)
~~~

您可以在 API 参考手册中找到每个信号的描述。例如，“activate”信号位于 [GIO API Reference](https://docs.gtk.org/gio/signal.Application.activate.html) 的 GApplication 部分。

~~~c
void
activate (
  GApplication* self,
  gpointer user_data
)
~~~

这是信号处理函数 “activate” 的相关描述。在上面的声明里你可以用任何名字代替 “activate”。
参数包括：

- self 是一个型号所属的实例
- user\_data 是来自 `g_signal_connect` 函数的第 4 个参数 data
如果 data 是填的 NULL，那么你可以忽略，把第 2 个参数留空。

此外，API 参考手册非常重要，可以通过查阅该手册来编写 Gtk 应用程序。这些手册位于 ['GTK Documentation'](https://docs.gtk.org/)。

让我们编译上面的源文件（`pr2.c`）并运行它。

~~~
$ gcc `pkg-config --cflags gtk4` pr2.c `pkg-config --libs gtk4`
$ ./a.out
GtkApplication is activated.
$
~~~

Good job! 但是键入这么长的一行内容进行编译是比较麻烦的，我们可以使用 shell 脚本来解决这个问题。我们可以创建一个包含以下内容的文本文件。

~~~
gcc `pkg-config --cflags gtk4` $1.c `pkg-config --libs gtk4`
~~~

然后保存到 `$HOME/bin` 目录, 通常为 `/home/(username)/bin` [^2]。另外记得使用 chmod 命令添加运行权限。如果文件名是 `comp`, 那么我们可以像下面这样做：

[^2]: 如果你的名字是 James, 那么对应的目录应该是 `/home/james/bin`

~~~
$ chmod 755 $HOME/bin/comp
$ ls -log $HOME/bin
    ...  ...  ...
-rwxr-xr-x 1   62 May 23 08:21 comp
    ...  ...  ...
~~~

如果这是您第一次创建 `$HOME/bin` 目录并在其中保存文件，那么您需要注销并再次登录。

~~~
$ comp pr2
$ ./a.out
GtkApplication is activated.
$
~~~

## GtkWindow 和 GtkApplicationWindow

### GtkWindow

在上一小节中我们让程序在收到 activate 信号时打印出消息 “GtkApplication  is activated.”。然而这还不够，因为 Gtk 是图形用户界面 (GUI) 的框架。所以接下来我们继续在这个程序中添加一个窗口。我们需要：

1. 创建一个 GtkWindow
2. 将其和 GtkApplication 连接起来
3. 显示窗口

现在我们来重新编写 `app_activate`。

#### 创建 GtkWindow

~~~C
1 static void
2 app_activate (GApplication *app, gpointer user_data) {
3   GtkWidget *win;
4 
5   win = gtk_window_new ();
6   gtk_window_set_application (GTK_WINDOW (win), GTK_APPLICATION (app));
7   gtk_widget_present (GTK_WINDOW (win));
8 }
~~~

Widget是一个抽象的概念，包含了所有的GUI界面，如窗口、对话框、按钮、多行文本、容器等。
GtkWidget 是一个基础对象，所有的 GUI 对象都从它派生。

~~~
parent <-----> child
GtkWidget -- GtkWindow
~~~

GtkWindow 在其对象的顶部包含 GtkWidget，感兴趣的可以查看 GtkWindow 内部结构是如何定义的。

![GtkWindow and GtkWidget](image/window_widget.png)

下面是函数 `gtk_window_new` 的声明：

~~~C
GtkWidget *
gtk_window_new (void);
~~~

根据这个定义，它返回一个指向 GtkWidget，而不是 GtkWindow 的指针。它实际上创建了一个新的 GtkWindow 实例（不是 GtkWidget），但返回了一个指向 GtkWidget 的指针。但是，指针指向的是GtkWidget，同时它也指向了包含GtkWidget的GtkWindow。

如果你想使用 `win` 作为 GtkWindow 类型的实例的指针，你需要强制转换它：

~~~C
(GtkWindow *) win
~~~

它起作用，但是一般不这么写。
用“GTK_WINDOW”宏用来代替这种功能：

~~~C
GTK_WINDOW (win)
~~~

使用宏转换类型是一种比较推荐的方式，因为它不仅强转了指针类型，还做了类型检查。

#### 连接到 GtkApplication.

`gtk_window_set_application` 函数用于将 GtkWindow 连接到 GtkApplication。

~~~C
gtk_window_set_application (GTK_WINDOW (win), GTK_APPLICATION (app));
~~~

您需要将 `win` 转换为 GtkWindow 并将 `app` 转换为 GtkApplication。`GTK_WINDOW` 和 `GTK_APPLICATION` 宏用于完成以上两项工作。

GtkApplication 会持续运行，直到相关窗口被销毁。如果你没有连接 GtkWindow 和 GtkApplication，GtkApplication 会立即销毁自己。因为没有窗口连接到 GtkApplication，所以 GtkApplication 不需要等待任何东西。当它自己销毁时，GtkWindow 也被销毁了。

#### 显示窗口

函数 `gtk_widget_show` 用于向用户显示窗口（给用户看的界面）。

Gtk4 默认将控件可见性设置为可见，因此每个小部件都不需要此功能来显示自己。但是，有一个例外。顶部窗口（这个术语将在后面解释）在创建时是不可见的。所以你需要使用上面的函数来显示窗口。

您可以改用 `gtk_widget_set_visible (win, true)` 代替 `gtk_window_present`。但是这两个代码的行为逻辑是不同的。
假设屏幕上有两个窗口 win1 和 win2， Win1 在 Win2 后面，两个窗口都可见。
函数 `gtk_widget_set_visible (win1, true)` 不执行任何操作，因为 win1 已经可见。所以，win1 仍然在 win2 后面。
另一个函数 `gtk_window_present (win1)` 会将 Win1 移动到窗口的栈顶。
因此，如果你要的是窗口出现的效果，应该用 `gtk_window_present`。

GTK 4.10 之后 `gtk_widget_show` 和 `gtk_widget_hide` 这两个方法都被废弃了，你需要用 `gtk_widget_set_visible` 代替。

将程序保存为 `pr3.c` 编译并运行它。

~~~
$ comp pr3
$ ./a.out
~~~

执行程序，然后一个小窗口会显示出来。

![Screenshot of the window](image/screenshot_pr3.png)

单击关闭按钮，然后窗口消失，程序结束。

### GtkApplicationWindow

GtkApplicationWindow 是 GtkWindow 的子对象。它有一些额外的功能可以更好地与 GtkApplication 集成。
推荐用 GtkApplicationWindow 作为应用程序的顶层窗口，而不是 GtkWindow。

现在重写程序并使用 GtkApplicationWindow。

~~~C
1 static void
2 app_activate (GApplication *app, gpointer user_data) {
3   GtkWidget *win;
4 
5   win = gtk_application_window_new (GTK_APPLICATION (app));
6   gtk_window_set_title (GTK_WINDOW (win), "pr4");
7   gtk_window_set_default_size (GTK_WINDOW (win), 400, 300);
8   gtk_widget_present (GTK_WINDOW(win));
9 }
~~~

创建 GtkApplicationWindow 时，需要将 GtkApplication 实例作为参数。然后它会自动连接这两个实例。所以你不需要再调用`gtk_window_set_application`了。接着程序设置窗口的标题和默认大小。

编译并运行`a.out`，然后你会看到一个更大的窗口，它的标题是“pr4”。

![Screenshot of the window](image/screenshot_pr4.png)

主页：[教程介绍](../README.md)，上一节：[第02节](sec02.md)，下一节：[第04节](sec04.md)
