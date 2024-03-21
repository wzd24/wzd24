---
title: Scorpio - Documentation
---

# 生产环境部署方案

## 系统部署架构图

![alt text](images/image1.png)

## 服务器需求

[服务器需求列表](/assets/documents/长安一条街服务器需求gcp.xlsx)

## 部署步骤

### 数据库、Redis及RabbitMQ部署
  略
### 部署数据库
1. 创建数据库

   分别创建 配置库、账号库、玩家库、聊天库及QuartZ库。
   脚本如下：
   ```sql
   CREATE DATABASE `tang_config` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';
   ```
   ```sql
   CREATE DATABASE `ucenter` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';
   ```
   ```sql
   CREATE DATABASE `tang_game` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';
   ```
   ```sql
   CREATE DATABASE `tang_chat` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';
   ```
   ```sql
   CREATE DATABASE `tang_quartz` CHARACTER SET 'utf8' COLLATE 'utf8_general_ci';
   ```
2. 初始化数据库
    在上述的数据库中分别执行 database 目录下对应的数据库脚本文件。如：

    ```sql
    use tang_config
    source tang_config.sql;
    ```
    ```sql
    use ucenter
    source ucenter.sql;
    ```
    ```sql
    use tang_game
    source tang_game.sql;
    ```
    ```sql
    use tang_chat
    source tang_chat.sql;
    ```
    ```sql
    use tang_quartz
    source tang_quartz.sql;
    ```
3. 创建区服数据
    游戏的区服配置在 ucenter 数据库的 servers 表中，可以根据字段值添加区服。
    也可以导入servers.sql 中定义好的区服数据。

### 登录服部署步骤

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
    ![alt text](images/image2.png)

3. 构建并上传DEB部署包

> 注意：以下步骤请全部在Ubuntu 20.04环境下操作，亲情推荐WSL，😁

- 将user-center-deb.zip解压并将其中的deb目录覆盖ucenter解决方案中的deb目录，
![alt text](images/image3.png)
- 执行 build.sh 脚本开始构建deb包。
- 将构建好的 tang-ucenter-server.deb 包上传到登录服。
4. 部署deb包并启动服务。

- 执行 ```sudo dpkg -i tang-ucenter-server.deb``` 部署deb包。
- 执行 ```sudo systemctl start ucenter``` 启动服务。

### 游戏服服部署步骤

1. 部署.NET 6.0

    请参考微软官方部署文档部署.NET 6.0。
    [在 Ubuntu 20.04 上安装 .NET SDK 或 .NET 运行时](https://learn.microsoft.com/zh-cn/dotnet/core/install/linux-ubuntu-2004)

2. 修改及上传配置。

- 修改游戏服配置
    打开 tang 解决方案的 Sailina.Tang.Game.Host 项目下的appsettings.json文件，将其中的数据库链接、redis链接及RabbitMQ链接修改为您的服务器地址：
![配置图片](images/20240205104541.png "")
    - 修改 ```Server:Env``` 配置为```prd```：
    ![alt text](images/image4.png)
    - 修改 ```RabbitMQ:Enabled``` 配置为 ```true```
    - 修改 ```PersistentDelay:Enabled``` 配置为 ```true```
- 上传配置

    将上述两个文件上传到游戏服服务器的/etc/tang/目录下。如：
    ![alt text](images/image5.png)

3. 构建并上传DEB部署包

> 注意：以下步骤请全部在Ubuntu 20.04环境下操作，亲情推荐WSL，😁

- 将game-server-deb.zip解压并将其中的deb目录覆盖tang解决方案中的deb目录，
![alt text](images/image6.png)
- 执行 build.sh 脚本开始构建deb包。
- 将构建好的 tang-game-server.deb 包上传到游戏服。
4. 部署deb包并启动服务。

- 执行 ```sudo dpkg -i tang-game-server.deb``` 部署deb包。
- 执行 ```sudo systemctl start tang``` 启动服务。

### 游戏持久化服部署步骤

1. 部署.NET 6.0

    请参考微软官方部署文档部署.NET 6.0。
    [在 Ubuntu 20.04 上安装 .NET SDK 或 .NET 运行时](https://learn.microsoft.com/zh-cn/dotnet/core/install/linux-ubuntu-2004)

2. 修改及上传配置。

- 修改游戏服配置
    打开 tang 解决方案的 Sailina.Tang.Persistence.Host 项目下的appsettings.json文件，将其中的数据库链接、redis链接及RabbitMQ链接修改为您的服务器地址：
![配置图片](images/20240205104541.png "")
    - 修改 ```Server:Env``` 配置为```prd```：
    ![alt text](images/image4.png)
- 上传配置

    将上述两个文件上传到游戏持久化服服务器的/etc/tang/目录下。如：
    ![alt text](images/image5.png)

3. 构建并上传DEB部署包

> 注意：以下步骤请全部在Ubuntu 20.04环境下操作，亲情推荐WSL，😁

- 执行 build-persistence.sh 脚本开始构建deb包。
- 将构建好的 tang-game-persistence.deb 包上传到游戏持久化服。
4. 部署deb包并启动服务。

- 执行 ```sudo dpkg -i tang-game-persistence.deb``` 部署deb包。
- 执行 ```sudo systemctl start persistence``` 启动服务。

## 部署前端负载均衡

推荐部署前端负载均衡，项目组可自行选择负载均衡方案。