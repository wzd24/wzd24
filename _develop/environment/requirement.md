---
title: 系统需求
---

# 系统需求
本文概述了了解开发《长安一条街》项目所需系统环境及开发工具。

## .NET

### .NET 6.0

游戏服项目基于 .NET 6.0 开发，所以负责开发游戏服的人员的设备必须部署.NET 6.0。

### .NET Core 3.1

登录服项目基于 .NET Core 3.1 开发，所以负责开发游戏服的人员的设备必须部署.NET Core 3.1。

### 部署 .NET

请参考微软官方部署文档部署.NET。
[在 Windows 上安装 .NET](https://learn.microsoft.com/zh-cn/dotnet/core/install/windows)


## MySQL

项目目前使用 MySQL 存储游戏相关配置及游戏数据。

## Redis 5+

项目目前使用 Redis 作为游戏热数据缓存及排行榜数据保存方案。

## RabbitMQ (可选)

项目目前使用 RabbitMQ 作为游戏异步数据存储的消息队列，当开启游戏服的数据异步保存时，游戏服会将数据通过 RabbitMQ 推送到持久化服务，最后由持久化服务将数据写入数据库。

## 开发工具

开发人员可以使用各种支持 .NET 开发的开发工具开发项目，但为统一及利用项目配置的编码规范，推荐使用 Visual Studio 2022 或者 VS Code 工具开发。

部署方式请参考微软官网：

[Visual Studio: 面向软件开发人员和 Teams 的 IDE 和代码编辑器](https://visualstudio.microsoft.com/zh-hans/)

[Visual Studio Code - Code Editing. Redefined](https://code.visualstudio.com/)