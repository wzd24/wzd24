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

## 使用配置

本项目实现了一个完整的框架用于注入，读取，同步游戏配置数据。以下为框架的核心类及使用方式。

### `IGameSettingDefinitionProvider`

`IGameSettingDefinitionProvider` 接口用于注入配置，配置实体需要注入到框架后方可被配置框架识别并管理。通过实现 `IGameSettingDefinitionProvider` 接口并在接口方法中将配置注入到框架中，参见如下:

``` cs
 public class GameSettingDefinitionProvider : IGameSettingDefinitionProvider
 {
     public void Define(IGameSettingDefinitionContext context)
     {
         context.AddGlobalGeneral<AttributeSetting>("attribute");
         context.AddGlobalGeneral<NewFunctionOnSetting>("newFunctionOn");
         context.AddGlobalGeneral<GuideSetting>("guide");
         context.AddGlobalGeneral<EventPlotSetting>("eventPlot");
         context.AddGlobalGeneral<NPCSetting>("npc");
         context.AddGlobalGeneral<GameConfigSetting>("gameConfig", settingCatalog: SettingCatalog.Host);
         context.AddGlobalGeneral<MapTemplateSetting>("mapTemplate", false);
     }
 }

 //AddGlobalGeneral 方法定义：
 public static IGameSettingDefinitionContext AddGlobalGeneral<TValue>(
    this IGameSettingDefinitionContext context,
    string shortName, //配置短名称，用于和客户端同步配置时作为配置标识键。
    bool syncToClient = true, //配置是否同步到客户端，默认为：true。
    SettingCatalog settingCatalog = SettingCatalog.App, //配置同步时机，Host为客户端启动时同步，App为玩家登录后同步，Full为两个阶段都同步。
    IEnumerable<TValue> defaultValue = null//配置默认数据，当数据库中对应的数据表不存在或表为空时，配置框架会返回这个默认值。
    ) where TValue : GameSettingBase
{

}
```
> 注：框架会自动发现并注入所有实现了 `IGameSettingDefinitionProvider` 的类。

### `IGameSettingManager`

`IGameSettingManager` 接口用于读取配置数据，通过在需要读取数据的类中注入 `IGameSettingManager` 接口，便可通过接口的 `GetGlobalGameSettingAsync` 方法获取配置的值。
如：

``` cs

public class Avatar
{
    private readonly IGameSettingManager _gameSettingManager;

    public Avatar(IGameSettingManager gameSettingManager){
        _gameSettingManager = gameSettingManager;
    }

    public void Example(){
        var flagSettings = (await _gameSettingManager.GetAllAsync<GuildFlagSetting>());//读取 GuildFlagSetting 配置的所有数据。
        var flagSetting = (await _gameSettingManager.GetAsync<GuildFlagSetting>(12));//读取 GuildFlagSetting 配置ID 为 12 的数据。
    }
}
```

> 关于框架的实现方案，感兴趣的同学可以查看相应的实现代码，本文档不展开来讲。
