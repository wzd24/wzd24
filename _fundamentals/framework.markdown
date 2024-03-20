---
title: 框架及中间件
description: 服务端开发指南
---

# 框架及中间件

本文概述了开发《长安一条街》项目所需掌握的框架、中间件及开发技术。

## .NET 6 

本项目基于.NET 6 框架开发，团队所有成员应精通.NET 6。详情请参阅[开发文档](https://learn.microsoft.com/zh-cn/dotnet/)

## Scorpio 框架

本项目依赖Scorpio项目来实现 自动注册、服务发现、模块加载、配置加载等功能，团队所有成员应能熟练使用Scorpio项目的各项功能。详情请参阅[开发文档](https://scorpio.work/)

## Orleans 3.X

本项目基于 Orleans 3.6.1，并依赖 Orleans 提供的分布式、Actor、持久化、序列化等能力，团队所有成员应能熟练掌握 Orleans 3.X（7.X因为完全重构过，大部分功能无法兼容，请不要升级）。详情请参阅[开发文档](https://learn.microsoft.com/zh-cn/dotnet/orleans/)

## RabbitMQ

本项目基于 RabbitMQ 实现数据异步持久化功能，负责持久化功能的成员应熟练掌握 RabbitMQ及RabbitMQ的.NET版客户端SDK。详情请参阅[开发文档](https://www.rabbitmq.com/docs/documentation)

## Dapper

本项目数据库访问使用Dapper库提供的API，团队成员应熟练掌握Dapper。详情请参阅[开发文档](https://dapperlib.github.io/Dapper/)

## Quartz.NET

本项目的计划任务、后台任务使用Quartz.NET实现，团队成员应熟练掌握。详情请参阅[开发文档](https://www.quartz-scheduler.net/)
