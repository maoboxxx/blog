## 1.概述
我们一般搭建Ubuntu开发环境都是采用VMware或者VirtualBox的虚拟机安装Ubuntu的方案，但是这样的方案会有几个弊端：

>  1. 安装、启动慢；
>  2. 使用图形桌面时卡顿、鼠标不跟手、打字有延迟；
>  3. 磁盘空间占用比较大；
>  4. 内存资源占用比较大；
>  5. 虚拟机网络的配置复杂；
>  6. 与物理机以及外部进行文件或数据交互比较繁琐；
>  7. 虚拟机本身比较容易出bug。

再或者就是安装Windows+Ubuntu的双系统，这样的方式在日常的开发中也有不少的弊端，同时只能运行一个系统，双系统的方案没办法做到两个系统同时使用。

诸如此类的一些弊端，那在Windows上有没有更加轻便的Ubuntu环境呢？

有的

如果没有特别的图形界面开发需求的话，可以采用Windows自身提供的WSL方案进行Ubuntu的环境搭建。**WSL**全称**Windows Subsystem for Linux**（Windows上的Linux子系统），这套设施是微软推出的，为开发者们提供了很大的便利。（**注：WSL只在win10的较新版本之后才支持**）

通过WSL将Ubuntu成功安装之后，可以直接在Windows中打开Linux命令行终端，可以直接在Windows系统中挂载Ubuntu的文件系统并对其进行操作，可以直接通过Windows下安装的VSCode打开Linux的工作区进行开发和调试。

WSL的方案具有以下的优点：

>  1. 磁盘空间占用小；
>  2. 内存资源占用小；
>  3. 可以直接在Windows中打开Linux终端；
>  4. 可以直接在Windows资源管理器中挂载Linux文件系统并对其进行操作；
>  5. Windows下的VSCode可以打开Linux的工作区进行开发和调试；
>  6. Linux的网络配置与Windows物理机的配置完全同步；
>  7. 稳定性和兼容性较好。
## 2.安装WSL
## 2.1 启用适用于Linux的Windows子系统
在**控制面板->程序->程序和功能**页面左侧，打开**启用或关闭Windows功能**，勾选**适用于Linux的Windows子系统**。
![启用适用于Linux的Windows子系统](https://img-blog.csdnimg.cn/4ca0a6f24e6f48129974ed9ab59d112d.png#pic_center)
## 2.2 安装Ubuntu
这一步也是非常的简单，直接在Microsoft Store中搜索Ubuntu，选择一个合适的版本直接安装就OK了。
![Microsoft Store中安装Ubuntu](https://img-blog.csdnimg.cn/2f1656303e274b07a4df72f53747cd7a.png#pic_center)
安装完成后，可以在Windows的开始菜单中看到已经安装的Ubuntu，
![已经安装的Ubuntu](https://img-blog.csdnimg.cn/c76726177fbd43af8158f11febd9a1de.png#pic_center)
点击运行，便打开了一个Linux的命令行终端，对其进行一些初始化配置，便可以使用了。
## 2.3 挂载Ubuntu文件系统
Ubuntu安装完成后，为了方便在Windows中对其文件系统进行访问，我们可以将Ubuntu的文件系统挂载到Windows下。
Win+R打开运行，执行如下指令。

```powershell
\\wsl$
```
![执行\\wsl$](https://img-blog.csdnimg.cn/846806ecb89e4f728a5c5b65c9abceff.png#pic_center)
然后就可以看到Ubuntu的文件系统目录，
![Ubuntu目录](https://img-blog.csdnimg.cn/37dd8e6d826a4cda9b69bac3552f2c1e.png#pic_center)
右键选择**映射网络驱动器**，将Ubuntu的文件系统目录挂载到Windows资源管理器中，
![映射网络驱动器](https://img-blog.csdnimg.cn/083b7e3a5a114b3680b07cb148c02bf4.png#pic_center)
映射成功后，便可以在**我的电脑**中看到Ubuntu的目录（**注：前提是WSL已经启动**），
![Ubuntu目录挂载成功](https://img-blog.csdnimg.cn/b5ad0ab7e01847e2a0e43eb38ef5db0a.png#pic_center)
## 3 VSCode连接WSL
在Windows中打开VSCode，并在**扩展**中搜索“remote-”，找到WSL并进行安装。
![VSCode中安装WSL](https://img-blog.csdnimg.cn/92a4369f7099409d9dc895ea80dccd25.png#pic_center)
安装成功后，重启软件，在左侧功能区进入**远程资源管理器**，然后选择**WSL Targets**，列表中就会出现已安装的Ubuntu。
![VSCode远程资源管理器](https://img-blog.csdnimg.cn/28fb352863d84fac8b14d5ead855f364.png#pic_center)
这里就可以连接WSL中安装的Ubuntu，并可以打开Ubuntu中的工作区进行编程了，也可以点击左下角**绿色的远程连接按钮**打开WSL连接。
![远程连接](https://img-blog.csdnimg.cn/7ba91360904247b0934cc5c9dd5e6b07.png#pic_center)
连接成功后可以看到左下角绿色按钮发生了变化，并且在VSCode中打开文件夹，出现的就不再是Windows的资源管理器，而是Linux的文件系统了。
![远程连接成功](https://img-blog.csdnimg.cn/4ecab1ab848340fb8d46e2c9a4f4e098.png#pic_center)
![VSCode中打开Linux文件系统](https://img-blog.csdnimg.cn/514c3be7fa8e48abb6864749b6b451ca.png#pic_center)
