---
title: Migrar módulos e manipuladores HTTP para ASP.NET Core middleware
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: bdf27ccb742d4bc05bac71e6c96d71c38dcb4b62
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975488"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="69d3f-102">Migrar módulos e manipuladores HTTP para ASP.NET Core middleware</span><span class="sxs-lookup"><span data-stu-id="69d3f-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="69d3f-103">Este artigo mostra como migrar [módulos e manipuladores HTTP ASP.net existentes de System. WebServer](/iis/configuration/system.webserver/) para ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="69d3f-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="69d3f-104">Módulos e manipuladores revisitados</span><span class="sxs-lookup"><span data-stu-id="69d3f-104">Modules and handlers revisited</span></span>

<span data-ttu-id="69d3f-105">Antes de prosseguir para ASP.NET Core middleware, vamos primeiro recapitular como funcionam os módulos e manipuladores HTTP:</span><span class="sxs-lookup"><span data-stu-id="69d3f-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Manipulador de módulos](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="69d3f-107">**Os manipuladores são:**</span><span class="sxs-lookup"><span data-stu-id="69d3f-107">**Handlers are:**</span></span>

* <span data-ttu-id="69d3f-108">Classes que implementam [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="69d3f-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="69d3f-109">Usado para lidar com solicitações com um determinado nome de arquivo ou extensão, como *. Report*</span><span class="sxs-lookup"><span data-stu-id="69d3f-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="69d3f-110">[Configurado](/iis/configuration/system.webserver/handlers/) no *Web. config*</span><span class="sxs-lookup"><span data-stu-id="69d3f-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="69d3f-111">**Os módulos são:**</span><span class="sxs-lookup"><span data-stu-id="69d3f-111">**Modules are:**</span></span>

* <span data-ttu-id="69d3f-112">Classes que implementam [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="69d3f-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="69d3f-113">Invocado para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="69d3f-113">Invoked for every request</span></span>

* <span data-ttu-id="69d3f-114">Capacidade de curto-circuito (interromper o processamento de uma solicitação)</span><span class="sxs-lookup"><span data-stu-id="69d3f-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="69d3f-115">Capaz de adicionar à resposta HTTP ou criar suas próprias</span><span class="sxs-lookup"><span data-stu-id="69d3f-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="69d3f-116">[Configurado](/iis/configuration/system.webserver/modules/) no *Web. config*</span><span class="sxs-lookup"><span data-stu-id="69d3f-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="69d3f-117">**A ordem na qual os módulos processam solicitações de entrada é determinada por:**</span><span class="sxs-lookup"><span data-stu-id="69d3f-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="69d3f-118">O [ciclo de vida do aplicativo](https://msdn.microsoft.com/library/ms227673.aspx), que é uma série de eventos acionados por ASP.net: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Cada módulo pode criar um manipulador para um ou mais eventos.</span><span class="sxs-lookup"><span data-stu-id="69d3f-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="69d3f-119">Para o mesmo evento, a ordem na qual eles são configurados em *Web. config*.</span><span class="sxs-lookup"><span data-stu-id="69d3f-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="69d3f-120">Além dos módulos, você pode adicionar manipuladores para os eventos do ciclo de vida ao arquivo *global.asax.cs* .</span><span class="sxs-lookup"><span data-stu-id="69d3f-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="69d3f-121">Esses manipuladores são executados após os manipuladores nos módulos configurados.</span><span class="sxs-lookup"><span data-stu-id="69d3f-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="69d3f-122">De manipuladores e módulos para middleware</span><span class="sxs-lookup"><span data-stu-id="69d3f-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="69d3f-123">**O middleware é mais simples do que módulos e manipuladores HTTP:**</span><span class="sxs-lookup"><span data-stu-id="69d3f-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="69d3f-124">Módulos, manipuladores, *global.asax.cs*, *Web. config* (exceto para a configuração do IIS) e o ciclo de vida do aplicativo foram eliminados</span><span class="sxs-lookup"><span data-stu-id="69d3f-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="69d3f-125">As funções dos módulos e dos manipuladores foram tomadas pelo middleware</span><span class="sxs-lookup"><span data-stu-id="69d3f-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="69d3f-126">O middleware é configurado usando código em vez de *Web. config*</span><span class="sxs-lookup"><span data-stu-id="69d3f-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="69d3f-127">O [Branch de pipeline](xref:fundamentals/middleware/index#use-run-and-map) permite enviar solicitações para middleware específico, com base na URL, mas também em cabeçalhos de solicitação, cadeias de consulta, etc.</span><span class="sxs-lookup"><span data-stu-id="69d3f-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="69d3f-128">**Os middleware são muito semelhantes aos módulos:**</span><span class="sxs-lookup"><span data-stu-id="69d3f-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="69d3f-129">Invocado em princípio para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="69d3f-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="69d3f-130">Capacidade de curto-circuito de uma solicitação, [não passando a solicitação para o próximo middleware](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="69d3f-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="69d3f-131">Capaz de criar sua própria resposta HTTP</span><span class="sxs-lookup"><span data-stu-id="69d3f-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="69d3f-132">**Os módulos e middleware são processados em uma ordem diferente:**</span><span class="sxs-lookup"><span data-stu-id="69d3f-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="69d3f-133">A ordem do middleware baseia-se na ordem em que eles são inseridos no pipeline de solicitação, enquanto a ordem dos módulos é principalmente baseada nos eventos do [ciclo de vida do aplicativo](https://msdn.microsoft.com/library/ms227673.aspx)</span><span class="sxs-lookup"><span data-stu-id="69d3f-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="69d3f-134">A ordem do middleware para respostas é o inverso do para solicitações, enquanto a ordem dos módulos é a mesma para solicitações e respostas</span><span class="sxs-lookup"><span data-stu-id="69d3f-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="69d3f-135">Confira [criar um pipeline de middleware com IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="69d3f-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="69d3f-137">Observe como na imagem acima, o middleware de autenticação curto-circuitou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="69d3f-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="69d3f-138">Migrando o código do módulo para o middleware</span><span class="sxs-lookup"><span data-stu-id="69d3f-138">Migrating module code to middleware</span></span>

<span data-ttu-id="69d3f-139">Um módulo HTTP existente terá uma aparência semelhante a esta:</span><span class="sxs-lookup"><span data-stu-id="69d3f-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="69d3f-140">Conforme mostrado na página [middleware](xref:fundamentals/middleware/index) , um middleware ASP.NET Core é uma classe que expõe um `Invoke` método que leva `HttpContext` um e retornando um `Task`.</span><span class="sxs-lookup"><span data-stu-id="69d3f-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="69d3f-141">Seu novo middleware terá a seguinte aparência:</span><span class="sxs-lookup"><span data-stu-id="69d3f-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="69d3f-142">O modelo de middleware anterior foi tirado da seção sobre o [middleware de escrita](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="69d3f-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="69d3f-143">A classe auxiliar *MyMiddlewareExtensions* torna mais fácil configurar seu middleware em sua `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="69d3f-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="69d3f-144">O `UseMyMiddleware` método adiciona a classe de middleware ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="69d3f-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="69d3f-145">Os serviços exigidos pelo middleware são injetados no construtor do middleware.</span><span class="sxs-lookup"><span data-stu-id="69d3f-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="69d3f-146">O módulo pode encerrar uma solicitação, por exemplo, se o usuário não estiver autorizado:</span><span class="sxs-lookup"><span data-stu-id="69d3f-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="69d3f-147">Um middleware trata isso não chamando `Invoke` no próximo middleware no pipeline.</span><span class="sxs-lookup"><span data-stu-id="69d3f-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="69d3f-148">Lembre-se de que isso não encerra completamente a solicitação, porque o middleware anterior ainda será invocado quando a resposta voltar ao pipeline.</span><span class="sxs-lookup"><span data-stu-id="69d3f-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="69d3f-149">Ao migrar a funcionalidade do módulo para o novo middleware, você pode achar que seu código não é compilado porque a `HttpContext` classe mudou significativamente em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="69d3f-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="69d3f-150">[Posteriormente](#migrating-to-the-new-httpcontext), você verá como migrar para o novo ASP.NET Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="69d3f-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="69d3f-151">Migrando a inserção de módulo para o pipeline de solicitação</span><span class="sxs-lookup"><span data-stu-id="69d3f-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="69d3f-152">Módulos HTTP são normalmente adicionados ao pipeline de solicitação usando *Web. config*:</span><span class="sxs-lookup"><span data-stu-id="69d3f-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="69d3f-153">Converta isso [adicionando o novo middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) ao pipeline de solicitação em sua `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="69d3f-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="69d3f-154">O ponto exato no pipeline em que você insere o novo middleware depende do evento que ele tratou de um módulo (`BeginRequest`, `EndRequest`etc.) e de sua ordem na lista de módulos em *Web. config*.</span><span class="sxs-lookup"><span data-stu-id="69d3f-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="69d3f-155">Como mencionado anteriormente, não há nenhum ciclo de vida do aplicativo em ASP.NET Core e a ordem em que as respostas são processadas pelo middleware difere da ordem usada pelos módulos.</span><span class="sxs-lookup"><span data-stu-id="69d3f-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="69d3f-156">Isso pode tornar sua decisão de pedido mais desafiadora.</span><span class="sxs-lookup"><span data-stu-id="69d3f-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="69d3f-157">Se a ordenação se tornar um problema, você poderá dividir o módulo em vários componentes de middleware que podem ser ordenados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="69d3f-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="69d3f-158">Migrando o código do manipulador para o middleware</span><span class="sxs-lookup"><span data-stu-id="69d3f-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="69d3f-159">Um manipulador HTTP é semelhante a este:</span><span class="sxs-lookup"><span data-stu-id="69d3f-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="69d3f-160">Em seu projeto de ASP.NET Core, você converteria isso em um middleware semelhante a este:</span><span class="sxs-lookup"><span data-stu-id="69d3f-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="69d3f-161">Esse middleware é muito semelhante ao middleware correspondente aos módulos.</span><span class="sxs-lookup"><span data-stu-id="69d3f-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="69d3f-162">A única diferença real é que aqui não há nenhuma chamada para `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="69d3f-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="69d3f-163">Isso faz sentido, porque o manipulador está no final do pipeline de solicitação, portanto, não haverá o próximo middleware a ser invocado.</span><span class="sxs-lookup"><span data-stu-id="69d3f-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="69d3f-164">Migrando a inserção do manipulador para o pipeline de solicitação</span><span class="sxs-lookup"><span data-stu-id="69d3f-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="69d3f-165">A configuração de um manipulador HTTP é feita em *Web. config* e é semelhante a esta:</span><span class="sxs-lookup"><span data-stu-id="69d3f-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="69d3f-166">Você pode converter isso adicionando o novo middleware do manipulador ao pipeline de solicitação em sua `Startup` classe, semelhante ao middleware convertido de módulos.</span><span class="sxs-lookup"><span data-stu-id="69d3f-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="69d3f-167">O problema dessa abordagem é que ele enviaria todas as solicitações ao seu novo middleware de manipulador.</span><span class="sxs-lookup"><span data-stu-id="69d3f-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="69d3f-168">No entanto, você só deseja que as solicitações com uma determinada extensão acessem o middleware.</span><span class="sxs-lookup"><span data-stu-id="69d3f-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="69d3f-169">Isso forneceria a mesma funcionalidade que você tinha com o manipulador HTTP.</span><span class="sxs-lookup"><span data-stu-id="69d3f-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="69d3f-170">Uma solução é ramificar o pipeline para solicitações com uma determinada extensão, usando o `MapWhen` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="69d3f-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="69d3f-171">Você faz isso no mesmo `Configure` método em que adiciona o outro middleware:</span><span class="sxs-lookup"><span data-stu-id="69d3f-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="69d3f-172">`MapWhen`usa estes parâmetros:</span><span class="sxs-lookup"><span data-stu-id="69d3f-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="69d3f-173">Um lambda que usa `HttpContext` e retorna `true` se a solicitação deve descer o Branch.</span><span class="sxs-lookup"><span data-stu-id="69d3f-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="69d3f-174">Isso significa que você pode ramificar solicitações não apenas com base em sua extensão, mas também em cabeçalhos de solicitação, parâmetros de cadeia de caracteres de consulta, etc.</span><span class="sxs-lookup"><span data-stu-id="69d3f-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="69d3f-175">Um lambda que usa um `IApplicationBuilder` e adiciona todo o middleware para a ramificação.</span><span class="sxs-lookup"><span data-stu-id="69d3f-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="69d3f-176">Isso significa que você pode adicionar middleware adicional à ramificação na frente do middleware do manipulador.</span><span class="sxs-lookup"><span data-stu-id="69d3f-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="69d3f-177">Middleware adicionado ao pipeline antes que a ramificação seja invocada em todas as solicitações; o Branch não terá nenhum impacto sobre eles.</span><span class="sxs-lookup"><span data-stu-id="69d3f-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="69d3f-178">Carregando opções de middleware usando o padrão de opções</span><span class="sxs-lookup"><span data-stu-id="69d3f-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="69d3f-179">Alguns módulos e manipuladores têm opções de configuração que são armazenadas em *Web. config*. No entanto, no ASP.NET Core um novo modelo de configuração é usado no lugar de *Web. config*.</span><span class="sxs-lookup"><span data-stu-id="69d3f-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="69d3f-180">O novo [sistema de configuração](xref:fundamentals/configuration/index) oferece essas opções para resolver isso:</span><span class="sxs-lookup"><span data-stu-id="69d3f-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="69d3f-181">Insira diretamente as opções no middleware, conforme mostrado na [próxima seção](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="69d3f-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="69d3f-182">Use o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="69d3f-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="69d3f-183">Crie uma classe para manter suas opções de middleware, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="69d3f-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="69d3f-184">Armazenar os valores de opção</span><span class="sxs-lookup"><span data-stu-id="69d3f-184">Store the option values</span></span>

   <span data-ttu-id="69d3f-185">O sistema de configuração permite que você armazene valores de opção em qualquer lugar que desejar.</span><span class="sxs-lookup"><span data-stu-id="69d3f-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="69d3f-186">No entanto, a maioria dos sites usa *appSettings. JSON*, portanto, seguiremos essa abordagem:</span><span class="sxs-lookup"><span data-stu-id="69d3f-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="69d3f-187">*MyMiddlewareOptionsSection* aqui é um nome de seção.</span><span class="sxs-lookup"><span data-stu-id="69d3f-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="69d3f-188">Ele não precisa ser o mesmo que o nome da sua classe Options.</span><span class="sxs-lookup"><span data-stu-id="69d3f-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="69d3f-189">Associar os valores de opção à classe Options</span><span class="sxs-lookup"><span data-stu-id="69d3f-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="69d3f-190">O padrão de opções usa a estrutura de injeção de dependência do ASP.NET Core para associar o tipo `MyMiddlewareOptions`de opções ( `MyMiddlewareOptions` como) a um objeto que tem as opções reais.</span><span class="sxs-lookup"><span data-stu-id="69d3f-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="69d3f-191">Atualize sua `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="69d3f-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="69d3f-192">Se você estiver usando *appSettings. JSON*, adicione-o ao construtor de configuração no `Startup` Construtor:</span><span class="sxs-lookup"><span data-stu-id="69d3f-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="69d3f-193">Configurar o serviço de opções:</span><span class="sxs-lookup"><span data-stu-id="69d3f-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="69d3f-194">Associe suas opções à sua classe Options:</span><span class="sxs-lookup"><span data-stu-id="69d3f-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="69d3f-195">Insira as opções em seu construtor de middleware.</span><span class="sxs-lookup"><span data-stu-id="69d3f-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="69d3f-196">Isso é semelhante a injetar opções em um controlador.</span><span class="sxs-lookup"><span data-stu-id="69d3f-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="69d3f-197">O método de extensão [UseMiddleware](#http-modules-usemiddleware) que adiciona seu middleware ao `IApplicationBuilder` cuida da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="69d3f-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="69d3f-198">Isso não é limitado `IOptions` a objetos.</span><span class="sxs-lookup"><span data-stu-id="69d3f-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="69d3f-199">Qualquer outro objeto que seu middleware requer pode ser injetado dessa maneira.</span><span class="sxs-lookup"><span data-stu-id="69d3f-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="69d3f-200">Carregando opções de middleware por meio de injeção direta</span><span class="sxs-lookup"><span data-stu-id="69d3f-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="69d3f-201">O padrão de opções tem a vantagem de que ele cria um acoplamento flexível entre valores de opções e seus consumidores.</span><span class="sxs-lookup"><span data-stu-id="69d3f-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="69d3f-202">Depois que você tiver associado uma classe Options com os valores de opções reais, qualquer outra classe poderá obter acesso às opções por meio da estrutura de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="69d3f-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="69d3f-203">Não há necessidade de passar pelos valores de opções.</span><span class="sxs-lookup"><span data-stu-id="69d3f-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="69d3f-204">Isso é interrompido no entanto, se você quiser usar o mesmo middleware duas vezes, com opções diferentes.</span><span class="sxs-lookup"><span data-stu-id="69d3f-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="69d3f-205">Por exemplo, um middleware de autorização usado em diferentes ramificações que permitem diferentes funções.</span><span class="sxs-lookup"><span data-stu-id="69d3f-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="69d3f-206">Você não pode associar dois objetos de opções diferentes com a classe One Options.</span><span class="sxs-lookup"><span data-stu-id="69d3f-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="69d3f-207">A solução é obter os objetos de opções com os valores de opções reais em `Startup` sua classe e passá-los diretamente para cada instância do middleware.</span><span class="sxs-lookup"><span data-stu-id="69d3f-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="69d3f-208">Adicionar uma segunda chave para *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="69d3f-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="69d3f-209">Para adicionar um segundo conjunto de opções ao arquivo *appSettings. JSON* , use uma nova chave para identificá-la exclusivamente:</span><span class="sxs-lookup"><span data-stu-id="69d3f-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="69d3f-210">Recuperar valores de opções e passá-los para o middleware.</span><span class="sxs-lookup"><span data-stu-id="69d3f-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="69d3f-211">O `Use...` método de extensão (que adiciona o middleware ao pipeline) é um local lógico para passar os valores de opção:</span><span class="sxs-lookup"><span data-stu-id="69d3f-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="69d3f-212">Habilite o middleware para obter um parâmetro de opções.</span><span class="sxs-lookup"><span data-stu-id="69d3f-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="69d3f-213">Forneça uma sobrecarga do método `Use...` de extensão (que usa o parâmetro options e o transmite para `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="69d3f-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="69d3f-214">Quando `UseMiddleware` é chamado com parâmetros, ele passa os parâmetros para o construtor de middleware quando cria uma instância do objeto de middleware.</span><span class="sxs-lookup"><span data-stu-id="69d3f-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="69d3f-215">Observe como isso encapsula o objeto Options em um `OptionsWrapper` objeto.</span><span class="sxs-lookup"><span data-stu-id="69d3f-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="69d3f-216">Isso implementa `IOptions`, conforme esperado pelo construtor de middleware.</span><span class="sxs-lookup"><span data-stu-id="69d3f-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="69d3f-217">Migrando para o novo HttpContext</span><span class="sxs-lookup"><span data-stu-id="69d3f-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="69d3f-218">Vimos anteriormente que o `Invoke` método em seu middleware usa um parâmetro do tipo: `HttpContext`</span><span class="sxs-lookup"><span data-stu-id="69d3f-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="69d3f-219">`HttpContext`mudou significativamente em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="69d3f-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="69d3f-220">Esta seção mostra como converter as propriedades usadas com mais frequência de [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) para o novo `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="69d3f-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="69d3f-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="69d3f-221">HttpContext</span></span>

<span data-ttu-id="69d3f-222">**HttpContext. Items** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="69d3f-223">**ID de solicitação exclusiva (sem equivalente de System. Web. HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="69d3f-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="69d3f-224">Fornece uma ID exclusiva para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="69d3f-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="69d3f-225">Muito útil para incluir em seus logs.</span><span class="sxs-lookup"><span data-stu-id="69d3f-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="69d3f-226">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="69d3f-226">HttpContext.Request</span></span>

<span data-ttu-id="69d3f-227">**HttpContext. Request. HttpMethod** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="69d3f-228">**HttpContext. Request. QueryString** traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="69d3f-229">**HttpContext. Request. URL** e **HttpContext. Request. RawUrl** traduzir para:</span><span class="sxs-lookup"><span data-stu-id="69d3f-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="69d3f-230">**HttpContext. Request. IsSecureConnection** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="69d3f-231">**HttpContext. Request. UserHostAddress** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="69d3f-232">**HttpContext. Request. cookies** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="69d3f-233">**HttpContext. Request. RequestContext. RouteData** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="69d3f-234">**HttpContext. Request. Headers** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="69d3f-235">**HttpContext. Request. UserAgent** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="69d3f-236">**HttpContext. Request. UrlReferrer** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="69d3f-237">**HttpContext. Request. ContentType** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="69d3f-238">**HttpContext. Request. Form** é convertido em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="69d3f-239">Leia valores de formulário somente se o subtipo de conteúdo for *x-www-form-urlencoded* ou *form-data*.</span><span class="sxs-lookup"><span data-stu-id="69d3f-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="69d3f-240">**HttpContext. Request. InputStream** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="69d3f-241">Use este código somente em um middleware de tipo de manipulador, no final de um pipeline.</span><span class="sxs-lookup"><span data-stu-id="69d3f-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="69d3f-242">Você pode ler o corpo bruto, conforme mostrado acima apenas uma vez por solicitação.</span><span class="sxs-lookup"><span data-stu-id="69d3f-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="69d3f-243">O middleware tentando ler o corpo após a primeira leitura lerá um corpo vazio.</span><span class="sxs-lookup"><span data-stu-id="69d3f-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="69d3f-244">Isso não se aplica à leitura de um formulário, como mostrado anteriormente, porque isso é feito de um buffer.</span><span class="sxs-lookup"><span data-stu-id="69d3f-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="69d3f-245">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="69d3f-245">HttpContext.Response</span></span>

<span data-ttu-id="69d3f-246">**HttpContext. Response. status** e **HttpContext. Response. StatusDescription** são convertidos em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="69d3f-247">**HttpContext. Response. ContentEncoding** e **HttpContext. Response. ContentType** são convertidos em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="69d3f-248">**HttpContext. Response. ContentType** em seu próprio também se traduz em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="69d3f-249">**HttpContext. Response. Output** é convertido em:</span><span class="sxs-lookup"><span data-stu-id="69d3f-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="69d3f-250">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="69d3f-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="69d3f-251">O fornecimento de um arquivo é discutido [aqui](../fundamentals/request-features.md#middleware-and-request-features).</span><span class="sxs-lookup"><span data-stu-id="69d3f-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="69d3f-252">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="69d3f-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="69d3f-253">O envio de cabeçalhos de resposta é complicado pelo fato de que, se você defini-los depois que algo tiver sido gravado no corpo da resposta, eles não serão enviados.</span><span class="sxs-lookup"><span data-stu-id="69d3f-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="69d3f-254">A solução é definir um método de retorno de chamada que será chamado imediatamente antes de gravar na resposta começar.</span><span class="sxs-lookup"><span data-stu-id="69d3f-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="69d3f-255">Isso é melhor feito no início do `Invoke` método em seu middleware.</span><span class="sxs-lookup"><span data-stu-id="69d3f-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="69d3f-256">Esse é o método de retorno de chamada que define seus cabeçalhos de resposta.</span><span class="sxs-lookup"><span data-stu-id="69d3f-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="69d3f-257">O código a seguir define um método de `SetHeaders`retorno de chamada chamado:</span><span class="sxs-lookup"><span data-stu-id="69d3f-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="69d3f-258">O `SetHeaders` método de retorno de chamada ficaria assim:</span><span class="sxs-lookup"><span data-stu-id="69d3f-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="69d3f-259">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="69d3f-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="69d3f-260">Os cookies viajam para o navegador em um cabeçalho de resposta *Set-cookie* .</span><span class="sxs-lookup"><span data-stu-id="69d3f-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="69d3f-261">Como resultado, o envio de cookies requer o mesmo retorno de chamada usado para enviar cabeçalhos de resposta:</span><span class="sxs-lookup"><span data-stu-id="69d3f-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="69d3f-262">O `SetCookies` método de retorno de chamada seria semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="69d3f-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="69d3f-263">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="69d3f-263">Additional resources</span></span>

* [<span data-ttu-id="69d3f-264">Visão geral de manipuladores HTTP e módulos HTTP</span><span class="sxs-lookup"><span data-stu-id="69d3f-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="69d3f-265">Configuração</span><span class="sxs-lookup"><span data-stu-id="69d3f-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="69d3f-266">Inicialização de aplicativos</span><span class="sxs-lookup"><span data-stu-id="69d3f-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="69d3f-267">Middleware</span><span class="sxs-lookup"><span data-stu-id="69d3f-267">Middleware</span></span>](xref:fundamentals/middleware/index)
