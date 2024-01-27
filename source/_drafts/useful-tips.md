## idea

### 启动出现Internal error.Please refer to http://jb.gg/ide/critical-startup-errors

提示 java.net.BindException， 说明idea需要的端口被占用

idea会在6942和6991之间的第一个可用端口上进行绑定，如果无法在该范围内的任何端口上进行绑定，则会引发此异常，造成启动失败



#### 解决方法一

后台关闭占用端口的软件



#### 解决方法二

这通常是由Windows NAT驱动程序（winnat）引起的，停止并重新启动该服务可以解决问题。

使用系统管理员启动CMD

`net stop winnat`

然后启动IDEA，启动成功后，CMD里继续运行下面的命令

`net start winnat`
