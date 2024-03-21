---
title: 部署数据库
---

## 部署数据库

### 创建数据库

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
### 初始化数据库

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

### 创建区服数据

游戏的区服配置在 ucenter 数据库的 servers 表中，可以根据字段值添加区服。
也可以导入servers.sql 中定义好的区服数据。
