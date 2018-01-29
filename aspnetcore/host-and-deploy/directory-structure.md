---
title: "ASP.NET 核心目录结构"
author: guardrex
description: "请参阅发布的 ASP.NET Core 应用程序的目录结构。"
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 03/15/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 27f0f40aea1c55315642d7d6f9b9d7be3e111cb4
ms.sourcegitcommit: 12e5194936b7e820efc5505a2d5d4f84e88eb5ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="directory-structure-of-published-aspnet-core-apps"></a><span data-ttu-id="a971f-103">已发布的 ASP.NET Core 应用的目录结构</span><span class="sxs-lookup"><span data-stu-id="a971f-103">Directory structure of published ASP.NET Core apps</span></span>

<span data-ttu-id="a971f-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a971f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a971f-105">在 ASP.NET 核，应用程序目录中，*发布*，组成应用程序文件、 配置文件、 静态资产、 包和的运行时 （独立的应用）。</span><span class="sxs-lookup"><span data-stu-id="a971f-105">In ASP.NET Core, the application directory, *publish*, is comprised of application files, config files, static assets, packages, and the runtime (for self-contained apps).</span></span>

| <span data-ttu-id="a971f-106">应用类型</span><span class="sxs-lookup"><span data-stu-id="a971f-106">App Type</span></span>                       | <span data-ttu-id="a971f-107">目录结构</span><span class="sxs-lookup"><span data-stu-id="a971f-107">Directory Structure</span></span> |
| ------------------------------ | ------------------- |
| <span data-ttu-id="a971f-108">依赖于框架的部署</span><span class="sxs-lookup"><span data-stu-id="a971f-108">Framework-dependent Deployment</span></span> | <ul><li><span data-ttu-id="a971f-109">publish\*</span><span class="sxs-lookup"><span data-stu-id="a971f-109">publish\*</span></span><ul><li><span data-ttu-id="a971f-110">日志\*（如果包含在 publishOptions）</span><span class="sxs-lookup"><span data-stu-id="a971f-110">logs\* (if included in publishOptions)</span></span></li><li><span data-ttu-id="a971f-111">refs\*</span><span class="sxs-lookup"><span data-stu-id="a971f-111">refs\*</span></span></li><li><span data-ttu-id="a971f-112">运行时\*</span><span class="sxs-lookup"><span data-stu-id="a971f-112">runtimes\*</span></span></li><li><span data-ttu-id="a971f-113">视图\*（如果包含在 publishOptions）</span><span class="sxs-lookup"><span data-stu-id="a971f-113">Views\* (if included in publishOptions)</span></span></li><li><span data-ttu-id="a971f-114">wwwroot\* （如果包含在 publishOptions）</span><span class="sxs-lookup"><span data-stu-id="a971f-114">wwwroot\* (if included in publishOptions)</span></span></li><li><span data-ttu-id="a971f-115">.dll 文件</span><span class="sxs-lookup"><span data-stu-id="a971f-115">.dll files</span></span></li><li><span data-ttu-id="a971f-116">myapp.deps.json</span><span class="sxs-lookup"><span data-stu-id="a971f-116">myapp.deps.json</span></span></li><li><span data-ttu-id="a971f-117">myapp.dll</span><span class="sxs-lookup"><span data-stu-id="a971f-117">myapp.dll</span></span></li><li><span data-ttu-id="a971f-118">myapp.pdb</span><span class="sxs-lookup"><span data-stu-id="a971f-118">myapp.pdb</span></span></li><li><span data-ttu-id="a971f-119">myapp。PrecompiledViews.dll （如果预编译 Razor 视图）</span><span class="sxs-lookup"><span data-stu-id="a971f-119">myapp.PrecompiledViews.dll (if precompiling Razor Views)</span></span></li><li><span data-ttu-id="a971f-120">myapp。PrecompiledViews.pdb （如果预编译 Razor 视图）</span><span class="sxs-lookup"><span data-stu-id="a971f-120">myapp.PrecompiledViews.pdb (if precompiling Razor Views)</span></span></li><li><span data-ttu-id="a971f-121">myapp.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="a971f-121">myapp.runtimeconfig.json</span></span></li><li><span data-ttu-id="a971f-122">（如果包含在 publishOptions） 的 web.config</span><span class="sxs-lookup"><span data-stu-id="a971f-122">web.config (if included in publishOptions)</span></span></li></ul></li></ul> |
| <span data-ttu-id="a971f-123">自包含的部署</span><span class="sxs-lookup"><span data-stu-id="a971f-123">Self-contained Deployment</span></span>      | <ul><li><span data-ttu-id="a971f-124">publish\*</span><span class="sxs-lookup"><span data-stu-id="a971f-124">publish\*</span></span><ul><li><span data-ttu-id="a971f-125">日志\*（如果包含在 publishOptions）</span><span class="sxs-lookup"><span data-stu-id="a971f-125">logs\* (if included in publishOptions)</span></span></li><li><span data-ttu-id="a971f-126">refs\*</span><span class="sxs-lookup"><span data-stu-id="a971f-126">refs\*</span></span></li><li><span data-ttu-id="a971f-127">视图\*（如果包含在 publishOptions）</span><span class="sxs-lookup"><span data-stu-id="a971f-127">Views\* (if included in publishOptions)</span></span></li><li><span data-ttu-id="a971f-128">wwwroot\* （如果包含在 publishOptions）</span><span class="sxs-lookup"><span data-stu-id="a971f-128">wwwroot\* (if included in publishOptions)</span></span></li><li><span data-ttu-id="a971f-129">.dll 文件</span><span class="sxs-lookup"><span data-stu-id="a971f-129">.dll files</span></span></li><li><span data-ttu-id="a971f-130">myapp.deps.json</span><span class="sxs-lookup"><span data-stu-id="a971f-130">myapp.deps.json</span></span></li><li><span data-ttu-id="a971f-131">myapp.exe</span><span class="sxs-lookup"><span data-stu-id="a971f-131">myapp.exe</span></span></li><li><span data-ttu-id="a971f-132">myapp.pdb</span><span class="sxs-lookup"><span data-stu-id="a971f-132">myapp.pdb</span></span></li><li><span data-ttu-id="a971f-133">myapp。PrecompiledViews.dll （如果预编译 Razor 视图）</span><span class="sxs-lookup"><span data-stu-id="a971f-133">myapp.PrecompiledViews.dll (if precompiling Razor Views)</span></span></li><li><span data-ttu-id="a971f-134">myapp。PrecompiledViews.pdb （如果预编译 Razor 视图）</span><span class="sxs-lookup"><span data-stu-id="a971f-134">myapp.PrecompiledViews.pdb (if precompiling Razor Views)</span></span></li><li><span data-ttu-id="a971f-135">myapp.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="a971f-135">myapp.runtimeconfig.json</span></span></li><li><span data-ttu-id="a971f-136">（如果包含在 publishOptions） 的 web.config</span><span class="sxs-lookup"><span data-stu-id="a971f-136">web.config (if included in publishOptions)</span></span></li></ul></li></ul> |
<span data-ttu-id="a971f-137">\*指示目录</span><span class="sxs-lookup"><span data-stu-id="a971f-137">\* Indicates a directory</span></span>

<span data-ttu-id="a971f-138">内容*发布*目录表示*内容的根路径*，也称为*应用程序基路径*，部署。</span><span class="sxs-lookup"><span data-stu-id="a971f-138">The contents of the *publish* directory represents the *content root path*, also called the *application base path*, of the deployment.</span></span> <span data-ttu-id="a971f-139">任何名称提供给*发布*部署中的目录，其位置用作托管的应用程序服务器的物理路径。</span><span class="sxs-lookup"><span data-stu-id="a971f-139">Whatever name is given to the *publish* directory in the deployment, its location serves as the server's physical path to the hosted application.</span></span> <span data-ttu-id="a971f-140">*Wwwroot*目录中，如果存在，仅包含静态资产。</span><span class="sxs-lookup"><span data-stu-id="a971f-140">The *wwwroot* directory, if present, only contains static assets.</span></span> <span data-ttu-id="a971f-141">*日志*目录可能包含在部署项目中创建它并添加`<Target>`到下面显示的元素你*.csproj*文件或通过以物理方式上创建目录服务器。</span><span class="sxs-lookup"><span data-stu-id="a971f-141">The *logs* directory may be included in the deployment by creating it in the project and adding the `<Target>` element shown below to your *.csproj* file or by physically creating the directory on the server.</span></span>

```xml
<Target Name="CreateLogsFolder" AfterTargets="Publish">
  <MakeDir Directories="$(PublishDir)Logs" 
           Condition="!Exists('$(PublishDir)Logs')" />
  <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                    Lines="Generated file" 
                    Overwrite="True" 
                    Condition="!Exists('$(PublishDir)Logs\.log')" />
</Target>
```

<span data-ttu-id="a971f-142">`<MakeDir>`元素创建一个空*日志*中已发布的输出文件夹。</span><span class="sxs-lookup"><span data-stu-id="a971f-142">The `<MakeDir>` element creates an empty *Logs* folder in the published output.</span></span> <span data-ttu-id="a971f-143">元素使用`PublishDir`属性来确定用于创建文件夹的目标位置。</span><span class="sxs-lookup"><span data-stu-id="a971f-143">The element uses the `PublishDir` property to determine the target location for creating the folder.</span></span> <span data-ttu-id="a971f-144">多种部署方法，例如 Web 部署，在部署过程中跳过空文件夹。</span><span class="sxs-lookup"><span data-stu-id="a971f-144">Several deployment methods, such as Web Deploy, skip empty folders during deployment.</span></span> <span data-ttu-id="a971f-145">`<WriteLinesToFile>`元素生成的文件中*日志*文件夹中，这可确保部署到服务器的文件夹。</span><span class="sxs-lookup"><span data-stu-id="a971f-145">The `<WriteLinesToFile>` element generates a file in the *Logs* folder, which guarantees deployment of the folder to the server.</span></span> <span data-ttu-id="a971f-146">请注意，如果工作进程不具有写访问权限的目标文件夹的文件夹创建可能仍会失败。</span><span class="sxs-lookup"><span data-stu-id="a971f-146">Note that folder creation may still fail if the worker process doesn't have write access to the target folder.</span></span>

<span data-ttu-id="a971f-147">部署目录中需要读取/Execute 权限，而*日志*目录需要读/写权限。</span><span class="sxs-lookup"><span data-stu-id="a971f-147">The deployment directory requires Read/Execute permissions, while the *Logs* directory requires Read/Write permissions.</span></span> <span data-ttu-id="a971f-148">将在其中写入资产的附加目录需要读/写权限。</span><span class="sxs-lookup"><span data-stu-id="a971f-148">Additional directories where assets will be written require Read/Write permissions.</span></span>