
代码放在 linux 侧，办公在 Windows 侧。 linux 侧用于编译。

### vscode + rmate （linux 侧）

这个方式不好。
linux 端 rmate 开启什么文件， vscode 才能打开什么文件， 不直观。

教程

https://www.bbsmax.com/A/n2d9pxB5Dv/



多种方式的对比
https://github.com/chunpu/blog/issues/88


### Visual Studio Code + Remote VSCode Extension

https://snyk.io/test/github/rafaelmaiolla/remote-vscode

一个插件，使用成功过，不喜欢这种使用方式。


### Visual Studio Code + VSCode Remote SSH Editor Extension

https://marketplace.visualstudio.com/items?itemName=seedess.vscode-remote-editor 

没使用成功

command 'remote.editor.connectRemote' not found


### Visual Studio Code + SFTP 

https://zhuanlan.zhihu.com/p/29590357
https://segmentfault.com/a/1190000011844958

遇到问题：
使用 SFTP：Download 之后 只看到下载了部分目录，文件都没下载下来

解决方案：
原来是要在资源管理器中右键手动下载的，不是配置好 sftp.json文件后自动的.


## Visual Studio Code + Remote Workspace

这个挺好用 

不是把文件复制一份到本地 是直接编辑远程的文件

### linux 开启 samba  Windows 来登陆

Visual Studio 对磁盘读写多，我这里没满足要求，使用起来很卡顿。

Visual Studio Code 工作良好。 SourceInsight 工作良好。