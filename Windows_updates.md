Windows 补丁安装失败，包括重启计算机都是失败。

应该使用安全模式安装。

其他关闭服务，或者其他方式都不要费劲了。


```
dism /online /add-package /packagepath:c:\xxxxxx
```