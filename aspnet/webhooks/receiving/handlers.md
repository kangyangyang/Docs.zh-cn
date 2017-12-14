---
uid: webhooks/receiving/handlers
title: "ASP.NET Webhook 处理程序 |Microsoft 文档"
author: rick-anderson
description: "如何处理 ASP.NET Webhook 请求。"
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2012
ms.topic: article
ms.assetid: a55b0d20-9c90-4bd3-a471-20da6f569f0c
ms.technology: 
ms.prod: .net-framework
ms.openlocfilehash: 3aaef756ee00d7e44aa757062e1ef297312ecf22
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="aspnet-webhooks-handlers"></a><span data-ttu-id="580d0-103">ASP.NET Webhook 处理程序</span><span class="sxs-lookup"><span data-stu-id="580d0-103">ASP.NET WebHooks handlers</span></span>

<span data-ttu-id="580d0-104">一旦 Webhook 请求经过验证 WebHook 接收方，已准备好由用户代码处理。</span><span class="sxs-lookup"><span data-stu-id="580d0-104">Once WebHooks requests has been validated by a WebHook receiver, it is ready to be processed by user code.</span></span> <span data-ttu-id="580d0-105">这就是*处理程序*进入。</span><span class="sxs-lookup"><span data-stu-id="580d0-105">This is where *handlers* come in.</span></span> <span data-ttu-id="580d0-106">处理程序派生自[IWebHookHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookHandler.cs)接口，但通常使用[WebHookHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookHandler.cs)而不是直接从接口派生的类。</span><span class="sxs-lookup"><span data-stu-id="580d0-106">Handlers derive from the [IWebHookHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookHandler.cs) interface but typically uses the [WebHookHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookHandler.cs) class instead of deriving directly from the interface.</span></span>

<span data-ttu-id="580d0-107">WebHook 请求可由一个或多个处理程序处理。</span><span class="sxs-lookup"><span data-stu-id="580d0-107">A WebHook request can be processed by one or more handlers.</span></span> <span data-ttu-id="580d0-108">处理程序调用顺序基于其各自*顺序*转从最低到高顺序其中简单的整数 （建议必须介于 1 和 100） 的属性：</span><span class="sxs-lookup"><span data-stu-id="580d0-108">Handlers are called in order based on their respective *Order* property going from lowest to highest where Order is a simple integer (suggested to be between 1 and 100):</span></span>

![WebHook 处理程序订单属性关系图](_static/Handlers.png)

<span data-ttu-id="580d0-110">可以选择性地设置一个处理程序*响应*WebHookHandlerContext 这将导致在处理停止和响应发回的 WebHook 的 HTTP 响应上的属性。</span><span class="sxs-lookup"><span data-stu-id="580d0-110">A handler can optionally set the *Response* property on the WebHookHandlerContext which will lead the processing to stop and the response to be sent back as the HTTP response to the WebHook.</span></span> <span data-ttu-id="580d0-111">在上面的示例中，因为它具有更高的阶比 B 和 B 设置响应，将不会调用处理程序 C。</span><span class="sxs-lookup"><span data-stu-id="580d0-111">In the case above, Handler C won’t get called because it has a higher order than B and B sets the response.</span></span>

<span data-ttu-id="580d0-112">设置响应是通常仅适用于 Webhook 响应可以在其中执行回原始的 API 的信息。</span><span class="sxs-lookup"><span data-stu-id="580d0-112">Setting the response is typically only relevant for WebHooks where the response can carry information back to the originating API.</span></span> <span data-ttu-id="580d0-113">例如，这是带有 Slack Webhook 其中响应发回到 WebHook 来自何处通道的用例。</span><span class="sxs-lookup"><span data-stu-id="580d0-113">This is for example the case with Slack WebHooks where the response is posted back to the channel where the WebHook came from.</span></span> <span data-ttu-id="580d0-114">如果它们仅想要从该特定的接收方接收 Webhook，处理程序可以设置的接收方属性。</span><span class="sxs-lookup"><span data-stu-id="580d0-114">Handlers can set the Receiver property if they only want to receive WebHooks from that particular receiver.</span></span> <span data-ttu-id="580d0-115">如果它们不能设置为所有这些调用的接收方。</span><span class="sxs-lookup"><span data-stu-id="580d0-115">If they don’t set the receiver they are called for all of them.</span></span>

<span data-ttu-id="580d0-116">响应的一个其他常见用途是使用*410 不再存在*响应以指示 WebHook 不再处于活动状态，并且应提交任何进一步的请求。</span><span class="sxs-lookup"><span data-stu-id="580d0-116">One other common use of a response is to use a *410 Gone* response to indicate that the WebHook no longer is active and no further requests should be submitted.</span></span>

<span data-ttu-id="580d0-117">默认情况下将由所有 WebHook 接收方调用处理程序。</span><span class="sxs-lookup"><span data-stu-id="580d0-117">By default a handler will be called by all WebHook receivers.</span></span> <span data-ttu-id="580d0-118">但是，如果*接收方*属性设置为处理程序的名称，则该处理程序将仅从该接收方收到 WebHook 请求。</span><span class="sxs-lookup"><span data-stu-id="580d0-118">However, if the *Receiver* property is set to the name of a handler then that handler will only receive WebHook requests from that receiver.</span></span>

## <a name="processing-a-webhook"></a><span data-ttu-id="580d0-119">处理 WebHook</span><span class="sxs-lookup"><span data-stu-id="580d0-119">Processing a WebHook</span></span>

<span data-ttu-id="580d0-120">当调用处理程序时，它获取[WebHookHandlerContext](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookHandlerContext.cs)包含有关 WebHook 请求的信息。</span><span class="sxs-lookup"><span data-stu-id="580d0-120">When a handler is called, it gets a [WebHookHandlerContext](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookHandlerContext.cs) containing information about the WebHook request.</span></span> <span data-ttu-id="580d0-121">有从可用的数据，通常将 HTTP 请求正文，*数据*属性。</span><span class="sxs-lookup"><span data-stu-id="580d0-121">The data, typically the HTTP request body, is available from the *Data* property.</span></span>

<span data-ttu-id="580d0-122">数据类型通常是 JSON 或 HTML 窗体数据，但可以根据需要强制转换为更具体的类型。</span><span class="sxs-lookup"><span data-stu-id="580d0-122">The type of the data is typically JSON or HTML form data, but it is possible to cast to a more specific type if desired.</span></span> <span data-ttu-id="580d0-123">例如，由 ASP.NET Webhook 生成自定义 Webhook 可以转换为类型[CustomNotifications](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers.Custom/WebHooks/CustomNotifications.cs) ，如下所示：</span><span class="sxs-lookup"><span data-stu-id="580d0-123">For example, the custom WebHooks generated by ASP.NET WebHooks can be cast to the type [CustomNotifications](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers.Custom/WebHooks/CustomNotifications.cs) as follows:</span></span>

```csharp
public class MyWebHookHandler : WebHookHandler
{
    public MyWebHookHandler()
    {
        this.Receiver = "custom";
    }

    public override Task ExecuteAsync(string generator, WebHookHandlerContext context)
    {
        CustomNotifications notifications = context.GetDataOrDefault<CustomNotifications>();
        foreach (var notification in notifications.Notifications)
        {
            ...
        }
        return Task.FromResult(true);
    }
}
```

  ## <a name="queued-processing"></a><span data-ttu-id="580d0-124">排队的处理</span><span class="sxs-lookup"><span data-stu-id="580d0-124">Queued Processing</span></span>

<span data-ttu-id="580d0-125">如果在几秒内不生成响应，大多数 WebHook 发件人将重新发送 WebHook。</span><span class="sxs-lookup"><span data-stu-id="580d0-125">Most WebHook senders will resend a WebHook if a response is not generated within a handful of seconds.</span></span> <span data-ttu-id="580d0-126">这意味着您的处理程序必须完成的处理顺序不以使它能够再次调用该时间范围内。</span><span class="sxs-lookup"><span data-stu-id="580d0-126">This means that your handler must complete the processing within that time frame in order not for it to be called again.</span></span>

<span data-ttu-id="580d0-127">如果处理花费的时间长，或单独更好地处理则[WebHookQueueHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookQueueHandler.cs)可以用于例如提交到队列，WebHook 请求[Azure 存储队列](https://msdn.microsoft.com/en-us/library/azure/dd179353.aspx)。</span><span class="sxs-lookup"><span data-stu-id="580d0-127">If the processing takes longer, or is better handled separately then the [WebHookQueueHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookQueueHandler.cs) can be used to submit the WebHook request to a queue, for example [Azure Storage Queue](https://msdn.microsoft.com/en-us/library/azure/dd179353.aspx).</span></span>

<span data-ttu-id="580d0-128">概述[WebHookQueueHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookQueueHandler.cs)此处提供实现：</span><span class="sxs-lookup"><span data-stu-id="580d0-128">An outline of a [WebHookQueueHandler](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/WebHooks/WebHookQueueHandler.cs) implementation is provided here:</span></span>

```csharp
public class QueueHandler : WebHookQueueHandler
{
    public override Task EnqueueAsync(WebHookQueueContext context)
    {

        // Enqueue WebHookQueueContext to your queuing system of choice

        return Task.FromResult(true);
    }
}
```