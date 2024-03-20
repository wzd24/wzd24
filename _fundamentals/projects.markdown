---
title: 项目列表
---
# 项目结构

## handlers
客户端通信接口处理模块

### Sailina.Tang.Player.Handlers
客户端通信接口请求处理项目，用于处理客户端发送的请求。
- 接口规范请参阅文档：[接口规范](/assets/documents/接口规范.pdf)。
- 该项目开发详情请参阅： [Handler](Handler.md)

## hosts
服务启动宿主模块，该项目只作为启动宿主，不包含业务逻辑。
### Sailina.Tang.Game.Host

游戏服启动程序，用于启动游戏服。

### Sailina.Tang.Management.Host

游戏管理命令行工具，用于执行游戏插件，处理游戏数据问题或调整游戏数据。

### Sailina.Tang.Persistence.Host

游戏数据持久化服务程序，用于异步持久化玩家数据到玩家数据库。需要RabbitMQ支持。

## infrastructure

基础代码模块

### Sailina.Tang.Abstract

基础代码接口定义项目，本项目定义部分基础功能的接口、抽象类及数据结构。

- 该项目开发详情请参阅： [Abstract](Abstract.md)

### Sailina.Tang

基础代码接口实现项目，本项目实现部分基础功能的接口的逻辑。

### Sailina.Tang.Quartz


Quartz 集成项目，用于执行游戏中的部分定时任务。

### Sailina.Tang.Settings

游戏配置数据定义项目，用于定义游戏中的配置参数。

## management

游戏管理模块，用于处理GM平台发送的管理指令。

### Sailina.Tang.Management.Abstract

游戏管理模块接口定义项目，用于定义游戏管理服务的接口及数据结构。

### Sailina.Tang.Management

游戏管理模块接口实现项目，用于实现游戏管理服务的接口逻辑。

### Sailina.Tang.Management.Web

游戏管理模块的ASP.NET Core 实现，可以部署在游戏服中或独立部署，目前部署在游戏服服务上。如需独立部署需另外开发ASP.NET Core 宿主程序。

### modules/player

游戏服务端业务逻辑模块

### Sailina.Tang.Player

游戏服接口项目，用于定义游戏Orleans集群对外服务的接口及数据结构。

### Sailina.Tang.Player.Entities

游戏服数据实体定义项目，用于定义游戏实体及状态的数据结构。

### Sailina.Tang.Player.Implement

游戏服业务逻辑实现项目，本项目实现了游戏服的所有业务逻辑。


## plugins

插件模块

### Sailina.Tang.Plugins

游戏GM插件项目，本项目用于开发游戏服务端GM插件，本项目实现的逻辑可以由 Sailina.Tang.Management.Host 命令行项目调用。

## tools

工具模块

### Sailina.Tang.SettingImport.WindowsForms

策划配置Excel文档配置参数导入项目，用于将策划创建的游戏配置参数从Excel导入到MySQL数据库中。
