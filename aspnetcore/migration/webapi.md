---
title: Migrar de ASP.NET Web API para ASP.NET Core
author: ardalis
description: Saiba como migrar uma implementação de API da Web da API Web do ASP.NET 4. x para ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/webapi
ms.openlocfilehash: 7f61b78c589fc9d01061b50554e5a639e372c3d8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661843"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="b9322-103">Migrar de ASP.NET Web API para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9322-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="b9322-104">De [Scott Addie](https://twitter.com/scott_addie) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b9322-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b9322-105">Uma API Web do ASP.NET 4. x é um serviço HTTP que atinge uma ampla variedade de clientes, incluindo navegadores e dispositivos móveis.</span><span class="sxs-lookup"><span data-stu-id="b9322-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="b9322-106">ASP.NET Core unifica os modelos de aplicativo de API Web e MVC do ASP.NET 4. x em um modelo de programação mais simples conhecido como ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="b9322-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="b9322-107">Este artigo demonstra as etapas necessárias para migrar da API Web do ASP.NET 4. x para ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="b9322-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="b9322-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b9322-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b9322-109">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="b9322-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="b9322-110">Examine o projeto de API Web do ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="b9322-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="b9322-111">Como ponto de partida, este artigo usa o projeto *ProductsApp* criado em [introdução com ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="b9322-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="b9322-112">Nesse projeto, um projeto de API Web simples ASP.NET 4. x é configurado da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="b9322-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="b9322-113">No *global.asax.cs*, é feita uma chamada para `WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="b9322-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="b9322-114">A classe `WebApiConfig` é encontrada na pasta *App_Start* e tem um método de `Register` estático:</span><span class="sxs-lookup"><span data-stu-id="b9322-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="b9322-115">Essa classe configura o [Roteamento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), embora ele não esteja realmente sendo usado no projeto.</span><span class="sxs-lookup"><span data-stu-id="b9322-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="b9322-116">Ele também configura a tabela de roteamento, que é usada pelo ASP.NET Web API.</span><span class="sxs-lookup"><span data-stu-id="b9322-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="b9322-117">Nesse caso, a API Web ASP.NET 4. x espera que as URLs correspondam ao formato `/api/{controller}/{id}`, com `{id}` sendo opcional.</span><span class="sxs-lookup"><span data-stu-id="b9322-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="b9322-118">O projeto *ProductsApp* inclui um controlador.</span><span class="sxs-lookup"><span data-stu-id="b9322-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="b9322-119">O controlador herda de `ApiController` e contém duas ações:</span><span class="sxs-lookup"><span data-stu-id="b9322-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="b9322-120">O modelo de `Product` usado pelo `ProductsController` é uma classe simples:</span><span class="sxs-lookup"><span data-stu-id="b9322-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="b9322-121">As seções a seguir demonstram a migração do projeto de API Web para ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="b9322-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="b9322-122">Criar projeto de destino</span><span class="sxs-lookup"><span data-stu-id="b9322-122">Create destination project</span></span>

<span data-ttu-id="b9322-123">Conclua as seguintes etapas no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b9322-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="b9322-124">Vá para o **arquivo** > **novo** > **projeto** > **outros tipos de projeto** > soluções do **Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="b9322-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="b9322-125">Selecione **solução em branco**e nomeie a solução *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="b9322-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="b9322-126">Clique no botão **OK**.</span><span class="sxs-lookup"><span data-stu-id="b9322-126">Click the **OK** button.</span></span>
* <span data-ttu-id="b9322-127">Adicione o projeto *ProductsApp* existente à solução.</span><span class="sxs-lookup"><span data-stu-id="b9322-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="b9322-128">Adicione um novo projeto de **aplicativo Web ASP.NET Core** à solução.</span><span class="sxs-lookup"><span data-stu-id="b9322-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="b9322-129">Selecione a estrutura de destino **.NET Core** na lista suspensa e selecione o modelo projeto de **API** .</span><span class="sxs-lookup"><span data-stu-id="b9322-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="b9322-130">Nomeie o projeto *ProductsCore*e clique no botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="b9322-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="b9322-131">A solução agora contém dois projetos.</span><span class="sxs-lookup"><span data-stu-id="b9322-131">The solution now contains two projects.</span></span> <span data-ttu-id="b9322-132">As seções a seguir explicam como migrar o conteúdo do projeto *ProductsApp* para o projeto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="b9322-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="b9322-133">Migrar configuração</span><span class="sxs-lookup"><span data-stu-id="b9322-133">Migrate configuration</span></span>

<span data-ttu-id="b9322-134">ASP.NET Core não usa a pasta *App_Start* ou o arquivo *global. asax* , e o arquivo *Web. config* é adicionado no momento da publicação.</span><span class="sxs-lookup"><span data-stu-id="b9322-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="b9322-135">*Startup.cs* é a substituição para *global. asax* e está localizada na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="b9322-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="b9322-136">A classe `Startup` manipula todas as tarefas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9322-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="b9322-137">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b9322-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="b9322-138">No ASP.NET Core MVC, o roteamento de atributo é incluído por padrão quando <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> é chamado em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b9322-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="b9322-139">A chamada a seguir `UseMvc` substitui o arquivo */webapiconfig.cs App_Start* do projeto *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="b9322-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="b9322-140">Migrar modelos e controladores</span><span class="sxs-lookup"><span data-stu-id="b9322-140">Migrate models and controllers</span></span>

<span data-ttu-id="b9322-141">Copie sobre o controlador do projeto *ProductApp* e o modelo que ele usa.</span><span class="sxs-lookup"><span data-stu-id="b9322-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="b9322-142">Siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="b9322-142">Follow these steps:</span></span>

1. <span data-ttu-id="b9322-143">Copie os *controladores/ProductsController. cs* do projeto original para o novo.</span><span class="sxs-lookup"><span data-stu-id="b9322-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="b9322-144">Copie a pasta *modelos* inteira do projeto original para o novo.</span><span class="sxs-lookup"><span data-stu-id="b9322-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="b9322-145">Altere os namespaces dos arquivos copiados para que correspondam ao novo nome do projeto (*ProductsCore*).</span><span class="sxs-lookup"><span data-stu-id="b9322-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="b9322-146">Ajuste a instrução `using ProductsApp.Models;` em *ProductsController.cs* também.</span><span class="sxs-lookup"><span data-stu-id="b9322-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="b9322-147">Neste ponto, a criação do aplicativo resulta em vários erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="b9322-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="b9322-148">Os erros ocorrem porque os seguintes componentes não existem no ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b9322-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="b9322-149">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="b9322-149">`ApiController` class</span></span>
* <span data-ttu-id="b9322-150">Namespace `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="b9322-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="b9322-151">interface `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="b9322-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="b9322-152">Corrija os erros da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="b9322-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="b9322-153">Alterar `ApiController` para <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="b9322-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="b9322-154">Adicione `using Microsoft.AspNetCore.Mvc;` para resolver a referência de `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="b9322-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="b9322-155">Excluir `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="b9322-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="b9322-156">Altere o tipo de retorno da ação de `GetProduct` de `IHttpActionResult` para `ActionResult<Product>`.</span><span class="sxs-lookup"><span data-stu-id="b9322-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="b9322-157">Simplifique a instrução `return` da ação de `GetProduct` para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b9322-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="b9322-158">Configurar o roteamento</span><span class="sxs-lookup"><span data-stu-id="b9322-158">Configure routing</span></span>

<span data-ttu-id="b9322-159">Configure o roteamento da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="b9322-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="b9322-160">Marque a classe `ProductsController` com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="b9322-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="b9322-161">O atributo [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) anterior configura o padrão de roteamento de atributo do controlador.</span><span class="sxs-lookup"><span data-stu-id="b9322-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="b9322-162">O atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) torna o atributo roteando um requisito para todas as ações neste controlador.</span><span class="sxs-lookup"><span data-stu-id="b9322-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="b9322-163">O roteamento de atributos dá suporte a tokens, como `[controller]` e `[action]`.</span><span class="sxs-lookup"><span data-stu-id="b9322-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="b9322-164">No tempo de execução, cada token é substituído pelo nome do controlador ou da ação, respectivamente, ao qual o atributo foi aplicado.</span><span class="sxs-lookup"><span data-stu-id="b9322-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="b9322-165">Os tokens reduzem o número de cadeias de caracteres mágicos no projeto.</span><span class="sxs-lookup"><span data-stu-id="b9322-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="b9322-166">Os tokens também garantem que as rotas permaneçam sincronizadas com os controladores e ações correspondentes quando refatoração de renomeação automática são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="b9322-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="b9322-167">Defina o modo de compatibilidade do projeto para ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="b9322-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="b9322-168">A alteração anterior:</span><span class="sxs-lookup"><span data-stu-id="b9322-168">The preceding change:</span></span>

    * <span data-ttu-id="b9322-169">É necessário para usar o atributo `[ApiController]` no nível do controlador.</span><span class="sxs-lookup"><span data-stu-id="b9322-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="b9322-170">Optamos pelos comportamentos potencialmente em potencial introduzidos no ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="b9322-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="b9322-171">Habilite solicitações HTTP Get para as ações de `ProductController`:</span><span class="sxs-lookup"><span data-stu-id="b9322-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="b9322-172">Aplique o atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) à ação `GetAllProducts`.</span><span class="sxs-lookup"><span data-stu-id="b9322-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="b9322-173">Aplique o atributo `[HttpGet("{id}")]` à ação `GetProduct`.</span><span class="sxs-lookup"><span data-stu-id="b9322-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="b9322-174">Após as alterações anteriores e a remoção de instruções de `using` não usadas, o arquivo *ProductsController.cs* terá esta aparência:</span><span class="sxs-lookup"><span data-stu-id="b9322-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="b9322-175">Execute o projeto migrado e navegue até `/api/products`.</span><span class="sxs-lookup"><span data-stu-id="b9322-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="b9322-176">Uma lista completa de três produtos é exibida.</span><span class="sxs-lookup"><span data-stu-id="b9322-176">A full list of three products appears.</span></span> <span data-ttu-id="b9322-177">Navegue até `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="b9322-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="b9322-178">O primeiro produto é exibido.</span><span class="sxs-lookup"><span data-stu-id="b9322-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="b9322-179">Correção de compatibilidade</span><span class="sxs-lookup"><span data-stu-id="b9322-179">Compatibility shim</span></span>

<span data-ttu-id="b9322-180">A biblioteca [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fornece um Shim de compatibilidade para mover projetos de API Web ASP.NET 4. x para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9322-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="b9322-181">O Shim de compatibilidade estende ASP.NET Core para dar suporte a um número de convenções da API Web 2 do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="b9322-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="b9322-182">O exemplo de porta anteriormente neste documento é básico o bastante para que o Shim de compatibilidade seja desnecessário.</span><span class="sxs-lookup"><span data-stu-id="b9322-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="b9322-183">Para projetos maiores, o uso do Shim de compatibilidade pode ser útil para a ponte temporária da lacuna da API entre ASP.NET Core e a API Web 2 do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="b9322-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="b9322-184">O Shim da compatibilidade da API Web deve ser usado como uma medida temporária para dar suporte à migração de projetos de API Web grandes ASP.NET 4. x para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9322-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="b9322-185">Ao longo do tempo, os projetos devem ser atualizados para usar padrões de ASP.NET Core em vez de depender do Shim de compatibilidade.</span><span class="sxs-lookup"><span data-stu-id="b9322-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="b9322-186">Os recursos de compatibilidade incluídos no `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluem:</span><span class="sxs-lookup"><span data-stu-id="b9322-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="b9322-187">Adiciona um tipo de `ApiController` para que os tipos base dos controladores não precisem ser atualizados.</span><span class="sxs-lookup"><span data-stu-id="b9322-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="b9322-188">Habilita a associação de modelo de estilo de API Web.</span><span class="sxs-lookup"><span data-stu-id="b9322-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="b9322-189">ASP.NET Core funções de associação de modelo MVC da mesma forma que o do ASP.NET 4. x MVC 5, por padrão.</span><span class="sxs-lookup"><span data-stu-id="b9322-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="b9322-190">O Shim de compatibilidade altera a associação de modelo para ser mais semelhante às convenções de associação de modelo da API Web 2 do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="b9322-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="b9322-191">Por exemplo, tipos complexos são associados automaticamente do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="b9322-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="b9322-192">Estende a associação de modelo para que as ações do controlador possam ter parâmetros do tipo `HttpRequestMessage`.</span><span class="sxs-lookup"><span data-stu-id="b9322-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="b9322-193">Adiciona os formatadores de mensagem, permitindo que as ações retornem resultados do tipo `HttpResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="b9322-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="b9322-194">Adiciona métodos de resposta adicionais que as ações da API Web 2 podem ter usado para atender às respostas:</span><span class="sxs-lookup"><span data-stu-id="b9322-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="b9322-195">geradores de `HttpResponseMessage`:</span><span class="sxs-lookup"><span data-stu-id="b9322-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="b9322-196">Métodos de resultado da ação:</span><span class="sxs-lookup"><span data-stu-id="b9322-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="b9322-197">Adiciona uma instância de `IContentNegotiator` ao contêiner de injeção de dependência (DI) do aplicativo e disponibiliza os tipos relacionados à negociação de conteúdo de [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="b9322-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="b9322-198">Exemplos desses tipos incluem `DefaultContentNegotiator` e `MediaTypeFormatter`.</span><span class="sxs-lookup"><span data-stu-id="b9322-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="b9322-199">Para usar o Shim de compatibilidade:</span><span class="sxs-lookup"><span data-stu-id="b9322-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="b9322-200">Instale o pacote NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="b9322-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="b9322-201">Registre os serviços da correção de compatibilidade com o contêiner de DI do aplicativo chamando `services.AddMvc().AddWebApiConventions()` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b9322-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="b9322-202">Defina rotas específicas da API Web usando `MapWebApiRoute` no `IRouteBuilder` na chamada de `IApplicationBuilder.UseMvc` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9322-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9322-203">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b9322-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
