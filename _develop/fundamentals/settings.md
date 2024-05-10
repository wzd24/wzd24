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



### GameSettingBase

GameSettingBase 类是所有配置数据实体的基类，如：

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