关于这个博客
============

这是第一次写出来的博客，利用github加readthedocs制作的高颜值博客

1. 利用git系统托管

2. 利用readthedocs对接github实现公网访问

3. 写作时利用Typora以Markdown语法写作，再利用pandoc将.md文件转化为.rst文件

      转化语句：

      .. code:: text

         pandoc -V mainfont="SimSun" -f markdown -t rst hello.md -o hello.rst
