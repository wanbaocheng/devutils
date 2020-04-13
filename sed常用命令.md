[sed](https://www.gnu.org/software/sed/)（意为流编辑器，源自英语“stream editor”的缩写）是一个使用简单紧凑的编程语言来解析和转换文本Unix实用程序。
它可以对文本文件中的数据进行选取、替换、删除、新增等操作。

+ 安装
  + 可参见(https://www.twle.cn/c/yufei/sed/sed-basic-environment.html)
  + [稳定版的源码下载地址](http://ftp.gnu.org/gnu/sed/)

+ [文档](https://www.gnu.org/software/sed/manual/)
  + [在线文档](https://www.gnu.org/software/sed/manual/sed.html)
  + [pdf](https://www.gnu.org/software/sed/manual/sed.pdf)

+ 示例
```shell script
$ sed -i "/\/MathJax.js/c<script src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" xml:space="preserve" type="text/javascript"></script>" vlfeat的某个doc页面.html
```
