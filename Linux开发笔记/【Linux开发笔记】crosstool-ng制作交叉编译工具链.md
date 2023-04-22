# 1 编译/安装crosstool-ng
## 1.1 crosstool-ng简介
crosstool-ng是一个交叉编译工具链生成器。它支持许多体系结构和组件，并具有简单而强大的菜单样式界面。

crosstool-ng官网地址：[https://crosstool-ng.github.io/](https://crosstool-ng.github.io/)

## 1.2 下载crosstool-ng源码
下载地址：[https://crosstool-ng.github.io/download/](https://crosstool-ng.github.io/download/)

## 1.3 解压
```shell
tar -xvf crosstool-ng-1.25.0.tar.xz
```

## 1.4 配置编译/安装参数
### 1.4.1 执行configure
进入解压后的crosstool-ng目录中，

如果需要指定crosstool-ng的安装路径以及修改其他的编译参数，可以先执行查看configure的一些参数配置。
```shell
./configure --help
```
linux中编译各种开源库和工具有一些比较通用的方法，我在[【Linux开发笔记】Linux编译第三方库通用方法](https://blog.csdn.net/qq_37354286/article/details/102932316)文章中也有提到过。

我这里选择默认的配置，直接执行configure。

```shell
./configure
```

### 1.4.2 解决依赖问题
在执行configure的过程中，会检查各种需要的依赖项，下面的列表中是我在configure过程中遇到的依赖问题以及解决方法：
| 问题 | 解决 |
|--|--|
|configure: error: missing required tool: flex  | sudo apt-get install flex |
|configure: error: missing required tool: makeinfo  | sudo apt-get install texinfo |
|configure: error: missing required tool: help2man  | sudo apt-get install help2man |
|configure: error: Required tool not found: GNU awk  | sudo apt-get install gawk |
|configure: error: configure: error: Required tool not found: make  | sudo apt-get install make |
|configure: error: Required tool not found: libtool  | sudo apt-get install libtool<br>sudo apt-get install libtool-bin |
|configure: error: curses library not found  |sudo apt-get install ncurses-dev |
|YACC parser.tab.c  | sudo apt-get install bison |

### 1.4.3 编译/安装
1.编译
```shell
make
```
2.安装
```shell
sudo make install
```
执行
```shell
ct-ng -v
```
正常显示版本信息，则安装成功。

# 2 制作交叉编译工具链
## 2.1 交叉编译准备
新建一个名为arm-linux（作为本次交叉编译的工作目录，名称随意）的目录。

进入crosstool-ng目录下的sample目录，该目录下有各个环境的config模板，如下图所示：

![config模板](https://img-blog.csdnimg.cn/983f251dabf64871b0516d8d738bb486.png)

交叉编译器的命名规则及详细解释可以参考[交叉编译器的命名规则及详细解释](https://blog.csdn.net/LEON1741/article/details/81537529)这篇文章。

根据自己当前的环境选择，我这里用到的是s3c2440al-40，所以选择的config模板是arm-unknown-linux-gnueabi，进入arm-unknown-linux-gnueabi目录中，将该目录下的crosstool.config拷贝到arm-linux目录下，并重命名为.config。

在arm-linux中新建三个目录，

    1.build：作为crosstool-ng的构建目录；
    2.arm-unknown-linux-gnueabi：作为工具链安装目录；
    3.src：作为交叉编译过程中存放依赖包源码的目录。

下载gcc和gdb源码，gnu官网下载太慢，这里推荐[阿里云镜像站](https://developer.aliyun.com/mirror/)下载[gcc](https://mirrors.aliyun.com/gnu/gcc/)和[gdb](https://mirrors.aliyun.com/gnu/gdb/)源码包。

将下载好的gcc和gdb源码包放进src目录下，实际上crosstool-ng在编译过程中会下载依赖项到src目录，也可以将各个依赖项的源码放进src目录下，crosstool-ng找到src中如果有依赖项的本地源码包，那么就直接用本地源码包了。

## 2.2 交叉编译配置
在arm-linux目录下执行
```shell
ct-ng menuconfig
```
终端中出现crosstool-ng的配置菜单，如下图所示：

![crosstool-ng配置菜单](https://img-blog.csdnimg.cn/65445c9d78c648f79bd9d2d640ab27d9.png)

### 2.2.1 Paths and misc options配置
| 选项 | 操作|
|--|--|
|Local tarballs directory|设置为src目录绝对路径|
|Working directory|设置为build目录绝对路径|
|Prefix directory|设置为工具链安装目录绝对路径|

注意：Prefix directory设置为工具链安装目录绝对路径，但是该路径不能和.config为同一个目录，我之前就是配置了工具链安装目录和.config为同一目录，结果在执行编译的时候，总是会报下面的错误，花费了不少时间在上面。
```shell
[00:03] / bzip2: Can't open input file .config: No such file or directory.
[ERROR]  
[ERROR]  >>
[ERROR]  >>  Build failed in step '(top-level)'
[ERROR]  >>
[ERROR]  >>  Error happened in: CT_InstallConfigurationFile[scripts/functions@2536]
[ERROR]  >>        called from: main[scripts/crosstool-NG.sh@634]
[ERROR]  >>
[ERROR]  >>  For more info on this error, look at the file: 'build.log'
[ERROR]  >>  There is a list of known issues, some with workarounds, in:
[ERROR]  >>      https://crosstool-ng.github.io/docs/known-issues/
[ERROR]  >>
[ERROR]  >>  If you feel this is a bug in crosstool-NG, report it at:
[ERROR]  >>      https://github.com/crosstool-ng/crosstool-ng/issues/
[ERROR]  >>
[ERROR]  >>  Make sure your report includes all the information pertinent to this issue.
[ERROR]  >>  Read the bug reporting guidelines here:
[ERROR]  >>      http://crosstool-ng.github.io/support/
[ERROR]   
[ERROR]  (elapsed: 0:01.50)
```

### 2.2.2 Target options配置
| 选项 | 操作|
|--|--|
|Target Architecture|设置为arm|
|Floating point|根据cpu本身的浮点运算规格选择，我这里选择的是no fpu|
|Default instruction set mode|设置为arm|
|Emit assembly for CPU|这一项设置为arm9tdmi，可以通过芯片手册查到。![ARM9TDMI](https://img-blog.csdnimg.cn/51f12ee85a744f34879101b7a4eb987c.png)|

### 2.2.3 Operating System配置
| 选项 | 操作|
|--|--|
|Target OS|设置为linux|
|Version of linux|设置目标linux内核版本|

### 2.2.4 C compiler配置
| 选项 | 操作|
|--|--|
|Version of gcc|设置目标gcc版本|

### 2.2.5 Debug facilities配置
| 选项 | 操作|
|--|--|
|gdb|按Y键勾选gdb|
|Version of gdb|设置目标gdb版本|

### 2.2.6 Companion tools配置
这一项菜单里面有一些配套工具，比如bison、make等，根据自己需求选择。

## 2.3 构建工具链
在arm-linux目录（即.config所在目录）下执行
```shell
ct-ng build
```
crosstool-ng开始构建工具链，如果遇到以下报错：
```shell
[EXTRA]    Retrieving 'xxx-x.x.xx'
[ERROR]    xxx: download failed
```
比如我遇到的zlib：
```shell
[EXTRA]    Retrieving 'zlib-1.2.12'
[ERROR]    zlib: download failed
```
手动下载该依赖项的源码并放到src目录下。

将手动下载的依赖项源码包在放置了在src中之后，再次执行
```shell
ct-ng build
```
如果出现了以下错误：
```shell
make: "build" is up to date
```
执行一下
```shell
touch .config
```
该命令用于更新一下.config文件的时间戳，然后再次执行
```shell
ct-ng build
```
等待编译完成就大功告成了，硬件性能和工具链编译配置的不同花费的时间也不一样，我这边大概花费一个小时，进入arm-unknown-linux-gnueabi目录可以看到制作好的工具链。