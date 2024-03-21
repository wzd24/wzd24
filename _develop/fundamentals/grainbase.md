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

## GrainBase{T}

项目的所有 Grain 类应该继承自 ```GrainBase<T>``` ,该类实现了 ```IGrainBase``` 接口中的通用方法，以及属性化 State 、子系统 的初始化代码。
如：

``` cs
   public interface IAvatar : IGrainBase, Orleans.IGrainWithIntegerKey
   {
       ValueTask Login(string username, string password);
   }

   public class Avatar : GrainBase<Avatar>, IAvatar
   {
        private readonly IPersistentState<AvatarVIPState> _vipState;

        /// <summary>
        /// 属性化 State，在对象初始化时会自动初始化
        /// </summary>
        [PropertyPersistentState("AvatarState", "AvatarStateStorage")]
        public IPersistentState<AvatarState> AvatarState { get; set; }

        /// <summary>
        /// 子系统，在对象初始化时会自动初始化
        /// </summary>

        [SubSystem]
        public AvatarHeros Heros { get; set; }


       public Avatar(IServiceProvider serviceProvider,
            [PersistentState("AvatarVIPState", SystemStorages.RelationalSingleStorage)] 
            IPersistentState<AvatarVIPState> vipState,
        ) : base(serviceProvider)
       {
            _vipState = vipState;
       }

       public ValueTask Login(string username, string password) => throw new NotImplementedException();
   }

```