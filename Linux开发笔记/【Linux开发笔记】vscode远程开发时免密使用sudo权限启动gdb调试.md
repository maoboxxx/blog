# 1 前言
我们在Linux程序的开发过程中经常会使用vscode的远程开发功能，也就是在本地使用vscode连接远端的一台Linux主机并进行代码编程。

但是在使用vscode进行远程gdb调试的时候，有些功能模块的正常运行或者程序内部调用shell的操作需要使用sudo权限。

![sudo](https://img-blog.csdnimg.cn/4b28d50d5c254b028e919995e9e019ea.png#pic_center)

# 2 配置gdb脚本
## 2.1 新建gdb脚本
在远端Linux主机任意目录下新建一个名为gdb的文本文件，我是在当前工程vscode的工作区${workspaceFolder}目录下创建的，任何目录都可以，为了以后开发方便也可以在当前用户的home目录下创建。
## 2.2 在gdb脚本中编辑
通过文本编辑器打开刚才创建的文件gdb，并在文件中输入以下命令：
```shell
sudo /usr/bin/gdb "$@"
```
该命令中的“/usr/bin/gdb”为远端Linux主机上实际的gdb调试器路径，编辑完成后保存并关闭。

## 2.3 更改gdb脚本的权限
使用chmod命令将gdb脚本文件权限修改为当前用户可执行，我这里简单粗暴直接把权限改为了777。
```shell
chmod 777 gdb
```
# 3 配置launch.json
打开vscode当前工作区下.vscode中的launch.json，并添加如下配置：
```json
"externalConsole": false,
"MIMode": "gdb",
"miDebuggerPath": "${workspaceFolder}/gdb", // gdb脚本的路径
```

# 4 配置sudo权限为免密
进入到/etc/sudoers.d目录下，
```shell
cd /etc/sudoers.d
```
在sudoers.d目录下新建一个名为“sudo_gdb_for_vscode”（文件名随意）的文本文件。
```shell
sudo touch sudo_gdb_for_vscode
```
使用sudo权限打开sudo_gdb_for_vscode进行编辑，在文件中写入以下配置：
```shell
%admin ALL=(ALL) NOPASSWD:ALL
```
保存文件并退出。

# 5 测试
## 5.1 测试sudo免密
配置完成后，新开一个终端，这个时候使用sudo权限执行任意命令，如果没有再提示输入管理员密码，则表示配置sudo权限免密成功了。

## 5.2 测试vscode远程调试
再回到本地vscode中进行远程调试，这个时候可以发现程序中需要使用sudo权限的功能模块或者程序内部使用sudo权限的shell操作都能够正常在远端Linux主机中使用sudo权限运行了。

![vscode](https://img-blog.csdnimg.cn/6cc84438d42b499f9fc8a2aac2188ffc.jpeg#pic_center)

----

本文原创发布于公众号***Linux未来工程师***。