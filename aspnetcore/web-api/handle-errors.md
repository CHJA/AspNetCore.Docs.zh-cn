---
title: 处理 ASP.NET Core Web API 中的错误
author: pranavkm
description: 了解 ASP.NET Core Web API 的错误处理。
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/27/2019
uid: web-api/handle-errors
ms.openlocfilehash: dc21d4b2cf096b8d38b0a24d739e6874186004e7
ms.sourcegitcommit: 5d25a7f22c50ca6fdd0f8ecd8e525822e1b35b7a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2019
ms.locfileid: "71551747"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="38a92-103">处理 ASP.NET Core Web API 中的错误</span><span class="sxs-lookup"><span data-stu-id="38a92-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="38a92-104">本文介绍如何处理和自定义 ASP.NET Core Web API 的错误处理。</span><span class="sxs-lookup"><span data-stu-id="38a92-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="38a92-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="38a92-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="38a92-106">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="38a92-106">Developer Exception Page</span></span>

<span data-ttu-id="38a92-107">[开发人员异常页](xref:fundamentals/error-handling)是一种用于获取服务器错误详细堆栈跟踪的有用工具。</span><span class="sxs-lookup"><span data-stu-id="38a92-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="38a92-108">它使用 <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> 来捕获 HTTP 管道中的同步和异步异常，并生成错误响应。</span><span class="sxs-lookup"><span data-stu-id="38a92-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="38a92-109">为了进行说明，请考虑以下控制器操作：</span><span class="sxs-lookup"><span data-stu-id="38a92-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="38a92-110">运行以下 `curl` 命令以测试前面的操作：</span><span class="sxs-lookup"><span data-stu-id="38a92-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38a92-111">在 ASP.NET Core 3.0 及更高版本中，如果客户端不请求 HTML 格式的输出，则开发人员异常页将显示纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="38a92-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="38a92-112">将显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="38a92-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="38a92-113">要改为显示 HTML 格式的响应，请将 `Accept` HTTP 请求头设置为 `text/html` 媒体类型。</span><span class="sxs-lookup"><span data-stu-id="38a92-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="38a92-114">例如:</span><span class="sxs-lookup"><span data-stu-id="38a92-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="38a92-115">请考虑以下 HTTP 响应摘录：</span><span class="sxs-lookup"><span data-stu-id="38a92-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="38a92-116">在 ASP.NET Core 2.2 及更低版本中，开发人员异常页将显示 HTML 格式的响应。</span><span class="sxs-lookup"><span data-stu-id="38a92-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="38a92-117">例如，请考虑以下 HTTP 响应摘录：</span><span class="sxs-lookup"><span data-stu-id="38a92-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38a92-118">通过 Postman 等工具进行测试时，HTML 格式的响应会很有用。</span><span class="sxs-lookup"><span data-stu-id="38a92-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="38a92-119">以下屏幕截图显示了 Postman 中的纯文本和 HTML 格式的响应：</span><span class="sxs-lookup"><span data-stu-id="38a92-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Postman 中的开发人员异常页测试](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="38a92-121">仅当应用程序在开发环境中运行时才启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="38a92-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="38a92-122">否则当应用程序在生产环境中运行时，详细的异常信息会向公众泄露</span><span class="sxs-lookup"><span data-stu-id="38a92-122">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="38a92-123">有关配置环境的详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="38a92-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="38a92-124">异常处理程序</span><span class="sxs-lookup"><span data-stu-id="38a92-124">Exception handler</span></span>

<span data-ttu-id="38a92-125">在非开发环境中，可使用[异常处理中间件](xref:fundamentals/error-handling)来生成错误负载：</span><span class="sxs-lookup"><span data-stu-id="38a92-125">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="38a92-126">在 `Startup.Configure` 中，调用 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 以使用中间件：</span><span class="sxs-lookup"><span data-stu-id="38a92-126">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="38a92-127">配置控制器操作以响应 `/error` 路由：</span><span class="sxs-lookup"><span data-stu-id="38a92-127">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="38a92-128">前面的 `Error` 操作向客户端发送与 [RFC7807](https://tools.ietf.org/html/rfc7807) 兼容的负载。</span><span class="sxs-lookup"><span data-stu-id="38a92-128">The preceding `Error` action sends an [RFC7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="38a92-129">异常处理中间件还可以在本地开发环境中提供更详细的内容协商输出。</span><span class="sxs-lookup"><span data-stu-id="38a92-129">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="38a92-130">使用以下步骤在开发和生产环境中生成一致的负载格式：</span><span class="sxs-lookup"><span data-stu-id="38a92-130">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="38a92-131">在 `Startup.Configure` 中，注册特定于环境的异常处理中间件实例：</span><span class="sxs-lookup"><span data-stu-id="38a92-131">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="38a92-132">在上述代码中，通过以下方法注册了中间件：</span><span class="sxs-lookup"><span data-stu-id="38a92-132">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="38a92-133">开发环境中 `/error-local-development` 的路由。</span><span class="sxs-lookup"><span data-stu-id="38a92-133">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="38a92-134">非开发环境中 `/error` 的路由。</span><span class="sxs-lookup"><span data-stu-id="38a92-134">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="38a92-135">将属性路由应用于控制器操作：</span><span class="sxs-lookup"><span data-stu-id="38a92-135">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="38a92-136">使用异常来修改响应</span><span class="sxs-lookup"><span data-stu-id="38a92-136">Use exceptions to modify the response</span></span>

<span data-ttu-id="38a92-137">可以从控制器之外修改响应的内容。</span><span class="sxs-lookup"><span data-stu-id="38a92-137">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="38a92-138">在 ASP.NET 4.x Web API 中，执行此操作的一种方法是使用 <xref:System.Web.Http.HttpResponseException> 类型。</span><span class="sxs-lookup"><span data-stu-id="38a92-138">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="38a92-139">ASP.NET Core 不包括等效类型。</span><span class="sxs-lookup"><span data-stu-id="38a92-139">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="38a92-140">可以使用以下步骤来添加对 `HttpResponseException` 的支持：</span><span class="sxs-lookup"><span data-stu-id="38a92-140">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="38a92-141">创建名为 `HttpResponseException` 的已知异常类型：</span><span class="sxs-lookup"><span data-stu-id="38a92-141">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="38a92-142">创建名为 `HttpResponseExceptionFilter` 的操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="38a92-142">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="38a92-143">在 `Startup.ConfigureServices` 中，将操作筛选器添加到筛选器集合：</span><span class="sxs-lookup"><span data-stu-id="38a92-143">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="38a92-144">验证失败错误响应</span><span class="sxs-lookup"><span data-stu-id="38a92-144">Validation failure error response</span></span>

<span data-ttu-id="38a92-145">对于 Web API 控制器，如果模型验证失败，MVC 将使用 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 响应类型做出响应。</span><span class="sxs-lookup"><span data-stu-id="38a92-145">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="38a92-146">MVC 使用 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> 的结果来构造验证失败的错误响应。</span><span class="sxs-lookup"><span data-stu-id="38a92-146">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="38a92-147">下面的示例使用工厂在 `Startup.ConfigureServices` 中将默认响应类型更改为 <xref:Microsoft.AspNetCore.Mvc.SerializableError>：</span><span class="sxs-lookup"><span data-stu-id="38a92-147">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="38a92-148">客户端错误响应</span><span class="sxs-lookup"><span data-stu-id="38a92-148">Client error response</span></span>

<span data-ttu-id="38a92-149">错误结果定义为具有 HTTP 状态代码 400 或更高的结果。</span><span class="sxs-lookup"><span data-stu-id="38a92-149">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="38a92-150">对于 Web API 控制器，MVC 会将错误结果转换为具有 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 的结果。</span><span class="sxs-lookup"><span data-stu-id="38a92-150">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38a92-151">可以通过以下方式之一配置错误响应：</span><span class="sxs-lookup"><span data-stu-id="38a92-151">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="38a92-152">实现 ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="38a92-152">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="38a92-153">使用 ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="38a92-153">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="38a92-154">实现 ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="38a92-154">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="38a92-155">MVC 使用 `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` 生成 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 和 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 的所有实例。</span><span class="sxs-lookup"><span data-stu-id="38a92-155">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="38a92-156">这包括客户端错误响应、验证失败错误响应以及 `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` 和 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> 帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="38a92-156">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="38a92-157">若要自定义问题详细信息响应，请在 `Startup.ConfigureServices` 中注册 `ProblemDetailsFactory` 的自定义实现：</span><span class="sxs-lookup"><span data-stu-id="38a92-157">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="38a92-158">可以按照[使用 ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) 部分所述的方式配置错误响应。</span><span class="sxs-lookup"><span data-stu-id="38a92-158">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="38a92-159">使用 ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="38a92-159">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="38a92-160">使用 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> 属性配置 `ProblemDetails` 响应的内容。</span><span class="sxs-lookup"><span data-stu-id="38a92-160">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="38a92-161">例如，`Startup.ConfigureServices` 中的以下代码会更新 404 响应的 `type` 属性：</span><span class="sxs-lookup"><span data-stu-id="38a92-161">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end