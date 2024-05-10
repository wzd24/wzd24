---
title: 框架及中间件
description: 服务端开发指南
---

# 游戏设置 开发指南

本文介绍了 游戏设置 导入及加载机制并提供有关开发方法的指南。


## 简介

游戏设置 用于导入、加载、管理游戏的配置数据，。

项目使用 Scorpio 提供的管道模型处理客户端的请求，请求处理程序的核心为 GameHandler 类，项目提供了多种定义 GameHander 的方法，后面将介绍模型的工作方式及如何开发一个请求接口处理程序。

## 配置实体定义


### `GameSettingBase`

```GameSettingBase``` 类是所有配置数据实体的基类，如：

``` cs
    /// <summary>
    /// 头像框配置
    /// </summary>
    [Serializable]
    public class HeadFrameSetting : GameSettingBase
    {
        /// <summary>
        /// 头像框名称
        /// </summary>
        [Header("Name"), ColumnType("VARCHAR(50)")]
        public string Name { get; set; }

        /// <summary>
        /// 描述
        /// </summary>
        [Header("Des"), ColumnType("VARCHAR(200)")]
        public string Des { get; set; }

        /// <summary>
        /// 有效时长
        /// </summary>
        [Header("Duration"), ColumnType("INT(11)")]
        public int Duration { get; set; }

        [Header("ResourcesId"), ColumnType("VARCHAR(200)")]
        public string ResourcesId { get; set; }

        [Header("AnimationName"), ColumnType("VARCHAR(200)")]
        public string AnimationName { get; set; }
    }

```

### `HeaderAttribute`

```HeaderAttribute``` 用于指定从 Excel 导入的列头。如：

![Excel 截图](/assets/img/image7.png){: .img-thumbnail }

```cs 
        /// <summary>
        /// 头像框名称
        /// </summary>
        [Header("Name"/*对应于上图 Excel中的 B 列第 2 行 的 Name*/), ColumnType("VARCHAR(50)")]
        public string Name { get; set; }

```

```HeaderAttribute``` 类定义如下：

```cs
  public class HeaderAttribute : Attribute
  {
      /// <summary>
      /// 导入列名
      /// </summary>
      public string Name { get; set; }

      /// <summary>
      /// 是否需要分析及转换数据，用于将字符串中的 # 转换为 , 号。如： [[1#30110502]] 转换为 [[1,30110502]]
      /// </summary>
      public bool IsAnalysis { get; set; }

      /// <summary>
      /// 暂时没有作用
      /// </summary>
      public bool IsReplace { get; set; }

      public HeaderAttribute(string name, bool isAnalysis = false)
      {
          Name = name;
          IsAnalysis = isAnalysis;
      }
  }
```


### `ColumnTypeAttribute`

`ColumnTypeAttribute` 用于定义生成的数据表字段的数据类型。如：

```cs 
        /// <summary>
        /// 头像框名称
        /// </summary>
        [Header("Name"), ColumnType("VARCHAR(50)")/*生成的数据库字段类型为 "VARCHAR(50)"*/]
        public string Name { get; set; }

```


```ColumnTypeAttribute``` 类定义如下：

```cs
   public class ColumnTypeAttribute : Attribute
   {
       /// <summary>
       /// 数据库字段数据类型
       /// </summary>
       public string DbType { get; set; }

       /// <summary>
       /// 该字段是否为主键字段
       /// </summary>
       public bool IsPrimaryKey { get; set; }

       public ColumnTypeAttribute(string typeStr, bool isPrimaryKey = false)
       {
           IsPrimaryKey = isPrimaryKey;
           DbType = typeStr;
       }
   }
```

## 配置导刷工具

配置导刷工具用于根据策划配置的 Excel 文档自动生成 配置实体、增量配置更新 SQL 语句及直接导入配置到数据库中。
工具项目为 tools 模块的 Sailina.Tang.SettingImport.WindowsForms 项目，使用前需修改 `appsettings.json` 中的配置为当前环境实际配置。

```json
{
  "ConnectionStrings": {
    "Game_Config": "server=localhost;database={0};uid=root;pwd=1q2w3E*;CharSet=utf8;",//数据库连接。
    "DefaultDataName": "tang_config"//默认配置数据库名称，该数据库必须预先存在，如不存在，请提前创建数据库，启动时替换上面数据库连接中的{0}
  },
  "SqlTextPath": "D:\\SqlTxt",//生成的配置实体类代码及数据库脚本的输出路径。
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}

```
工具启动界面如下：

![alt text](/assets/img/image8.png){: .img-thumbnail }

首先请点击 “选择文件路径” 按钮选择Excel配置文档所在的路径，如：

![alt text](/assets/img/image9.png){: .img-thumbnail }

选择路径后，工具会加载所有可以识别的 Excel文档，如下列表框中所示：

![alt text](/assets/img/image10.png){: .img-thumbnail }

第二步，点击左边列表框中的需要导刷的配置的文件名以加入中间的导刷列表框，如果需要全部导刷，可以点击 全选/取消全选 按钮加入或移除所有配置，如：

![alt text](/assets/img/image12.png){: .img-thumbnail }

第三步，从 选择数据库 下拉列表框选择需要导刷的数据库，如果需要，可以在 新建数据库 按钮左边输入新数据库名并点击 新建数据库 按钮以创建新的空数据库，新的数据库将创建上一步加入的表定义，如：

![alt text](/assets/img/image11.png){: .img-thumbnail }

第四步，点击生成Model 按钮生成 配置实体类文件，类文件放置在 appsettings.json 中指定的路径中，（如果已经生成实体，请直接进入下一步）如：

![alt text](/assets/img/image13.png){: .img-thumbnail }

第五步，点击 生成脚本 按钮，工具将根据 Excel 文档及数据库中配置差异生成增量导入配置数据的SQL语句(`不包含创建数据表的语句`)，如：

``` sql
Update BadWordsSetting Set `ShieldWord`='六四運\動',`Id`=11373 Where Id = 11373;
Update BadWordsSetting Set `ShieldWord`='胡錦\濤',`Id`=11494 Where Id = 11494;
Update BadWordsSetting Set `ShieldWord`='學生運\動',`Id`=11498 Where Id = 11498;

INSERT INTO BadWordsSetting (`ShieldWord`,`Id`) VALUES ('特双',23768);
INSERT INTO BadWordsSetting (`ShieldWord`,`Id`) VALUES ('特波',23769);
INSERT INTO BadWordsSetting (`ShieldWord`,`Id`) VALUES ('特尾',23770);
INSERT INTO BadWordsSetting (`ShieldWord`,`Id`) VALUES ('中特',23771);
```
第六步，点击 导入数据 可将配置导入到数据库中。

![alt text](/assets/img/image14.png){: .img-thumbnail }

