---
title: Novidades do ASP.NET Core 2.0
author: rick-anderson
description: Saiba mais sobre os novos recursos no ASP.NET Core 2.0.
ms.author: riande
ms.date: 07/10/2017
uid: aspnetcore-2.0
ms.openlocfilehash: 07e490fd844f0f9044afce37651ef680ed0c894d
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64883091"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="36fe1-103">Novidades do ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="36fe1-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="36fe1-104">Este artigo destaca as alterações mais significativas no ASP.NET Core 2.0, com links para a documentação relevante.</span><span class="sxs-lookup"><span data-stu-id="36fe1-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="36fe1-105">Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="36fe1-105">Razor Pages</span></span>

<span data-ttu-id="36fe1-106">Páginas do Razor é um novo recurso do ASP.NET Core MVC que torna a codificação de cenários focados em página mais fácil e produtiva.</span><span class="sxs-lookup"><span data-stu-id="36fe1-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="36fe1-107">Para obter mais informações, consulte a introdução e o tutorial:</span><span class="sxs-lookup"><span data-stu-id="36fe1-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="36fe1-108">Introdução a Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="36fe1-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="36fe1-109">Introdução a Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="36fe1-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="36fe1-110">Metapacote do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36fe1-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="36fe1-111">Um novo metapacote do ASP.NET Core inclui todos os pacotes feitos e com suporte pelas equipes do ASP.NET Core e do Entity Framework Core, juntamente com as respectivas dependências internas e de terceiros.</span><span class="sxs-lookup"><span data-stu-id="36fe1-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="36fe1-112">Você não precisa mais escolher recursos individuais do ASP.NET Core por pacote.</span><span class="sxs-lookup"><span data-stu-id="36fe1-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="36fe1-113">Todos os recursos estão incluídos no pacote [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All).</span><span class="sxs-lookup"><span data-stu-id="36fe1-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="36fe1-114">Os modelos padrão usam este pacote.</span><span class="sxs-lookup"><span data-stu-id="36fe1-114">The default templates use this package.</span></span>

<span data-ttu-id="36fe1-115">Para obter mais informações, consulte [Metapacote do Microsoft.AspNetCore.All para ASP.NET Core 2.0](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="36fe1-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="36fe1-116">Repositório de tempo de execução</span><span class="sxs-lookup"><span data-stu-id="36fe1-116">Runtime Store</span></span>

<span data-ttu-id="36fe1-117">Aplicativos que usam o metapacote `Microsoft.AspNetCore.All` aproveitam automaticamente o novo repositório de tempo de execução do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="36fe1-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="36fe1-118">O repositório contém todos os ativos de tempo de execução necessários para executar aplicativos ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="36fe1-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="36fe1-119">Quando você usa o metapacote `Microsoft.AspNetCore.All`, nenhum ativo dos pacotes NuGet do ASP.NET Core referenciados são implantados com o aplicativo porque eles já estão no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="36fe1-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="36fe1-120">Os ativos no repositório de tempo de execução também são pré-compilados para melhorar o tempo de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36fe1-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="36fe1-121">Para obter mais informações, consulte [Repositório de tempo de execução](/dotnet/core/deploying/runtime-store)</span><span class="sxs-lookup"><span data-stu-id="36fe1-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="36fe1-122">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="36fe1-122">.NET Standard 2.0</span></span>

<span data-ttu-id="36fe1-123">Os pacotes do ASP.NET Core 2.0 são direcionados ao .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="36fe1-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="36fe1-124">Os pacotes podem ser referenciados por outras bibliotecas do .NET Standard 2.0 e podem ser executados em implementações em conformidade com o .NET Standard 2.0, incluindo o .NET Core 2.0 e o .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="36fe1-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="36fe1-125">O metapacote `Microsoft.AspNetCore.All` aborda apenas o .Net Core 2.0 porque ele foi projetado para ser utilizado com o repositório de tempo de execução do .Net Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="36fe1-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="36fe1-126">Atualização da configuração</span><span class="sxs-lookup"><span data-stu-id="36fe1-126">Configuration update</span></span>

<span data-ttu-id="36fe1-127">Uma instância de `IConfiguration` é adicionada ao contêiner de serviços por padrão no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="36fe1-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="36fe1-128">`IConfiguration` no contêiner de serviços torna mais fácil para aplicativos recuperarem valores de configuração do contêiner.</span><span class="sxs-lookup"><span data-stu-id="36fe1-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="36fe1-129">Para obter informações sobre o status da documentação planejada, consulte o [problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="36fe1-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="36fe1-130">Atualização de registro em log</span><span class="sxs-lookup"><span data-stu-id="36fe1-130">Logging update</span></span>

<span data-ttu-id="36fe1-131">No ASP.NET Core 2.0, o log será incorporado no sistema de DI (injeção de dependência) por padrão.</span><span class="sxs-lookup"><span data-stu-id="36fe1-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="36fe1-132">Você adiciona provedores e configura a filtragem no arquivo *Program.cs* em vez de usar o arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="36fe1-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="36fe1-133">E o `ILoggerFactory` padrão dá suporte à filtragem de forma que lhe permite usar uma abordagem flexível para filtragem entre provedores e filtragem específica do provedor.</span><span class="sxs-lookup"><span data-stu-id="36fe1-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="36fe1-134">Para obter mais informações, consulte [Introdução ao registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="36fe1-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="36fe1-135">Atualização de autenticação</span><span class="sxs-lookup"><span data-stu-id="36fe1-135">Authentication update</span></span>

<span data-ttu-id="36fe1-136">Um novo modelo de autenticação torna mais fácil configurar a autenticação para um aplicativo usando a DI.</span><span class="sxs-lookup"><span data-stu-id="36fe1-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="36fe1-137">Novos modelos estão disponíveis para configurar a autenticação para aplicativos Web e APIs Web usando o [Azure AD B2C] (https://azure.microsoft.com/services/active-directory-b2c/).</span><span class="sxs-lookup"><span data-stu-id="36fe1-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C] (https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="36fe1-138">Para obter informações sobre o status da documentação planejada, consulte o [problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="36fe1-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="36fe1-139">Atualização de identidade</span><span class="sxs-lookup"><span data-stu-id="36fe1-139">Identity update</span></span>

<span data-ttu-id="36fe1-140">Tornamos mais fácil criar APIs Web seguras usando a identidade do ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="36fe1-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="36fe1-141">Você pode adquirir tokens de acesso para acessar suas APIs Web usando a [MSAL (Biblioteca de Autenticação da Microsoft)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span><span class="sxs-lookup"><span data-stu-id="36fe1-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="36fe1-142">Para obter mais informações sobre alterações de autenticação no 2.0, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="36fe1-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="36fe1-143">Confirmação de conta e de recuperação de senha no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36fe1-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="36fe1-144">Habilitar a geração de código QR para aplicativos de autenticador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36fe1-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="36fe1-145">Migrar a autenticação e a identidade para o ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="36fe1-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="36fe1-146">Modelos do SPA</span><span class="sxs-lookup"><span data-stu-id="36fe1-146">SPA templates</span></span>

<span data-ttu-id="36fe1-147">Modelos de projeto de SPA (aplicativo de página único) para Angular, Aurelia, Knockout.js, React.js e React.js com Redux estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="36fe1-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="36fe1-148">O modelo Angular foi atualizado para Angular 4.</span><span class="sxs-lookup"><span data-stu-id="36fe1-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="36fe1-149">Os modelos Angular e React estão disponíveis por padrão. Para saber como obter os outros modelos, confira [Criar um novo projeto de SPA](xref:client-side/spa-services#creating-a-new-project).</span><span class="sxs-lookup"><span data-stu-id="36fe1-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#creating-a-new-project).</span></span> <span data-ttu-id="36fe1-150">Para obter informações de como criar um SPA no ASP.NET Core, confira [Usar JavaScriptServices para criar aplicativos de página única](xref:client-side/spa-services).</span><span class="sxs-lookup"><span data-stu-id="36fe1-150">For information about how to build a SPA in ASP.NET Core, see [Use JavaScriptServices for Creating Single Page Applications](xref:client-side/spa-services).</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="36fe1-151">Melhorias do Kestrel</span><span class="sxs-lookup"><span data-stu-id="36fe1-151">Kestrel improvements</span></span>

<span data-ttu-id="36fe1-152">O servidor Web Kestrel tem novos recursos que o tornam mais adequado como um servidor voltado para a Internet.</span><span class="sxs-lookup"><span data-stu-id="36fe1-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="36fe1-153">Uma série de opções de configuração de restrição de servidor serão adicionadas na nova propriedade `Limits` da classe `KestrelServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="36fe1-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="36fe1-154">Adicione limites para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="36fe1-154">Add limits for the following:</span></span>

* <span data-ttu-id="36fe1-155">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="36fe1-155">Maximum client connections</span></span>
* <span data-ttu-id="36fe1-156">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="36fe1-156">Maximum request body size</span></span>
* <span data-ttu-id="36fe1-157">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="36fe1-157">Minimum request body data rate</span></span>

<span data-ttu-id="36fe1-158">Para obter mais informações, consulte [Implementação do servidor Web Kestrel no ASP.NET Core](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="36fe1-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="36fe1-159">WebListener renomeado para HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="36fe1-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="36fe1-160">Os pacotes `Microsoft.AspNetCore.Server.WebListener` e `Microsoft.Net.Http.Server` foram mesclados em um novo pacote `Microsoft.AspNetCore.Server.HttpSys`.</span><span class="sxs-lookup"><span data-stu-id="36fe1-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="36fe1-161">Os namespaces foram atualizados para corresponderem.</span><span class="sxs-lookup"><span data-stu-id="36fe1-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="36fe1-162">Para obter mais informações, consulte [Implementação do servidor Web HTTP.sys no ASP.NET Core](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="36fe1-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="36fe1-163">Suporte aprimorado a cabeçalho HTTP</span><span class="sxs-lookup"><span data-stu-id="36fe1-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="36fe1-164">Ao usar o MVC para transmitir um `FileStreamResult` ou um `FileContentResult`, agora você tem a opção de definir uma `ETag` ou uma data `LastModified` no conteúdo que você transmitir.</span><span class="sxs-lookup"><span data-stu-id="36fe1-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="36fe1-165">Você pode definir esses valores no conteúdo retornado com código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="36fe1-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="36fe1-166">O arquivo retornado para os visitantes será decorado com os cabeçalhos HTTP apropriados para os valores `ETag` e `LastModified`.</span><span class="sxs-lookup"><span data-stu-id="36fe1-166">The file returned to your visitors will be decorated with the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="36fe1-167">Se um visitante do aplicativo solicitar o conteúdo com um cabeçalho de solicitação de intervalo, o ASP.NET Core reconhecerá a solicitação e lidará com o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="36fe1-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="36fe1-168">Se parte do conteúdo solicitado puder ser entregue, o ASP.NET Core ignorará a parte em questão e retornará apenas o conjunto de bytes solicitado.</span><span class="sxs-lookup"><span data-stu-id="36fe1-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="36fe1-169">Você não precisa gravar nenhum manipulador especial em seus métodos para adaptar ou manipular esse recurso; ele é manipulado automaticamente para você.</span><span class="sxs-lookup"><span data-stu-id="36fe1-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="36fe1-170">Inicialização de hospedagem e o Application Insights</span><span class="sxs-lookup"><span data-stu-id="36fe1-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="36fe1-171">Ambientes de hospedagem podem injetar dependências de pacote extras e executar código durante a inicialização do aplicativo, sem que o aplicativo precise tomar uma dependência explicitamente ou chamar algum método.</span><span class="sxs-lookup"><span data-stu-id="36fe1-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="36fe1-172">Esse recurso pode ser usado para habilitar determinados ambientes para recursos de "esclarecimento" exclusivos para esse ambiente sem que o aplicativo precise saber antecipadamente.</span><span class="sxs-lookup"><span data-stu-id="36fe1-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="36fe1-173">No ASP.NET Core 2.0, esse recurso é usado para habilitar o diagnóstico do Application Insights automaticamente durante a depuração no Visual Studio e (depois de optar por isto) quando em execução nos Serviços de Aplicativos do Azure.</span><span class="sxs-lookup"><span data-stu-id="36fe1-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="36fe1-174">Como resultado, os modelos de projeto não adicionam mais código e pacotes do Application Insights por padrão.</span><span class="sxs-lookup"><span data-stu-id="36fe1-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="36fe1-175">Para obter informações sobre o status da documentação planejada, consulte o [problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="36fe1-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="36fe1-176">Uso automático de tokens antifalsificação</span><span class="sxs-lookup"><span data-stu-id="36fe1-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="36fe1-177">O ASP.NET Core sempre ajudou a fazer a codificação HTML de seu conteúdo por padrão, mas com a nova versão, estamos dando um passo adicional para ajudar a impedir ataques de XSRF (falsificação de solicitação entre sites).</span><span class="sxs-lookup"><span data-stu-id="36fe1-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="36fe1-178">O ASP.NET Core agora emitirá tokens antifalsificação por padrão e os validará em ações de POST do formulário e em páginas sem configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="36fe1-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="36fe1-179">Para obter mais informações, confira [Impedir ataques de XSRF/CSRF (solicitação intersite forjada)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="36fe1-179">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="36fe1-180">Pré-compilação automática</span><span class="sxs-lookup"><span data-stu-id="36fe1-180">Automatic precompilation</span></span>

<span data-ttu-id="36fe1-181">A pré-compilação da exibição do Razor é habilitada durante a publicação por padrão, reduzindo o tamanho da saída de publicação e o tempo de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36fe1-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="36fe1-182">Para obter mais informações, confira [Compilação e pré-compilação de exibição Razor no ASP.NET Core](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="36fe1-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="36fe1-183">Suporte ao Razor para C# 7.1</span><span class="sxs-lookup"><span data-stu-id="36fe1-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="36fe1-184">O mecanismo de exibição Razor foi atualizado para funcionar com o novo compilador Roslyn.</span><span class="sxs-lookup"><span data-stu-id="36fe1-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="36fe1-185">Isso inclui suporte para recursos do C# 7.1 como expressões padrão, nomes de tupla inferidos e correspondência de padrões com genéricos.</span><span class="sxs-lookup"><span data-stu-id="36fe1-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="36fe1-186">Para usar o C# 7.1 em seu projeto, adicione a seguinte propriedade no arquivo de projeto e, em seguida, recarregue a solução:</span><span class="sxs-lookup"><span data-stu-id="36fe1-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="36fe1-187">Para obter informações sobre o status dos recursos do C# 7.1, consulte [o repositório GitHub do Roslyn](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="36fe1-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="36fe1-188">Outras atualizações de documentação para 2.0</span><span class="sxs-lookup"><span data-stu-id="36fe1-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="36fe1-189">Perfis de publicação do Visual Studio para a implantação do aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36fe1-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="36fe1-190">Gerenciamento de chaves</span><span class="sxs-lookup"><span data-stu-id="36fe1-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="36fe1-191">Configurar a autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="36fe1-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="36fe1-192">Configurar a autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="36fe1-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="36fe1-193">Configurar a autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="36fe1-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="36fe1-194">Configurar a autenticação da conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="36fe1-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="36fe1-195">Diretrizes de migração</span><span class="sxs-lookup"><span data-stu-id="36fe1-195">Migration guidance</span></span>

<span data-ttu-id="36fe1-196">Para obter diretrizes sobre como migrar aplicativos ASP.NET Core 1.x para o ASP.NET Core 2.0, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="36fe1-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="36fe1-197">Migrar do ASP.NET Core 1.x para o ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="36fe1-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="36fe1-198">Migrar a autenticação e a identidade para o ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="36fe1-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="36fe1-199">Informações adicionais</span><span class="sxs-lookup"><span data-stu-id="36fe1-199">Additional Information</span></span>

<span data-ttu-id="36fe1-200">Para obter uma lista de alterações, consulte as [Notas de versão do ASP.NET Core 2.0](https://github.com/aspnet/Home/releases/tag/2.0.0).</span><span class="sxs-lookup"><span data-stu-id="36fe1-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/aspnet/Home/releases/tag/2.0.0).</span></span>

<span data-ttu-id="36fe1-201">Para se conectar ao progresso e aos planos da equipe de desenvolvimento do ASP.NET Core, fique ligado no [ASP.NET Community Standup](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="36fe1-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
