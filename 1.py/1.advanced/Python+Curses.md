https://docs.python.org/zh-cn/3/howto/index.html
## curses
curses库为基于文本的中段提供了独立于中段的屏幕绘制和键盘处理功能; 这些终端包括VT100, Linux控制台以及各种程序提供的模拟终端.
显示终端支持各种控制代码以执行常见的的操作, 例如移动光标, 滚动屏幕和擦除区域. 不同的终端使用相差很大的代码, 并且往往有自己的差异操作.

虽然字符单元显示终端是一种过时的技术, 但在某些领域中, 能够用它们做花哨的事情仍然很有价值:
- 在不运行X server的小型或嵌入式Unix上;
- 在提供图形支持前, 可能需要运行的工具, 如操作系统安装程序和内核配置程序.
注: Python的Windows版不包括curses 模块, 一个可用的移植版本是UniCurses.
## Python的curses模块
对curses所提供的C函数的一个相当简单的包装器; 如果已经熟悉在C中进行curses变成, 把这些知识转移到Python是非常容易的. 最大的差异在于Python接口通过将不同的C函数比如addstr(), mvaddstr()和 mvwaddstr() 合并成为一个 ==addstr() ==方法让事情变得更简单.
完整指南参见 ncurses的Python库指南章节和 ncurses的C手册页.
## 开始和结束curses应用程序
https://docs.python.org/zh-cn/3/howto/curses.html




