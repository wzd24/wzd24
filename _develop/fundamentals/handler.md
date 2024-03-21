---
title: 接口处理程序
description: 服务端开发指南
---

# Handlers 模块开发指南

本文介绍了 Handlers 模块并提供有关开发方法的指南。

## 简介

Handlers 模块用于开发通讯协议无关客户端请求接口处理的功能。

项目使用 Scorpio 提供的管道模型处理客户端的请求，请求处理程序的核心为 GameHandler 类，项目提供了多种定义 GameHander 的方法，后面将介绍模型的工作方式及如何开发一个请求接口处理程序。

## GameHandler

GameHandler 是处理客户端请求的核心程序，GameHandler 类需要添加 `HandlerAttribute` 特性，下面我们将介绍如何开发 GameHandler 程序。
GameHandler 可以通过下面几种方式定义：

### POCO 

你可以直接定义一个包含`Execute`或`ExecuteAsync`方法(方法的返回类型必须为`Task`或`Task<T>`)的类作为 GameHandler，如：

```cs
[Handler("0001")] //0001为接口的编号。
class DemoHandler
{
    private readonly ILogger<DemoHandler> logger;

    public DemoHandler(IServiceProvider serviceProvider, ILogger<DemoHandler> logger) : base(serviceProvider)
    {
        this.logger = logger;
        logger.LogInformation($"{nameof(DemoHandler)} handler created.");
    }

    public Task<object> ExecuteAsync()
    {
        return Task.FromResult<object>("This is first handler.");
    }
}
```

### GameHandlerBase 

通过继承 `GameHandlerBase` 类并包含`Execute`或`ExecuteAsync`方法(方法的返回类型必须为`Task`或`Task<T>`)的类作为 GameHandler，如：

```cs
[Handler("0001")]
class DemoHandler:GameHandlerBase
{
    private readonly ILogger<DemoHandler> logger;

    public DemoHandler(IServiceProvider serviceProvider, ILogger<DemoHandler> logger) : base(serviceProvider)
    {
        this.logger = logger;
        logger.LogInformation($"{nameof(DemoHandler)} handler created.");
    }

    public Task<object> ExecuteAsync()
    {
        return Task.FromResult<object>("This is first handler.");
    }
}
```
### GameHandlerBase{T}

继承 ```GameHandlerBase<T>``` 类并重写`ExecuteAsync`方法的类作为 GameHandler，如：

```cs
[Handler("0003")]
class ArgHandler:GameHandlerBase<RequestPara>
{
    public ArgHandler(IServiceProvider serviceProvider):base(serviceProvider)
    {
        Console.WriteLine("Test");
    }

    public override async Task<IResponseMessage> Execute(RequestPara request)
    {
        return Success($"My name is {request.Name}, I am {request.Age} year(s) old.");
    }

}

class RequestPara{
public string Name{get;set;}
public int Age {get;set;}
}
```

> 注意：因为继承自```GameHandlerBase<T>``` 的GameHandler性能表现最为出色，所以在允许的情况下，请尽量使用该方案实现 GameHandler。


## GameHandler 处理程序参数

在使用不同的 GameHandler 实现方式中，有多种接受客户端发送的请求参数的方式。

### 无请求参数

在不需要客户端发送请求参数时可以通过以下方法定义 GameHandler。

#### POCO

```cs
[Handler("0001")]
class DemoHandler
{
    private readonly ILogger<DemoHandler> logger;

    public DemoHandler(IServiceProvider serviceProvider, ILogger<DemoHandler> logger) : base(serviceProvider)
    {
        this.logger = logger;
        logger.LogInformation($"{nameof(DemoHandler)} handler created.");
    }

    public Task<object> ExecuteAsync()
    {
        return Task.FromResult<object>("This is first handler.");
    }
}
```

#### GameHandlerBase

```cs
[Handler("0001")]
class DemoHandler:GameHandlerBase
{
    private readonly ILogger<DemoHandler> logger;

    public DemoHandler(IServiceProvider serviceProvider, ILogger<DemoHandler> logger) : base(serviceProvider)
    {
        this.logger = logger;
        logger.LogInformation($"{nameof(DemoHandler)} handler created.");
    }

    public Task<object> ExecuteAsync()
    {
        return Task.FromResult<object>("This is first handler.");
    }
}
```

#### GameHandlerBase{T}

> 注意：这种实现方式不支持无参请求。

### 有请求参数

在使用 POCO 或继承自 ```GameHandlerBase``` 方式实现 GameHandler 时，可以通过标量参数表或Dto对象接受请求参数，在使用 ```GameHandlerBase<T>``` 方式实现时，只支持 Dto 对象接受请求参数。

    假设请求参数：`{"name":"张三","age":23}`

```cs
[Handler("0003")]
class ArgHandler:GameHandlerBase
{
    public ArgHandler(IServiceProvider serviceProvider):base(serviceProvider)
    {
        Console.WriteLine("Test");
    }

    public Task<string> Execute(string name,int age)
    {
        return Task.FromResult<string>($"My name is {name}, I am {age} year(s) old.");
    }

}
```

或者

```cs
[Handler("0003")]
class ArgHandler:GameHandlerBase
{
    public ArgHandler(IServiceProvider serviceProvider):base(serviceProvider)
    {
        Console.WriteLine("Test");
    }

    public Task<string> Execute(RequestPara request)
    {
        return Task.FromResult<string>($"My name is {request.Name}, I am {request.Age} year(s) old.");
    }

}

class RequestPara{
public string Name{get;set;}
public int Age {get;set;}
}
```
或者

``` cs
[Handler("0003")]
class ArgHandler:GameHandlerBase<RequestPara>
{
    public ArgHandler(IServiceProvider serviceProvider):base(serviceProvider)
    {
        Console.WriteLine("Test");
    }

    public override async Task<IResponseMessage> Execute(RequestPara request)
    {
        return Success($"My name is {request.Name}, I am {request.Age} year(s) old.");
    }

}

class RequestPara{
public string Name{get;set;}
public int Age {get;set;}
}
```

## GameHandler 处理程序返回值



### 无返回值

在不不需要向客户端返回处理结果的情况下，可以使用以下方法指示框架不向客户端发送返回值： 
- 定义 Execute 方法返回类型为 ```Task```。
- 如果 Execute 方法返回类型为```Task<T>```,可以返回null值。
- 如果 Execute 方法返回类型为```Task<IResponseMessage>```,可以返回null值或者Success(null)。
    如：

``` cs
[Handler("0001")]
class FirstHandler:GameHandlerBase
{
    private readonly ILogger<FirstHandler> logger;

    public FirstHandler(IServiceProvider serviceProvider, ILogger<FirstHandler> logger) : base(serviceProvider)
    {
        this.logger = logger;
        logger.LogInformation($"{nameof(FirstHandler)} handler created.");
    }

    public async Task ExecuteAsync()
    {
        logger.LogInformation("This is first handler.");
    }
}

[Handler("0001")]
class FirstHandler:GameHandlerBase
{
    private readonly ILogger<FirstHandler> logger;

    public FirstHandler(IServiceProvider serviceProvider, ILogger<FirstHandler> logger) : base(serviceProvider)
    {
        this.logger = logger;
        logger.LogInformation($"{nameof(FirstHandler)} handler created.");
    }

    public async Task<object> ExecuteAsync()
    {
        logger.LogInformation("This is first handler.");
        return null;
    }
}

[Handler("0001")]
class FirstHandler:GameHandlerBase
{
    private readonly ILogger<FirstHandler> logger;

    public FirstHandler(IServiceProvider serviceProvider, ILogger<FirstHandler> logger) : base(serviceProvider)
    {
        this.logger = logger;
        logger.LogInformation($"{nameof(FirstHandler)} handler created.");
    }

    public async Task<IResponseMessage> ExecuteAsync()
    {
        logger.LogInformation("This is first handler.");
        return null;//或者 return Success(null);
    }
}
```

### 有返回值

在不不需要向客户端返回处理结果的情况下，可以使用以下方法指示框架向客户端发送返回值： 
- 设置 Execute 方法返回类型为```Task<T>```,可以返回需要发送给客户端的值。
- 设置 Execute 方法返回类型为```Task<IResponseMessage>```,可使用Success(result) 返回发送给客户端的值。

如：

```cs    
[Handler("0001")]
class FirstHandler:GameHandlerBase
{
    private readonly ILogger<FirstHandler> logger;

    public FirstHandler(IServiceProvider serviceProvider, ILogger<FirstHandler> logger) : base(serviceProvider)
    {
        this.logger = logger;
        logger.LogInformation($"{nameof(FirstHandler)} handler created.");
    }

    public async Task<object> ExecuteAsync()
    {
        logger.LogInformation("This is first handler.");
        return "This is first handler.";
    }
}

[Handler("0001")]
class FirstHandler:GameHandlerBase
{
    private readonly ILogger<FirstHandler> logger;

    public FirstHandler(IServiceProvider serviceProvider, ILogger<FirstHandler> logger) : base(serviceProvider)
    {
        this.logger = logger;
        logger.LogInformation($"{nameof(FirstHandler)} handler created.");
    }

    public async Task<IResponseMessage> ExecuteAsync()
    {
        logger.LogInformation("This is first handler.");
            return Success("This is first handler.");
    }
}
```
