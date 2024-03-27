---
title: 子系统
description: 服务端开发指南
---

# 子系统

## 简介

子系统用于实现类似 门客、情缘、子嗣等玩法逻辑，所有子系统应继承自```SubSystemBase<TGrain, TSubSystem>``` 类型并使用 ```SubSystemAttribute``` 特性标注注入。

子系统在被初始化后拥有和包含它的Grain组件一样的生命周期，并且可以和Grain一样通过注入维护自身的 State 对象。

## SubSystemBase{TGrain, TSubSystem}

``` cs

    public abstract class SubSystemBase<TGrain, TSubSystem> : ISubSystemSet, ISubSystem
        where TSubSystem : SubSystemBase<TGrain, TSubSystem>
        where TGrain : GrainBase
    {
         protected SubSystemBase(IServiceProvider serviceProvider);

        protected virtual void InitPersistentStates(IServiceProvider serviceProvider);

        protected TGrain Grain { get; set; }

        protected IServiceProvider ServiceProvider { get; }

        protected abstract Task OnSetupAsync(CancellationToken arg);

        protected abstract Task OnActivate(CancellationToken arg);

        protected abstract Task OnDeactivateAsync(CancellationToken arg);

    }

```

## 示例

``` cs

    public class AvatarHeros : SubSystemBase<Avatar, AvatarHeros>
    {
        #region State
        [PropertyPersistentState("HeroEntityState", SystemStorages.RelationalListStorage)]
        private IPersistentState<HeroEntityStateV2> HeroEntityPersistentState { get; set; }

        [PropertyPersistentState("HerosDispatchState", SystemStorages.RelationalSingleStorage)]
        private IPersistentState<HerosDispatchEntityV2> HerosDispatchPersistentState { get; set; }

        public HeroEntityStateV2 HeroEntityState => HeroEntityPersistentState.State;
        public HerosDispatchEntityV2 HerosDispatchState => HerosDispatchPersistentState.State;
        #endregion

        #region 字段属性
        private readonly ILogger<AvatarHeros> _logger;
        private readonly IEventBus _eventBus;
        private readonly IRedisCacheClient _redisClient;

        /// <summary>
        /// 已解锁的名士Id集合（非名士模块开发者不可调用）
        /// </summary>
        private List<int> _heroIds = null;
        /// <summary>
        /// 已解锁的名士Id及职业信息（非名士模块开发者不可调用）
        /// </summary>
        private Dictionary<int, int> _heroIdsProfession = null;
        #endregion

        public AvatarHeros(
            IServiceProvider serviceProvider,
            ) : base(serviceProvider)
        {
            _logger = logger;
            _eventBus = eventBus;
            _redisClient = redisClient;
        }

        protected override async Task OnSetupAsync(CancellationToken arg)
        {
        }
        protected override async Task OnActivate(CancellationToken arg)
        {
        }
        protected override async Task OnDeactivateAsync(CancellationToken arg)
        {
        }

        #region 名士信息
        public LoginHerosDto GetLoginHeros()
        {
            var result = new LoginHerosDto() { Heros = GetHeros(), DispatchHeros = GetDispatchHeros() };
            //HeroCaiLiTest();
            return result;
        }
    }

```