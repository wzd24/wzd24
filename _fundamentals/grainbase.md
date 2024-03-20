---
title: Grain基类
description: 服务端开发指南
---

# Grain基类

## 简介

项目定义了 GrainBase 用于提供属性化 State 、子系统及部分通用方法。

## IGrainBase

``` cs
/// <summary>
/// 
/// </summary>
public interface IGrainBase : IGrain
{
    /// <summary>
    /// 
    /// </summary>
    /// <returns></returns>
    [OneWay]
    ValueTask ReloadAsync();

    /// <summary>
    /// 
    /// </summary>
    /// <param name="stateName"></param>
    /// <returns></returns>
    ValueTask<object> GetStateDataAsync(string stateName);
    /// <summary>
    /// 
    /// </summary>
    /// <returns></returns>
    ValueTask<object> GetStateDataAsync();

    /// <summary>
    /// 
    /// </summary>
    /// <returns></returns>
    [OneWay]
    ValueTask WriteStateAsync();

}
```