---
title: Formatar dados de resposta na API Web ASP.NET Core
author: ardalis
description: Saiba como formatar dados de resposta na API Web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 8/22/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 78fe620ea8fdd681a276253f77939bcb2a56ebb9
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391285"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="b4e8c-103">Formatar dados de resposta na API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4e8c-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="b4e8c-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b4e8c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b4e8c-105">ASP.NET Core MVC tem suporte para formatar dados de resposta.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="b4e8c-106">Os dados de resposta podem ser formatados usando formatos específicos ou em resposta ao formato solicitado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="b4e8c-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b4e8c-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="b4e8c-108">Resultados de ação específica de formato</span><span class="sxs-lookup"><span data-stu-id="b4e8c-108">Format-specific Action Results</span></span>

<span data-ttu-id="b4e8c-109">Alguns tipos de resultado de ação são específicos a um formato específico, como <xref:Microsoft.AspNetCore.Mvc.JsonResult> e <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="b4e8c-110">As ações podem retornar resultados formatados em um formato específico, independentemente das preferências do cliente.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="b4e8c-111">Por exemplo, retornar `JsonResult` retorna dados formatados em JSON.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="b4e8c-112">Retornar `ContentResult` ou uma cadeia de caracteres retorna dados de cadeia de caracteres formatados com texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="b4e8c-113">Uma ação não é necessária para retornar qualquer tipo específico.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="b4e8c-114">ASP.NET Core dá suporte a qualquer valor de retorno de objeto.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="b4e8c-115">Os resultados de ações que retornam objetos que não são <xref:Microsoft.AspNetCore.Mvc.IActionResult> tipos são serializados usando a implementação apropriada de <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="b4e8c-116">Para obter mais informações, consulte <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="b4e8c-117">O método auxiliar interno <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> retorna dados formatados para JSON: [!code-csharp @ no__t-2</span><span class="sxs-lookup"><span data-stu-id="b4e8c-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="b4e8c-118">O download de exemplo retorna a lista de autores.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="b4e8c-119">Usando as ferramentas de desenvolvedor de navegador F12 ou o [postmaster](https://www.getpostman.com/tools) com o código anterior:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="b4e8c-120">O cabeçalho de resposta contendo **Content-Type:** `application/json; charset=utf-8` é exibido.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="b4e8c-121">Os cabeçalhos de solicitação são exibidos.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-121">The request headers are displayed.</span></span> <span data-ttu-id="b4e8c-122">Por exemplo, o cabeçalho `Accept`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-122">For example, the `Accept` header.</span></span> <span data-ttu-id="b4e8c-123">O cabeçalho `Accept` é ignorado pelo código anterior.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="b4e8c-124">Para retornar dados formatados como texto sem formatação, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> e o auxiliar <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="b4e8c-125">No código anterior, o `Content-Type` retornado é `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="b4e8c-126">Retornar uma cadeia de caracteres fornece `Content-Type` de `text/plain`:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="b4e8c-127">Para ações com vários tipos de retorno, retorne `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="b4e8c-128">Por exemplo, retornando códigos de status HTTP diferentes com base no resultado das operações executadas.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="b4e8c-129">Negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="b4e8c-129">Content negotiation</span></span>

<span data-ttu-id="b4e8c-130">A negociação de conteúdo ocorre quando o cliente especifica um [cabeçalho Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="b4e8c-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="b4e8c-131">O formato padrão usado pelo ASP.NET Core é [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="b4e8c-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="b4e8c-132">A negociação de conteúdo é:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-132">Content negotiation is:</span></span>

* <span data-ttu-id="b4e8c-133">Implementado por <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="b4e8c-134">Incorporados aos resultados da ação específica do código de status retornados dos métodos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="b4e8c-135">Os métodos auxiliares de resultados de ação são baseados em `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="b4e8c-136">Quando um tipo de modelo é retornado, o tipo de retorno é `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="b4e8c-137">O seguinte método de ação usa os métodos auxiliares `Ok` e `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="b4e8c-138">Por padrão, o ASP.NET Core dá suporte a tipos de mídia `application/json`, `text/json` e `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="b4e8c-139">Ferramentas como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/tools) podem definir o cabeçalho de solicitação `Accept` para especificar o formato de retorno.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="b4e8c-140">Quando o cabeçalho `Accept` contém um tipo ao qual o servidor dá suporte, esse tipo é retornado.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="b4e8c-141">A próxima seção mostra como adicionar outros formatadores.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="b4e8c-142">As ações do controlador podem retornar POCOs (objetos Plain antigos do CLR).</span><span class="sxs-lookup"><span data-stu-id="b4e8c-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="b4e8c-143">Quando um POCO é retornado, o tempo de execução cria automaticamente um `ObjectResult` que encapsula o objeto.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="b4e8c-144">O cliente obtém o objeto serializado formatado.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="b4e8c-145">Se o objeto que está sendo retornado for `null`, uma resposta `204 No Content` será retornada.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="b4e8c-146">Retornando um tipo de objeto:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="b4e8c-147">No código anterior, uma solicitação para um alias de autor válido retorna uma resposta `200 OK` com os dados do autor.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="b4e8c-148">Uma solicitação para um alias inválido retorna uma resposta `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="b4e8c-149">O cabeçalho Accept</span><span class="sxs-lookup"><span data-stu-id="b4e8c-149">The Accept header</span></span>

<span data-ttu-id="b4e8c-150">A *negociação* de conteúdo ocorre quando um cabeçalho `Accept` é exibido na solicitação.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="b4e8c-151">Quando uma solicitação contém um cabeçalho Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="b4e8c-152">Enumera os tipos de mídia no cabeçalho Accept na ordem de preferência.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="b4e8c-153">Tenta localizar um formatador que pode produzir uma resposta em um dos formatos especificados.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="b4e8c-154">Se nenhum formatador for encontrado que possa atender à solicitação do cliente, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="b4e8c-155">Retornará `406 Not Acceptable` se <xref:Microsoft.AspNetCore.Mvc.MvcOptions> tiver sido definido ou-</span><span class="sxs-lookup"><span data-stu-id="b4e8c-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="b4e8c-156">Tenta encontrar o primeiro formatador que pode produzir uma resposta.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="b4e8c-157">Se nenhum formatador estiver configurado para o formato solicitado, o primeiro formatador que pode formatar o objeto será usado.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="b4e8c-158">Se nenhum cabeçalho `Accept` aparecer na solicitação:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="b4e8c-159">O primeiro formatador que pode manipular o objeto é usado para serializar a resposta.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="b4e8c-160">Não há nenhuma negociação ocorrendo.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="b4e8c-161">O servidor está determinando o formato a ser retornado.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-161">The server is determining what format to return.</span></span>

<span data-ttu-id="b4e8c-162">Se o cabeçalho Accept contiver `*/*`, o cabeçalho será ignorado, a menos que `RespectBrowserAcceptHeader` esteja definido como true no <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="b4e8c-163">Navegadores e negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="b4e8c-163">Browsers and content negotiation</span></span>

<span data-ttu-id="b4e8c-164">Diferentemente dos clientes de API típicos, os navegadores da Web fornecem cabeçalhos `Accept`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="b4e8c-165">O navegador da Web especifica vários formatos, incluindo curingas.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="b4e8c-166">Por padrão, quando a estrutura detecta que a solicitação é proveniente de um navegador:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="b4e8c-167">O cabeçalho `Accept` é ignorado.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="b4e8c-168">O conteúdo é retornado em JSON, a menos que configurado de outra forma.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="b4e8c-169">Isso fornece uma experiência mais consistente entre navegadores ao consumir APIs.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="b4e8c-170">Para configurar um aplicativo para honrar os cabeçalhos de aceitação do navegador, defina <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> como `true`:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="b4e8c-171">Configurar formatadores</span><span class="sxs-lookup"><span data-stu-id="b4e8c-171">Configure formatters</span></span>

<span data-ttu-id="b4e8c-172">Os aplicativos que precisam dar suporte a formatos adicionais podem adicionar os pacotes NuGet apropriados e configurar o suporte.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="b4e8c-173">Há formatadores separados para entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="b4e8c-174">Os formatadores de entrada são usados pela [Associação de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="b4e8c-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="b4e8c-175">Os formatadores de saída são usados para formatar respostas.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="b4e8c-176">Para obter informações sobre como criar um formatador personalizado, consulte [formatadores personalizados](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="b4e8c-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="b4e8c-177">Adicionar suporte ao formato XML</span><span class="sxs-lookup"><span data-stu-id="b4e8c-177">Add XML format support</span></span>

<span data-ttu-id="b4e8c-178">Os formatadores XML implementados usando <xref:System.Xml.Serialization.XmlSerializer> são configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="b4e8c-179">O código anterior serializa os resultados usando `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="b4e8c-180">Ao usar o código anterior, os métodos do controlador devem retornar o formato apropriado com base no cabeçalho `Accept` da solicitação.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-180">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="b4e8c-181">Configurar formatadores com base em System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="b4e8c-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="b4e8c-182">Os recursos do formatadores com base em `System.Text.Json` podem ser configurados, usando `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="b4e8c-183">As opções de serialização de saída, em uma base por ação, podem ser configuradas usando `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="b4e8c-184">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="b4e8c-185">Adicionar suporte ao formato JSON com base em Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="b4e8c-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="b4e8c-186">Antes do ASP.NET Core 3,0, os formatadores JSON usados por padrão implementaram usando o pacote `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="b4e8c-187">No ASP.NET Core 3.0 ou posterior, os formatadores JSON padrão baseiam-se no `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="b4e8c-188">O suporte para formatadores e recursos baseados em `Newtonsoft.Json` está disponível ao instalar o pacote NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) e configurá-lo no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="b4e8c-189">Alguns recursos podem não funcionar bem com @no__t formatadores baseados em-0 e exigem uma referência aos formatadores baseados em-1 de @no__t.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="b4e8c-190">Continue usando o @no__t formatadores baseados em-0 se o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="b4e8c-191">Usa atributos `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="b4e8c-192">Por exemplo `[JsonProperty]` ou `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="b4e8c-193">Personaliza as configurações de serialização.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="b4e8c-194">O se baseia em recursos fornecidos pelo `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="b4e8c-195">Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="b4e8c-196">Antes do ASP.NET Core 3.0, `JsonResult.SerializerSettings` aceita uma instância de `JsonSerializerSettings` que é específico para `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="b4e8c-197">Gera documentação [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="b4e8c-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="b4e8c-198">Os recursos para os formatadores baseados em-0 @no__t podem ser configurados usando `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="b4e8c-199">As opções de serialização de saída, em uma base por ação, podem ser configuradas usando `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="b4e8c-200">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-200">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="b4e8c-201">Adicionar suporte ao formato XML</span><span class="sxs-lookup"><span data-stu-id="b4e8c-201">Add XML format support</span></span>

<span data-ttu-id="b4e8c-202">A formatação XML requer o pacote NuGet [Microsoft. AspNetCore. Mvc. Formatters. xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="b4e8c-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="b4e8c-203">Os formatadores XML implementados usando <xref:System.Xml.Serialization.XmlSerializer> são configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="b4e8c-204">O código anterior serializa os resultados usando `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="b4e8c-205">Ao usar o código anterior, os métodos do controlador devem retornar o formato apropriado com base no cabeçalho `Accept` da solicitação.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="b4e8c-206">Especificar um formato</span><span class="sxs-lookup"><span data-stu-id="b4e8c-206">Specify a format</span></span>

<span data-ttu-id="b4e8c-207">Para restringir os formatos de resposta, aplique o filtro [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="b4e8c-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="b4e8c-208">Assim como a maioria dos [filtros](xref:mvc/controllers/filters), `[Produces]` pode ser aplicada na ação, controlador ou escopo global:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="b4e8c-209">O filtro [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) anterior:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="b4e8c-210">Força todas as ações dentro do controlador a retornar respostas formatadas em JSON.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="b4e8c-211">Se outros formatadores estiverem configurados e o cliente especificar um formato diferente, o JSON será retornado.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="b4e8c-212">Para obter mais informações, consulte [filtros](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="b4e8c-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="b4e8c-213">Formatadores de caso especiais</span><span class="sxs-lookup"><span data-stu-id="b4e8c-213">Special case formatters</span></span>

<span data-ttu-id="b4e8c-214">Alguns casos especiais são implementados com formatadores internos.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="b4e8c-215">Por padrão, os tipos de retorno `string` são formatados como *texto/simples* (*texto/HTML* , se solicitado por meio do cabeçalho `Accept`).</span><span class="sxs-lookup"><span data-stu-id="b4e8c-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="b4e8c-216">Esse comportamento pode ser excluído removendo o <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="b4e8c-217">Os formatadores são removidos no método `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="b4e8c-218">As ações que têm um tipo de retorno de objeto de modelo retornam `204 No Content` ao retornar `null`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="b4e8c-219">Esse comportamento pode ser excluído removendo o <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="b4e8c-220">O código a seguir remove o `StringOutputFormatter` e o `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="b4e8c-221">Sem o `StringOutputFormatter`, o formatador JSON interno formata os tipos de retorno `string`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="b4e8c-222">Se o formatador JSON interno for removido e um formatador XML estiver disponível, o formatador XML formatará os tipos de retorno `string`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="b4e8c-223">Caso contrário, os tipos de retorno `string` retornarão `406 Not Acceptable`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="b4e8c-224">Sem o `HttpNoContentOutputFormatter`, os objetos nulos são formatados com o formatador configurado.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="b4e8c-225">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-225">For example:</span></span>

* <span data-ttu-id="b4e8c-226">O formatador JSON retorna uma resposta com um corpo de `null`.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="b4e8c-227">O formatador XML retorna um elemento XML vazio com o atributo `xsi:nil="true"` definido.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="b4e8c-228">Mapeamentos de URL de formato de resposta</span><span class="sxs-lookup"><span data-stu-id="b4e8c-228">Response format URL mappings</span></span>

<span data-ttu-id="b4e8c-229">Os clientes podem solicitar um formato específico como parte da URL, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="b4e8c-230">Na cadeia de caracteres de consulta ou parte do caminho.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="b4e8c-231">Usando uma extensão de arquivo específica de formato, como. xml ou. JSON.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="b4e8c-232">O mapeamento do caminho da solicitação deve ser especificado na rota que está sendo usada pela API.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="b4e8c-233">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b4e8c-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="b4e8c-234">A rota anterior permite que o formato solicitado seja especificado como uma extensão de arquivo opcional.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="b4e8c-235">O atributo [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) verifica a existência do valor de formato no `RouteData` e mapeia o formato de resposta para o formatador apropriado quando a resposta é criada.</span><span class="sxs-lookup"><span data-stu-id="b4e8c-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="b4e8c-236">Rota</span><span class="sxs-lookup"><span data-stu-id="b4e8c-236">Route</span></span>        |             <span data-ttu-id="b4e8c-237">Formatador</span><span class="sxs-lookup"><span data-stu-id="b4e8c-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="b4e8c-238">O formatador de saída padrão</span><span class="sxs-lookup"><span data-stu-id="b4e8c-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="b4e8c-239">O formatador JSON (se configurado)</span><span class="sxs-lookup"><span data-stu-id="b4e8c-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="b4e8c-240">O formatador XML (se configurado)</span><span class="sxs-lookup"><span data-stu-id="b4e8c-240">The XML formatter (if configured)</span></span>  |
