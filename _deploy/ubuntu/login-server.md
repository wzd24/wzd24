---
title: 登录服部署
---

# 登录服部署步骤

1. 部署.NET Core 3.1

    请参考微软官方部署文档部署.NET Core 3.1。
    [在 Ubuntu 20.04 上安装 .NET SDK 或 .NET 运行时](https://learn.microsoft.com/zh-cn/dotnet/core/install/linux-ubuntu-2004)

2. 修改及上传配置。

- 修改登陆服配置

    打开 ucenter 解决方案的 Sailina.UCenter.Host 项目下的appsettings.json文件，将其中的数据库链接修改为您的数据库服务器配置,将`GameServerUrl`配置修改为游戏服集群的任意一个节点地址或者游戏服前端负载均衡地址。
- 修改Redis配置

    打开 ucenter 解决方案的 Sailina.UCenter.Host 项目下的redis.json文件，将其中的redis服务器地址、用户名、密码修改为您的reis服务器配置。
- 上传配置

    将上述两个文件上传到登录服服务器的/etc/ucenter/目录下。如：
    ![alt text](/assets/img/image2.png){: .img-thumbnail }

3. 构建并上传DEB部署包

> 注意：以下步骤请全部在Ubuntu 20.04环境下操作，亲情推荐WSL，😁

- 将user-center-deb.zip解压并将其中的deb目录覆盖ucenter解决方案中的deb目录，
![alt text](/assets/img/image3.png){: .img-thumbnail }
- 执行 build.sh 脚本开始构建deb包。
- 将构建好的 tang-ucenter-server.deb 包上传到登录服。
4. 部署deb包并启动服务。

- 执行 ```sudo dpkg -i tang-ucenter-server.deb``` 部署deb包。
- 执行 ```sudo systemctl start ucenter``` 启动服务。
