---
title: 持久化服部署
---



# 游戏持久化服部署步骤

## 部署.NET 6.0

请参考微软官方部署文档部署.NET 6.0。
[在 Ubuntu 20.04 上安装 .NET SDK 或 .NET 运行时](https://learn.microsoft.com/zh-cn/dotnet/core/install/linux-ubuntu-2004)

## 修改及上传配置。

### 修改游戏服配置
打开 tang 解决方案的 Sailina.Tang.Persistence.Host 项目下的appsettings.json文件，将其中的数据库链接、redis链接及RabbitMQ链接修改为您的服务器地址：
![配置图片](/assets/img/20240205104541.png){: .img-thumbnail }

修改 ```Server:Env``` 配置为```prd```：
![alt text](/assets/img/image4.png){: .img-thumbnail }
### 上传配置

将上述两个文件上传到游戏持久化服服务器的/etc/tang/目录下。如：
![alt text](/assets/img/image5.png){: .img-thumbnail }

## 构建并上传DEB部署包

> 注意：以下步骤请全部在Ubuntu 20.04环境下操作，亲情推荐WSL，😁

- 执行 build-persistence.sh 脚本开始构建deb包。
- 将构建好的 tang-game-persistence.deb 包上传到游戏持久化服。

## 部署deb包并启动服务。

- 执行 ```sudo dpkg -i tang-game-persistence.deb``` 部署deb包。
- 执行 ```sudo systemctl start persistence``` 启动服务。
