---
title: ASP.NET Core 中的日志记录
author: tdykstra
description: 了解 ASP.NET Core 中的记录框架。 发现内置日志记录提供程序，并详细了解常见第三方提供程序。
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/14/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 0908cc6eeaaba4006a0029ef86d3e47dcf3f9a28
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341740"
---
# <a name="logging-in-aspnet-core"></a>ASP.NET Core 中的日志记录

作者：[Steve Smith](https://ardalis.com/) 和 [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。 本文介绍了如何将日志记录 API 与内置提供程序一起使用。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="add-providers"></a>添加提供程序

日志记录提供程序会显示或存储日志。 例如，控制台提供程序在控制台上显示日志，Azure Application Insights 提供程序会将这些日志存储在 Azure Application Insights 中。 可通过添加多个提供程序将日志发送到多个目标。

::: moniker range=">= aspnetcore-2.0"

要添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17-19)]

默认项目模板调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 扩展方法，该操作将添加以下日志记录提供程序：

* 控制台
* 调试
* EventSource（启动位置：ASP.NET Core 2.2）

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

如果使用 `CreateDefaultBuilder`，则可自行选择提供程序来替换默认提供程序。 调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

要使用提供程序，请安装其 NuGet 包，并在 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 的实例上调用提供程序的扩展方法：

[!code-csharp[](index/samples/1.x/TodoApiSample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

ASP.NET Core [依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将提供 `ILoggerFactory` 实例。 在 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 和 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) 包中定义了 `AddConsole` 和 `AddDebug` 扩展方法。 每个扩展方法都调用 `ILoggerFactory.AddProvider` 方法，传入提供程序的一个实例。

> [!NOTE]
> [示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x)在 `Startup.Configure` 方法中添加了日志提供程序。 要从先前执行的代码获取日志输出，请在 `Startup` 类构造函数中添加日志提供程序。

::: moniker-end

详细了解[内置日志记录提供程序](#built-in-logging-providers)，以及本文稍后部分介绍的[第三方日志记录提供程序](#third-party-logging-providers)。

## <a name="create-logs"></a>创建日志

从 DI 中获取 <xref:Microsoft.Extensions.Logging.ILogger`1> 对象。

::: moniker range=">= aspnetcore-2.0"

以下控制器示例会创建 `Information` 和 `Warning` 日志。 类别为 `TodoApiSample.Controllers.TodoController`（示例应用中 `TodoController` 的完全限定类名）：

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

以下 Razor 页面示例会创建“级别”为 `Information` 且“类别”为 `TodoApiSample.Pages.AboutModel` 的日志：

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

上述示例创建了“级别”为 `Information` 和 `Warning`，且“类别”为 `TodoController` 的日志。 

::: moniker-end

日志“级别”代表所记录事件的严重程度。 日志“类别”是与每个日志关联的字符串。 `ILogger<T>` 实例会创建“类别”为类型 `T` 的完全限定名称的日志。 本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a>启动时创建日志

要将日志写入 `Startup` 类，构造函数签名需包含 `ILogger` 参数：

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,19,26)]

### <a name="create-logs-in-program"></a>在程序中创建日志

要将日志写入 `Program` 类，请从 DI 获取 `ILogger` 实例：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>没有异步记录器方法

日志记录应该会很快，不值得牺牲性能来使用异步代码。 如果你的日志数据存储很慢，请不要直接写入它。 首先考虑将日志消息写入快速存储，稍后再将其变为慢速存储。 例如，记录到由另一进程读取和暂留以减缓存储的消息队列。

## <a name="configuration"></a>配置

日志记录提供程序配置由一个或多个配置提供程序提供：

* 文件格式（INI、JSON 和 XML）。
* 命令行参数。
* 环境变量。
* 内存中的 .NET 对象。
* 未加密的[机密管理器](xref:security/app-secrets)存储。
* 加密的用户存储，如 [Azure Key Vault](xref:security/key-vault-configuration)。
* （已安装或已创建的）自定义提供程序。

例如，日志记录配置通常由应用设置文件的 `Logging` 部分提供。 以下示例显示了典型 *appsettings.Development.json* 文件的内容：

::: moniker range=">= aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

`Logging` 属性可具有 `LogLevel` 和日志提供程序属性（显示控制台）。

`Logging` 下的 `LogLevel` 属性指定了用于记录所选类别的最低[级别](#log-level)。 在本例中，`System` 和 `Microsoft` 类别在 `Information` 级别记录，其他均在 `Debug` 级别记录。

`Logging` 下的其他属性均指定了日志记录提供程序。 本示例针对控制台提供程序。 如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。 提供程序属性（例如本例的 `Console`）也可指定 `LogLevel` 属性。 提供程序下的 `LogLevel` 指定了该提供程序记录的级别。

如果在 `Logging.{providername}.LogLevel` 中指定了级别，则这些级别将重写 `Logging.LogLevel` 中设置的所有内容。

::: moniker-end

::: moniker range="< aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

`LogLevel` 项表示日志名称。 `Default` 项适用于未显式列出的日志。 其值表示应用于给定日志的[日志级别](#log-level)。

::: moniker-end

若要了解如何实现配置提供程序，请参阅 <xref:fundamentals/configuration/index>。

## <a name="sample-logging-output"></a>日志记录输出示例

使用上一部分中显示的示例代码从命令行运行应用时，将在控制台中看到日志。 以下是控制台输出示例：

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

通过向 `http://localhost:5000/api/todo/0` 处的示例应用发出 HTTP Get 请求来生成前述日志。

在 Visual Studio 中运行示例应用时，“调试”窗口中将显示如下日志：


```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

由上一部分所示的 `ILogger` 调用创建的日志是以“TodoApi.Controllers.TodoController”开头的。 以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。 ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。

本文余下部分将介绍有关日志记录的某些详细信息及选项。

## <a name="nuget-packages"></a>NuGet 包

`ILogger` 和 `ILoggerFactory` 接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中，其默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。

## <a name="log-category"></a>日志类别

创建 `ILogger` 对象后，将为其指定“类别”。 该类别包含在由此 `Ilogger` 实例创建的每条日志消息中。 类别可以是任何字符串，但约定需使用类名，例如“TodoApi.Controllers.TodoController”。

使用 `ILogger<T>` 获取一个 `ILogger` 实例，该实例使用 `T` 的完全限定类型名称作为类别：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。

## <a name="log-level"></a>日志级别

每个日志都指定了一个 <xref:Microsoft.Extensions.Logging.LogLevel> 值。 日志级别指示严重性或重要程度。 例如，可在方法正常结束时写入 `Information` 日志，在方法返回“404 找不到”状态代码时写入 `Warning` 日志。

下面的代码会创建 `Information` 和 `Warning` 日志：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

在上述代码中，第一个参数是[日志事件 ID](#log-event-id)。 第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。 稍后将在本文的[消息模板部分](#log-message-template)介绍方法参数。

在方法名称中包含级别的日志方法（例如 `LogInformation` 和 `LogWarning`）是 [ILogger 的扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)。 这些方法会调用可接受 `LogLevel` 参数的 `Log` 方法。 可直接调用 `Log` 方法而不调用其中某个扩展方法，但语法相对复杂。 有关详细信息，请参阅 <xref:Microsoft.Extensions.Logging.ILogger> 和[记录器扩展源代码](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。

ASP.NET Core 定义了以下日志级别（按严重性从低到高排列）。

* 跟踪 = 0

  有关通常仅用于调试的信息。 这些消息可能包含敏感应用程序数据，因此不得在生产环境中启用它们。 默认情况下禁用。

* 调试 = 1

  有关在开发和调试中可能有用的信息。 示例:`Entering method Configure with flag set to true.` 由于日志数量过多，因此仅当执行故障排除时，才在生产中启用 `Debug` 级别日志。

* 信息 = 2

  用于跟踪应用的常规流。 这些日志通常有长期价值。 示例：`Request received for path /api/todo`

* 警告 = 3

  表示应用流中的异常或意外事件。 可能包括不会中断应用运行但仍需调查的错误或其他条件。 `Warning` 日志级别常用于已处理的异常。 示例：`FileNotFoundException for file quotes.txt.`

* 错误 = 4

  表示无法处理的错误和异常。 这些消息指示的是当前活动或操作（例如当前 HTTP 请求）中的失败，而不是整个应用中的失败。 日志消息示例：`Cannot insert record due to duplicate key violation.`

* 严重 = 5

  需要立即关注的失败。 例如数据丢失、磁盘空间不足。

使用日志级别控制写入到特定存储介质或显示窗口的日志输出量。 例如:

* 在生产中，通过 `Information` 级别将 `Trace` 发送到卷数据存储。 通过 `Critical` 级别将 `Warning` 发送到值数据存储。
* 在开发过程中，通过`Critical` 级别将 `Warning` 发送到控制台，并在进行故障排除时通过 `Information` 级别添加 `Trace`。

本文稍后的[日志筛选](#log-filtering)部分介绍如何控制提供程序处理的日志级别。

ASP.NET Core 为框架事件写入日志。 本文前面部分提供的日志示例排除了低于 `Information` 级别的日志，因此未创建 `Debug` 或 `Trace` 级别日志。 以下示例介绍了通过运行配置为显示 `Debug` 日志的示例应用而生成的控制台日志：

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>日志事件 ID

每个日志都可指定一个事件 ID。 该示例应用通过使用本地定义的 `LoggingEvents` 类来执行此操作：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

事件 ID 与一组事件相关联。 例如，与在页面上显示项列表相关的所有日志可能是 1001。

日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。 调试提供程序不显示事件 ID。 控制台提供程序在类别后的括号中显示事件 ID：

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>日志消息模板

每个日志都会指定一个消息模板。 消息模板可包含要填写参数的占位符。 请在占位符中使用名称而不是数字。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

占位符的顺序（而非其名称）决定了为其提供值的参数。 在以下代码中，请注意消息模板中的参数名称未按顺序排列：

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

此代码创建了一个参数值按顺序排列的日志消息：

```
Parameter values: parm1, parm2
```

日志记录框架按此方式工作，这样日志记录提供程序即可实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。 参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。 通过此信息，日志记录提供程序能够将参数值存储为字段。 例如，假设记录器方法调用如下所示：

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

如果要将日志发送到 Azure 表存储，则每个 Azure 表实体都可具有 `ID` 和 `RequestTime` 属性，这简化了对日志数据的查询。 无需分析文本消息的超时，查询即可找到特定 `RequestTime` 范围内的全部日志。

## <a name="logging-exceptions"></a>日志记录异常

记录器方法有可传入异常的重载，如下方示例所示：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

不同的提供程序处理异常信息的方式不同。 以下是上示代码的调试提供程序输出示例。

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>日志筛选

::: moniker range=">= aspnetcore-2.0"

可为特定或所有提供程序和类别指定最低日志级别。 最低级别以下的日志不会传递给该提供程序，因此不会显示或存储它们。

要禁止显示所有日志，可将 `LogLevel.None` 指定为最低日志级别。 `LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。

### <a name="create-filter-rules-in-configuration"></a>在配置中创建筛选规则

项目模板代码调用 `CreateDefaultBuilder` 来为控制台和调试提供程序设置日志记录。 `CreateDefaultBuilder` 方法还使用如下所示的代码，设置日志记录以查找 `Logging` 部分的配置：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=16)]

配置数据按提供程序和类别指定最低日志级别，如下方示例所示：

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

此 JSON 将创建 6 条筛选规则：1 条用于调试提供程序， 4 条用于控制台提供程序， 1 条用于所有提供程序。 创建 `ILogger` 对象时，为每个提供程序选择一个规则。

### <a name="filter-rules-in-code"></a>代码中的筛选规则

下面的示例演示了如何在代码中注册筛选规则：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

第二个 `AddFilter` 使用类型名称来指定调试提供程序。 第一个 `AddFilter` 应用于全部提供程序，因为它未指定提供程序类型。

### <a name="how-filtering-rules-are-applied"></a>如何应用筛选规则

先前示例中显示的配置数据和 `AddFilter` 代码会创建下表所示的规则。 前六条由配置示例创建，后两条由代码示例创建。

| 数字 | 提供程序      | 类别的开头为...          | 最低日志级别 |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | 调试         | 全部类别                          | 信息       |
| 2      | 控制台       | Microsoft.AspNetCore.Mvc.Razor.Internal | 警告           |
| 3      | 控制台       | Microsoft.AspNetCore.Mvc.Razor.Razor    | 调试             |
| 4      | 控制台       | Microsoft.AspNetCore.Mvc.Razor          | Error             |
| 5      | 控制台       | 全部类别                          | 信息       |
| 6      | 全部提供程序 | 全部类别                          | 调试             |
| 7      | 全部提供程序 | 系统                                  | 调试             |
| 8      | 调试         | Microsoft                               | 跟踪             |

创建 `ILogger` 对象时，`ILoggerFactory` 对象将根据提供程序选择一条规则，将其应用于该记录器。 将按所选规则筛选 `ILogger` 实例写入的所有消息。 从可用规则中为每个提供程序和类别对选择最具体的规则。

在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：

* 选择匹配提供程序或其别名的所有规则。 如果找不到任何匹配项，则选择提供程序为空的所有规则。
* 根据上一步的结果，选择具有最长匹配类别前缀的规则。 如果找不到任何匹配项，则选择未指定类别的所有规则。
* 如果选择了多条规则，则采用最后一条。
* 如果未选择任何规则，则使用 `MinimumLevel`。

假设你使用上述规则列表为类别“Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine”创建了 `ILogger` 对象：

* 对于调试提供程序，规则 1、6 和 8 适用。 规则 8 最为具体，因此选择了它。
* 对于控制台提供程序，符合的有规则 3、规则 4、规则 5 和规则 6。 规则 3 最为具体。

生成的 `ILogger` 实例将 `Trace` 级别及更高级别的日志发送到调试提供程序。 `Debug` 级别及更高级别的日志会发送到控制台提供程序。

### <a name="provider-aliases"></a>提供程序别名

每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称。  对于内置提供程序，请使用以下别名：

* 控制台
* 调试
* EventLog
* AzureAppServices
* TraceSource
* EventSource

### <a name="default-minimum-level"></a>默认最低级别

仅当配置或代码中的规则对给定提供程序和类别都不适用时，最低级别设置才会生效。 下面的示例演示如何设置最低级别：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

如果没有明确设置最低级别，则默认值为 `Information`，它表示 `Trace` 和 `Debug` 日志将被忽略。

### <a name="filter-functions"></a>筛选器函数

对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数。 函数中的代码可访问提供程序类型、类别和日志级别。 例如:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

通过某些日志记录提供程序，可根据日志级别和类别指定何时向存储介质写入日志、何时忽略日志。

`AddConsole` 和 `AddDebug` 扩展方法提供了接受筛选条件的重载。 下列示例代码将使控制台提供程序忽略低于 `Warning` 级别的日志，而使调试提供程序忽略由框架创建的日志。

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

`AddEventLog` 方法拥有接受 `EventLogSettings` 实例的重载，该实例的 `Filter` 属性中可能包含筛选函数。 TraceSource 提供程序不提供以上任何重载，因为其日志记录级别和其他参数都以它使用的 `SourceSwitch` 和 `TraceListener` 为依据。

可使用 `WithFilter` 扩展方法为所有通过 `ILoggerFactory` 实例注册的提供程序设置筛选规则。 下面的示例将（类别以“Microsoft”或“System”开头的）框架日志限制为警告，并在调试级别记录应用程序代码创建的日志。

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

要防止写入任何日志，请将 `LogLevel.None` 指定为最低日志级别。 `LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。

`WithFilter` 扩展方法由 [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet 包提供。 该方法返回一个新的 `ILoggerFactory` 实例，该实例将筛选传递给注册的所有记录器提供程序的日志消息。 它不会影响其他任何 `ILoggerFactory` 实例，包括原始 `ILoggerFactory` 实例。

::: moniker-end

## <a name="system-categories-and-levels"></a>系统类别和级别

下面是 ASP.NET Core 和 Entity Framework Core 使用的一些类别，备注中说明了可从这些类别获取的具体日志：

| 类别                            | 说明 |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | 常规 ASP.NET Core 诊断。 |
| Microsoft.AspNetCore.DataProtection | 考虑、找到并使用了哪些密钥。 |
| Microsoft.AspNetCore.HostFiltering  | 所允许的主机。 |
| Microsoft.AspNetCore.Hosting        | HTTP 请求完成的时间和启动时间。 加载了哪些承载启动程序集。 |
| Microsoft.AspNetCore.Mvc            | MVC 和 Razor 诊断。 模型绑定、筛选器执行、视图编译和操作选择。 |
| Microsoft.AspNetCore.Routing        | 路由匹配信息。 |
| Microsoft.AspNetCore.Server         | 连接启动、停止和保持活动响应。 HTTP 证书信息。 |
| Microsoft.AspNetCore.StaticFiles    | 提供的文件。 |
| Microsoft.EntityFrameworkCore       | 常规 Entity Framework Core 诊断。 数据库活动和配置、更改检测、迁移。 |

## <a name="log-scopes"></a>日志作用域

 “作用域”可对一组逻辑操作分组。 此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。 例如，在处理事务期间创建的每个日志都可包括事务 ID。

范围是由 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 `IDisposable` 类型，持续至释放为止。 要使用作用域，请在 `using` 块中包装记录器调用：

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

下列代码为控制台提供程序启用作用域：

::: moniker range="> aspnetcore-2.0"

Program.cs:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> 要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。
>
> 若要了解关配置，请参阅[配置](#configuration)部分。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Program.cs:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> 要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

*Startup.cs*：

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

每条日志消息都包含作用域内的信息：

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>内置日志记录提供程序

ASP.NET Core 提供以下提供程序：

* [控制台](#console-provider)
* [调试](#debug-provider)
* [EventSource](#eventsource-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)

本文稍后将介绍 [Azure 的日志记录](#logging-in-azure)选项。

有关 stdout 日志记录的信息，请参阅 <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> 和 <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>。

### <a name="console-provider"></a>控制台提供程序

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 提供程序包向控制台发送日志输出。 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

通过 [AddConsole 重载](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions)，可传入一个最低日志级别、一个筛选器函数，以及一个用于指示作用域是否受支持的布尔值。 另一个选项是传递 `IConfiguration` 对象，可通过它来指定支持的作用域及日志记录级别。

控制台提供程序对性能有重大影响，通常不适合在生产中使用。

在 Visual Studio 中创建新项目时，`AddConsole` 方法如下所示：

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

此代码引用 appSettings.json 文件的 `Logging` 部分：

[!code-json[](index/samples/1.x/TodoApiSample//appsettings.json)]

所显示的设置将框架日志限制为警告，并允许在调试级别记录应用日志，如[日志筛选](#log-filtering)部分所述。 有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。

::: moniker-end

要查看控制台日志记录输出，请在项目文件夹中打开命令提示符并运行以下命令：

```console
dotnet run
```

### <a name="debug-provider"></a>调试提供程序

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 提供程序包使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类（`Debug.WriteLine` 方法调用）来写入日志输出。

在 Linux 中，此提供程序将日志写入 /var/log/message。

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

[AddDebug 重载](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions)允许传入最低日志级别或筛选器函数。

::: moniker-end

### <a name="eventsource-provider"></a>EventSource 提供程序

对于面向 ASP.NET Core 1.1.0 或更高版本的应用，[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 提供程序包可实现事件跟踪。 在 Windows 中，它使用 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。 提供程序可跨平台使用，但尚无支持 Linux 或 macOS 的事件集合和显示工具。

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventSourceLogger();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventSourceLogger();
```

::: moniker-end

可使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。 虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。

要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging`。 （请勿遗漏字符串起始处的星号。）

![其他 Perfview 提供程序](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows EventLog 提供程序

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 提供程序包向 Windows 事件日志发送日志输出。

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)允许传入 `EventLogSettings` 或最低日志级别。

::: moniker-end

### <a name="tracesource-provider"></a>TraceSource 提供程序

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 提供程序包使用 <xref:System.Diagnostics.TraceSource> 库和提供程序。

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddTraceSource(sourceSwitchName);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

::: moniker-end

[AddTraceSource 重载](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) 允许传入资源开关和跟踪侦听器。

要使用此提供程序，应用必须在 .NET Framework（而非 .NET Core）上运行。 提供程序可将消息路由到多个[侦听器](/dotnet/framework/debug-trace-profile/trace-listeners)，例如示例应用中使用的 <xref:System.Diagnostics.TextWriterTraceListener>。

::: moniker range="< aspnetcore-2.0"

以下示例配置了 `TraceSource` 提供程序，用于向控制台窗口记录 `Warning` 和更高级别的消息。

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

## <a name="logging-in-azure"></a>Azure 中的日志记录

要了解 Azure 中的日志记录，请参阅下列部分：

* [Azure 应用服务提供程序](#azure-app-service-provider)
* [Azure 日志流式处理](#azure-log-streaming)

::: moniker range=">= aspnetcore-1.1"

* [Azure Application Insights 跟踪日志记录](#azure-application-insights-trace-logging)

::: moniker-end

### <a name="azure-app-service-provider"></a>Azure 应用服务提供程序

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。 面向 .NET Core 1.1 或更高版本的应用可使用该提供程序包。

::: moniker range=">= aspnetcore-2.0"

如果面向 .NET Core，请注意以下几点：

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* 提供程序包随附在 ASP.NET Core [Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)中。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不包括此提供程序包。 要使用提供程序，请安装该程序包。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

* 不要显式调用 <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>。 将应用部署到 Azure 应用服务时，会自动使提供程序对应用可用。

如果面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包，请向项目添加提供程序包。 在 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 实例上调用 `AddAzureWebAppDiagnostics`：

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 重载允许传入 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。 设置对象可以覆盖默认设置，例如日志记录输出模板、blob 名称和文件大小限制。 （“输出模板”是一个消息模板，除了通过 `ILogger` 方法调用提供的内容之外，还可将其应用于所有日志。）

在部署应用服务应用时，应用程序将遵循 Azure 门户中“应用服务”页面下的[诊断日志](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag)部分的设置。 更新这些设置后，更改会立即生效，无需重新启动或重新部署应用。

![Azure 日志记录设置](index/_static/azure-logging-settings.png)

日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt。 默认文件大小上限为 10 MB，默认最大保留文件数为 2。 默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt。 要详细了解默认行为，请参阅 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。

该提供程序仅当项目在 Azure 环境中运行时有效。 项目在本地运行时，该提供程序无效 &mdash; 它不会写入本地文件或 blob 的本地开发存储。

::: moniker-end

### <a name="azure-log-streaming"></a>Azure 日志流式处理

通过 Azure 日志流式处理，可从以下位置实时查看日志活动：

* 应用服务器
* Web 服务器
* 请求跟踪失败

要配置 Azure 日志流式处理，请执行以下操作：

* 从应用的门户页导航到“诊断日志”页。
* 将“应用程序日志记录(Filesystem)”设置为“开”。

![Azure 门户诊断日志页](index/_static/azure-diagnostic-logs.png)

导航到“日志流式处理”页，查看应用消息。 它们由应用通过 `ILogger` 接口记录。

![Azure 门户应用程序日志流式处理](index/_static/azure-log-streaming.png)

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights 跟踪日志记录

Application Insights SDK 可收集和报告 ASP.NET Core 日志记录基础结构生成的日志。 有关更多信息，请参见以下资源：

* [Application Insights 概述](/azure/application-insights/app-insights-overview)
* [用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Microsoft/ApplicationInsights-aspnetcore Wiki：日志记录](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Logging)。

::: moniker-end

## <a name="third-party-logging-providers"></a>第三方日志记录提供程序

适用于 ASP.NET Core 的第三方日志记录框架：

* [elmah.io](https://elmah.io/)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）
* [Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）
* [JSNLog](http://jsnlog.com/)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）
* [KissLog.net](https://kisslog.net/)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）
* [Loggr](http://loggr.net/)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）
* [NLog](http://nlog-project.org/)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）
* [Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）
* [Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-extensions-logging)）
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）

某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。

使用第三方框架类似于使用以下内置提供程序之一：

1. 将 NuGet 包添加到你的项目。
1. 调用 `ILoggerFactory`。

有关详细信息，请参阅各提供程序的相关文档。 Microsoft 不支持第三方日志记录提供程序。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/logging/loggermessage>
