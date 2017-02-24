title: First Try on Typora

date: 2017/2/17 09:00:00

categories:

- Study

tags:

- BlogTech
- Markdown Editor

---



# Typora初探

### Before Typora

在Windows平台上，Typora之前，我还曾经接触过[MarkdownPad](http://www.markdownpad.com/)和[MarkPad](http://code52.org/DownmarkerWPF/)两款Markdown编辑工具。

MarkdownPad和MarkPad的特点：

- 都支持Markdown的所有语法，可以实现不动用鼠标就完成一篇排版精美文章的书写；
- 二者提供的都是两屏对照的实时渲染系统，即用户在左侧屏敲入带有Markdown语法的文字，在右侧屏会实时显示相应的HTML预览效果。
- 所生成的Markdown文件可以直接用于个人博客以及上传至Github，支持程度好。

不过，MarkdownPad和MarkPad仍有些许不同：

- MarkdownPad的实时渲染采用的是[Awesomium](http://www.awesomium.com/)，简单来说，Awesomium是一个HTML网页UI界面的渲染引擎，适用于基于C++或者.NET的应用程序。个人觉得，Awesomium对于一个Markdown编辑器来说，功能过于强大，以至于使得MarkdownPad的运行比较吃内存，有悖于Markdown轻量级编辑器的定位，同时在Windows10操作系统下实际使用的时候发现，内嵌于MarkdownPad中的Awesomium经常崩溃，出现无法渲染的情况，需要手动下载Awesomium SDK进行修复（据说安装SDK之后就可以修复，但是我没成功...）。
- MarkPad的渲染使用的是JekyII，一个基于Ruby语言的静态网页和网站生成工具。同时，JekyII还可以和Github结合，搭建一个免费静态博客。
- 此外，在外观和易用性上，MarkdownPad显得比较老气，风格与MS Office比较类似，常用标记功能的图标都列在菜单栏中，较为容易上手使用，MarkPad则较为有科技感，界面简洁，但是由于没有相应的常用功能列表，所以使用起来还需要经过一段时间的锻炼，适合已经熟悉Markdown语法的用户。

两屏对照的实时渲染确实很方便，用户在书写过程中如果临时想更改之前文字的样式或者修改相应样式中的部分文字，只需要移动光标到相应的位置，修改部分内容即可。

### Typora

Typora是一款本地的、所写即可见的Markdown编辑器。