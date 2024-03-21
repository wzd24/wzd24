---
title: 启动项目
---

# 启动项目

## 修改配置
### 修改登陆服配置

打开 ucenter 解决方案的 Sailina.UCenter.Host 项目下的appsettings.json文件，将其中的数据库链接修改为您的数据库服务器配置

### 修改游戏服配置

打开 tang 解决方案的 Sailina.Tang.Game.Host 项目下的appsettings.json文件，将其中的数据库链接、redis链接及RabbitMQ链接修改为您的服务器地址：

![配置图片]({{site.baseurl}}/assets/img/20240205104541.png){: .img-thumbnail }


## 启服步骤

### 启动登录服

在ucenter 解决方案的 Sailina.UCenter.Host 项目目录下执行 

``` bash
dotnet run --urls http://localhost:5001
 ```

> 如果需要允许其他设备访问，请将 localhsot 修改为 0.0.0.0

### 启动游戏服

在 tang 解决方案的 Sailina.Tang.Game.Host 项目目录下执行
``` bash
dotnet run --urls http://localhost:5000
```

> 如果需要允许其他设备访问，请将 localhsot 修改为 0.0.0.0
