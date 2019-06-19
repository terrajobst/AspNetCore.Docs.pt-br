---
title: Migrar módulos e manipuladores HTTP para middleware do ASP.NET Core
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 84381210910c66a7d121120b8c6b0f046cae8c4f
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207799"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="c7dd1-102">Migrar módulos e manipuladores HTTP para middleware do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7dd1-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="c7dd1-103">Este artigo mostra como migrar do ASP.NET existentes [módulos HTTP e manipuladores de System. webServer](/iis/configuration/system.webserver/) para o ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="c7dd1-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="c7dd1-104">Módulos e manipuladores revistos</span><span class="sxs-lookup"><span data-stu-id="c7dd1-104">Modules and handlers revisited</span></span>

<span data-ttu-id="c7dd1-105">Antes de prosseguir para o middleware do ASP.NET Core, vamos primeiro recapitular como funcionam os manipuladores e módulos HTTP:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Manipulador de módulos](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="c7dd1-107">**Os manipuladores são:**</span><span class="sxs-lookup"><span data-stu-id="c7dd1-107">**Handlers are:**</span></span>

* <span data-ttu-id="c7dd1-108">As classes que implementam [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="c7dd1-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="c7dd1-109">Usado para manipular solicitações com um determinado nome de arquivo ou a extensão, como *.report*</span><span class="sxs-lookup"><span data-stu-id="c7dd1-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="c7dd1-110">[Configurado](/iis/configuration/system.webserver/handlers/) em *Web. config*</span><span class="sxs-lookup"><span data-stu-id="c7dd1-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="c7dd1-111">**Módulos são:**</span><span class="sxs-lookup"><span data-stu-id="c7dd1-111">**Modules are:**</span></span>

* <span data-ttu-id="c7dd1-112">As classes que implementam [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="c7dd1-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="c7dd1-113">Chamado para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="c7dd1-113">Invoked for every request</span></span>

* <span data-ttu-id="c7dd1-114">Capazes de curto-circuito (interromper o processamento adicional de uma solicitação)</span><span class="sxs-lookup"><span data-stu-id="c7dd1-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="c7dd1-115">Capaz de adicionar à resposta HTTP, ou criar seus próprios</span><span class="sxs-lookup"><span data-stu-id="c7dd1-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="c7dd1-116">[Configurado](/iis/configuration/system.webserver/modules/) em *Web. config*</span><span class="sxs-lookup"><span data-stu-id="c7dd1-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="c7dd1-117">**A ordem na qual os módulos de processam solicitações de entrada é determinada por:**</span><span class="sxs-lookup"><span data-stu-id="c7dd1-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="c7dd1-118">O [ciclo de vida do aplicativo](https://msdn.microsoft.com/library/ms227673.aspx), que é disparado pelo ASP.NET um eventos da série: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Cada módulo pode criar um manipulador para um ou mais eventos.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="c7dd1-119">Para o mesmo evento, a ordem na qual eles foram configurados na *Web. config*.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="c7dd1-120">Além de módulos, você pode adicionar manipuladores para os eventos de ciclo de vida para seus *Global.asax.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="c7dd1-121">Esses manipuladores executar após os manipuladores nos módulos configurados.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="c7dd1-122">De manipuladores e módulos para middleware</span><span class="sxs-lookup"><span data-stu-id="c7dd1-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="c7dd1-123">**Middleware são mais simples do que os manipuladores e módulos HTTP:**</span><span class="sxs-lookup"><span data-stu-id="c7dd1-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="c7dd1-124">Módulos, manipuladores *Global.asax.cs*, *Web. config* (exceto para a configuração do IIS) e o ciclo de vida do aplicativo serão excluídos</span><span class="sxs-lookup"><span data-stu-id="c7dd1-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="c7dd1-125">As funções dos módulos e manipuladores foram tomadas a tecla TAB middleware</span><span class="sxs-lookup"><span data-stu-id="c7dd1-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="c7dd1-126">Middleware são configurados usando o código em vez de em *Web. config*</span><span class="sxs-lookup"><span data-stu-id="c7dd1-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="c7dd1-127">[Ramificação do pipeline](xref:fundamentals/middleware/index#use-run-and-map) permite que você envia solicitações para o middleware específico, com base em não apenas a URL, mas também em cabeçalhos de solicitação, cadeias de caracteres de consulta, etc.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="c7dd1-128">**Middleware são muito semelhantes aos módulos:**</span><span class="sxs-lookup"><span data-stu-id="c7dd1-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="c7dd1-129">Invocado em princípio, para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="c7dd1-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="c7dd1-130">Capazes de curto-circuito a uma solicitação por [não passar a solicitação para o próximo middleware](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="c7dd1-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="c7dd1-131">Capaz de criar sua própria resposta HTTP</span><span class="sxs-lookup"><span data-stu-id="c7dd1-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="c7dd1-132">**Middleware e módulos são processados em uma ordem diferente:**</span><span class="sxs-lookup"><span data-stu-id="c7dd1-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="c7dd1-133">Pedido de middleware é baseado na ordem em que eles são ser inseridos no pipeline de solicitação, enquanto a ordem dos módulos se baseia principalmente na [ciclo de vida do aplicativo](https://msdn.microsoft.com/library/ms227673.aspx) eventos</span><span class="sxs-lookup"><span data-stu-id="c7dd1-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="c7dd1-134">Ordem de middleware para respostas é o inverso do que para solicitações, enquanto a ordem dos módulos é o mesmo para solicitações e respostas</span><span class="sxs-lookup"><span data-stu-id="c7dd1-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="c7dd1-135">Consulte [criar um pipeline de middleware com o IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="c7dd1-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="c7dd1-137">Observe como na imagem acima, o middleware de autenticação sofrido curto-circuito a solicitação.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="c7dd1-138">Migrando o código de módulo para middleware</span><span class="sxs-lookup"><span data-stu-id="c7dd1-138">Migrating module code to middleware</span></span>

<span data-ttu-id="c7dd1-139">Um módulo HTTP existente será semelhante a este:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="c7dd1-140">Conforme mostrado na [Middleware](xref:fundamentals/middleware/index) página, um middleware do ASP.NET Core é uma classe que expõe um `Invoke` levando método um `HttpContext` e retornando um `Task`.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="c7dd1-141">Seu novo middleware terá esta aparência:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="c7dd1-142">O modelo de middleware anterior foi obtido da seção em [escrever middleware](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="c7dd1-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="c7dd1-143">O *MyMiddlewareExtensions* classe auxiliar torna mais fácil de configurar seu middleware em seu `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="c7dd1-144">O `UseMyMiddleware` método adiciona sua classe de middleware ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="c7dd1-145">Serviços necessários para o middleware são injetados no construtor do middleware.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="c7dd1-146">O módulo pode encerrar uma solicitação, por exemplo, se o usuário não autorizado:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="c7dd1-147">Um middleware lida com isso, não chamando `Invoke` no próximo middleware no pipeline.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="c7dd1-148">Tenha em mente que isso não encerra completamente a solicitação, porque o middleware anterior ainda será chamado quando a resposta faz seu caminho através do pipeline.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="c7dd1-149">Ao migrar a funcionalidade do módulo para seu novo middleware, você pode achar que seu código não era compilado porque o `HttpContext` classe foi alterado significativamente no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="c7dd1-150">[Mais tarde](#migrating-to-the-new-httpcontext), você verá como migrar para ASP.NET Core HttpContext novo.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="c7dd1-151">Migrando inserção de módulo no pipeline de solicitação</span><span class="sxs-lookup"><span data-stu-id="c7dd1-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="c7dd1-152">Módulos HTTP normalmente são adicionados ao pipeline de solicitação usando *Web. config*:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="c7dd1-153">Converter isso por [adicionando seu novo middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) ao pipeline de solicitação no seu `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="c7dd1-154">O ponto exato no pipeline de onde você insere seu middleware novo depende do evento que ele tratado como um módulo (`BeginRequest`, `EndRequest`, etc.) e sua ordem na sua lista de módulos *Web. config*.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="c7dd1-155">Conforme anteriormente não mencionado, há mais nenhum ciclo de vida do aplicativo no ASP.NET Core e a ordem na qual as respostas são processadas pelo middleware é diferente da ordem usada pelos módulos.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="c7dd1-156">Isso pode tornar sua decisão de ordenação mais desafiador.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="c7dd1-157">Se a ordenação se torna um problema, você pode dividir seu módulo em vários componentes de middleware que podem ser ordenados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="c7dd1-158">Migrando o código de manipulador para middleware</span><span class="sxs-lookup"><span data-stu-id="c7dd1-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="c7dd1-159">Um manipulador HTTP é semelhante a:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="c7dd1-160">Em seu projeto ASP.NET Core, isso pode ser traduzido para um middleware semelhante a esta:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="c7dd1-161">Este middleware é muito semelhante ao middleware correspondente aos módulos.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="c7dd1-162">A única diferença é que não há aqui nenhuma chamada para `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="c7dd1-163">Isso faz sentido, porque o manipulador não é no final do pipeline de solicitação, portanto, não haverá nenhum próximo middleware para invocar.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="c7dd1-164">Migrando inserção manipulador no pipeline de solicitação</span><span class="sxs-lookup"><span data-stu-id="c7dd1-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="c7dd1-165">Configurar um manipulador HTTP é feito na *Web. config* e pode ter esta aparência:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="c7dd1-166">Você pode converter isso adicionando seu novo middleware de manipulador para o pipeline de solicitação no seu `Startup` classe, semelhante ao middleware convertido de módulos.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="c7dd1-167">O problema com essa abordagem é que ele seria enviar todas as solicitações para seu novo middleware de manipulador.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="c7dd1-168">No entanto, você precisará apenas solicitações com uma determinada extensão para alcançar seu middleware.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="c7dd1-169">Essa seria a mesma funcionalidade que tinha com seu manipulador HTTP.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="c7dd1-170">Uma solução é ramificar o pipeline para solicitações com uma determinada extensão, usando o `MapWhen` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="c7dd1-171">Você pode fazer isso no mesmo `Configure` método onde você pode adicionar o outro middleware:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="c7dd1-172">`MapWhen` usa esses parâmetros:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="c7dd1-173">Um lambda que usa o `HttpContext` e retorna `true` se a solicitação deve ir para baixo a ramificação.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="c7dd1-174">Isso significa que você pode ramificar solicitações não apenas com base em sua extensão, mas também em cabeçalhos de solicitação, parâmetros de cadeia de caracteres de consulta, etc.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="c7dd1-175">Um lambda que usa um `IApplicationBuilder` e adiciona o middleware para a ramificação.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="c7dd1-176">Isso significa que você pode adicionar outro middleware para a ramificação na frente do seu manipulador middleware.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="c7dd1-177">Middleware adicionado ao pipeline antes da ramificação será invocada em todas as solicitações; a ramificação não terá impacto sobre eles.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="c7dd1-178">Opções de middleware usando o padrão de opções de carregamento</span><span class="sxs-lookup"><span data-stu-id="c7dd1-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="c7dd1-179">Alguns manipuladores e módulos têm opções de configuração que são armazenadas em *Web. config*. No entanto, no ASP.NET Core um novo modelo de configuração é usado no lugar de *Web. config*.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="c7dd1-180">O novo [sistema de configuração](xref:fundamentals/configuration/index) fornece essas opções para resolver isso:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="c7dd1-181">Injetar diretamente as opções para o middleware, como mostra a [próxima seção](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="c7dd1-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="c7dd1-182">Use o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="c7dd1-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="c7dd1-183">Crie uma classe para manter suas opções de middleware, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="c7dd1-184">Store os valores de opção</span><span class="sxs-lookup"><span data-stu-id="c7dd1-184">Store the option values</span></span>

   <span data-ttu-id="c7dd1-185">O sistema de configuração permite que você armazene valores de opção em qualquer local desejado.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="c7dd1-186">No entanto, a maioria dos locais use *appSettings. JSON*, portanto, vamos dar essa abordagem:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="c7dd1-187">*MyMiddlewareOptionsSection* aqui é um nome de seção.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="c7dd1-188">Ele não precisa ser igual ao nome da sua classe de opções.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="c7dd1-189">Associar os valores de opção com a classe de opções</span><span class="sxs-lookup"><span data-stu-id="c7dd1-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="c7dd1-190">O padrão de opções usa a estrutura de injeção de dependência do ASP.NET Core para associar o tipo de opções (como `MyMiddlewareOptions`) com um `MyMiddlewareOptions` objeto que tem as opções reais.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="c7dd1-191">Atualização de seu `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="c7dd1-192">Se você estiver usando *appSettings. JSON*, adicione-o ao construtor de configuração no `Startup` construtor:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="c7dd1-193">Configure o serviço de opções:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="c7dd1-194">Associe suas opções de sua classe de opções:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="c7dd1-195">Injete as opções para o construtor de middleware.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="c7dd1-196">Isso é semelhante a injeção de opções em um controlador.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="c7dd1-197">O [UseMiddleware](#http-modules-usemiddleware) método de extensão que adiciona o middleware para o `IApplicationBuilder` se encarrega de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="c7dd1-198">Isso não é limitado a `IOptions` objetos.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="c7dd1-199">Qualquer outro objeto requer que seu middleware pode ser injetado dessa maneira.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="c7dd1-200">Opções de middleware por meio de injeção direta de carregamento</span><span class="sxs-lookup"><span data-stu-id="c7dd1-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="c7dd1-201">O padrão de opções tem a vantagem que ele cria o acoplamento solto entre valores de opções e seus consumidores.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="c7dd1-202">Depois que você associou a uma classe de opções com os valores de opções propriamente dito, qualquer outra classe pode obter acesso às opções por meio da estrutura de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="c7dd1-203">Não é necessário para passar ao redor de valores de opções.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="c7dd1-204">Isso divide entanto se você quiser usar o mesmo middleware duas vezes, com opções diferentes.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="c7dd1-205">Por exemplo um middleware de autorização usado em ramificações diferentes, permitindo que diferentes funções.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="c7dd1-206">É possível associar dois objetos diferentes opções com a classe de opções de um.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="c7dd1-207">A solução é obter os objetos de opções com os valores de opções propriamente dito no seu `Startup` de classe e passá-las diretamente para cada instância do seu middleware.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="c7dd1-208">Adicionar uma segunda chave a ser *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="c7dd1-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="c7dd1-209">Para adicionar um segundo conjunto de opções para o *appSettings. JSON* de arquivo, use uma nova chave de para identificá-lo exclusivamente:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="c7dd1-210">Recuperar valores de opções e passá-los para o middleware.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="c7dd1-211">O `Use...` método de extensão (o que adiciona seu middleware ao pipeline) é um lugar lógico para transmitir os valores de opção:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="c7dd1-212">Habilite o middleware para utilize um parâmetro de opções.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="c7dd1-213">Forneça uma sobrecarga da `Use...` método de extensão (que usa o parâmetro options e passa-o para `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="c7dd1-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="c7dd1-214">Quando `UseMiddleware` é chamado com parâmetros, ele passa os parâmetros para o construtor de middleware quando ele instancia o objeto de middleware.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="c7dd1-215">Observe como isso encapsula o objeto de opções em um `OptionsWrapper` objeto.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="c7dd1-216">Isso implementa `IOptions`, conforme o esperado pelo construtor de middleware.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="c7dd1-217">Migrando para o novo HttpContext</span><span class="sxs-lookup"><span data-stu-id="c7dd1-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="c7dd1-218">Você viu antes que o `Invoke` método no seu middleware usa um parâmetro de tipo `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="c7dd1-219">`HttpContext` foi alterado significativamente no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="c7dd1-220">Esta seção mostra como converter as propriedades mais usadas da [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) para o novo `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="c7dd1-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="c7dd1-221">HttpContext</span></span>

<span data-ttu-id="c7dd1-222">**HttpContext. Items** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="c7dd1-223">**ID de solicitação exclusiva (nenhum equivalente System.Web.HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="c7dd1-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="c7dd1-224">Fornece uma id exclusiva para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="c7dd1-225">Muito útil para incluir em seus logs.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="c7dd1-226">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="c7dd1-226">HttpContext.Request</span></span>

<span data-ttu-id="c7dd1-227">**HttpContext.Request.HttpMethod** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="c7dd1-228">**HttpContext.Request.QueryString** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="c7dd1-229">**HttpContext.Request.Url** e **HttpContext.Request.RawUrl** traduzir para:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="c7dd1-230">**HttpContext.Request.IsSecureConnection** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="c7dd1-231">**HttpContext.Request.UserHostAddress** translates to:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="c7dd1-232">**HttpContext.Request.Cookies** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="c7dd1-233">**HttpContext.Request.RequestContext.RouteData** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="c7dd1-234">**HttpContext.Request.Headers** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="c7dd1-235">**HttpContext.Request.UserAgent** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="c7dd1-236">**HttpContext.Request.UrlReferrer** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="c7dd1-237">**HttpContext.Request.ContentType** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="c7dd1-238">**HttpContext.Request.Form** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="c7dd1-239">Ler os valores de formulário apenas se for o tipo de conteúdo sub *x-www-form-urlencoded* ou *dados de formulário*.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="c7dd1-240">**HttpContext.Request.InputStream** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="c7dd1-241">Use esse código somente em um middleware de tipo de manipulador, no final de um pipeline.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="c7dd1-242">Você pode ler o corpo bruto, conforme mostrado acima apenas uma vez por solicitação.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="c7dd1-243">Middleware de tentativa de ler o corpo após a primeira leitura lê um corpo vazio.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="c7dd1-244">Isso não se aplica à leitura de um formulário, conforme mostrado anteriormente, porque isso é feito de um buffer.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="c7dd1-245">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="c7dd1-245">HttpContext.Response</span></span>

<span data-ttu-id="c7dd1-246">**HttpContext.Response.Status** e **HttpContext.Response.StatusDescription** traduzir para:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="c7dd1-247">**HttpContext.Response.ContentEncoding** e **HttpContext.Response.ContentType** traduzir para:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="c7dd1-248">**HttpContext.Response.ContentType** em seu próprio também se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="c7dd1-249">**HttpContext.Response.Output** se traduz em:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="c7dd1-250">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="c7dd1-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="c7dd1-251">Atendendo a um arquivo é discutida [aqui](../fundamentals/request-features.md#middleware-and-request-features).</span><span class="sxs-lookup"><span data-stu-id="c7dd1-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="c7dd1-252">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="c7dd1-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="c7dd1-253">Enviar cabeçalhos de resposta é complicado pelo fato de que se você defini-las depois que nada foi escrito para o corpo da resposta, eles não funcionará.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="c7dd1-254">A solução é definir um método de retorno de chamada que será chamado direita antes de gravar do início da resposta.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="c7dd1-255">Isso é feito melhor no início do `Invoke` método em seu middleware.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="c7dd1-256">É esse método de retorno de chamada que define os cabeçalhos de resposta.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="c7dd1-257">O código a seguir define um método de retorno de chamada chamado `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="c7dd1-258">O `SetHeaders` método de retorno de chamada teria esta aparência:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="c7dd1-259">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="c7dd1-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="c7dd1-260">Cookies de viagem para o navegador em um *Set-Cookie* cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="c7dd1-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="c7dd1-261">Como resultado, o envio de cookies requer mesmo retorno de chamada usado para enviar cabeçalhos de resposta:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="c7dd1-262">O `SetCookies` método de retorno de chamada deve ser semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="c7dd1-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="c7dd1-263">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c7dd1-263">Additional resources</span></span>

* [<span data-ttu-id="c7dd1-264">Visão geral de módulos HTTP e de manipuladores HTTP</span><span class="sxs-lookup"><span data-stu-id="c7dd1-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="c7dd1-265">Configuração</span><span class="sxs-lookup"><span data-stu-id="c7dd1-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="c7dd1-266">Inicialização de aplicativos</span><span class="sxs-lookup"><span data-stu-id="c7dd1-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="c7dd1-267">Middleware</span><span class="sxs-lookup"><span data-stu-id="c7dd1-267">Middleware</span></span>](xref:fundamentals/middleware/index)
