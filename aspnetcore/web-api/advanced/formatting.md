---
title: Formatar dados de resposta na API Web ASP.NET Core
author: ardalis
description: Saiba como formatar dados de resposta na API Web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 10/14/2016
uid: web-api/advanced/formatting
ms.openlocfilehash: 04f5b3c544cf3fc47c8321c8233535400fcf55f4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890761"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="166f9-103">Formatar dados de resposta na API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="166f9-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="166f9-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="166f9-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="166f9-105">O ASP.NET Core MVC tem suporte interno para formatação de dados de resposta, usando formatos fixos ou em resposta às especificações do cliente.</span><span class="sxs-lookup"><span data-stu-id="166f9-105">ASP.NET Core MVC has built-in support for formatting response data, using fixed formats or in response to client specifications.</span></span>

<span data-ttu-id="166f9-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="166f9-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="166f9-107">Resultados da ação específicos a um formato</span><span class="sxs-lookup"><span data-stu-id="166f9-107">Format-Specific Action Results</span></span>

<span data-ttu-id="166f9-108">Alguns tipos de resultado de ação são específicos a um formato específico, como `JsonResult` e `ContentResult`.</span><span class="sxs-lookup"><span data-stu-id="166f9-108">Some action result types are specific to a particular format, such as `JsonResult` and `ContentResult`.</span></span> <span data-ttu-id="166f9-109">As ações podem retornar resultados específicos que são sempre formatados de determinada maneira.</span><span class="sxs-lookup"><span data-stu-id="166f9-109">Actions can return specific results that are always formatted in a particular manner.</span></span> <span data-ttu-id="166f9-110">Por exemplo, o retorno de um `JsonResult` retornará dados formatados em JSON, independentemente das preferências do cliente.</span><span class="sxs-lookup"><span data-stu-id="166f9-110">For example, returning a `JsonResult` will return JSON-formatted data, regardless of client preferences.</span></span> <span data-ttu-id="166f9-111">Da mesma forma, o retorno de um `ContentResult` retornará dados de cadeia de caracteres formatados como texto sem formatação (assim como o simples retorno de uma cadeia de caracteres).</span><span class="sxs-lookup"><span data-stu-id="166f9-111">Likewise, returning a `ContentResult` will return plain-text-formatted string data (as will simply returning a string).</span></span>

> [!NOTE]
> <span data-ttu-id="166f9-112">Uma ação não precisa retornar nenhum tipo específico; o MVC dá suporte a qualquer valor retornado de objeto.</span><span class="sxs-lookup"><span data-stu-id="166f9-112">An action isn't required to return any particular type; MVC supports any object return value.</span></span> <span data-ttu-id="166f9-113">Se uma ação retorna uma implementação `IActionResult` e o controlador herda de `Controller`, os desenvolvedores têm muitos métodos auxiliares correspondentes a muitas das opções.</span><span class="sxs-lookup"><span data-stu-id="166f9-113">If an action returns an `IActionResult` implementation and the controller inherits from `Controller`, developers have many helper methods corresponding to many of the choices.</span></span> <span data-ttu-id="166f9-114">Os resultados de ações que retornam objetos que não são tipos `IActionResult` serão serializados usando a implementação `IOutputFormatter` apropriada.</span><span class="sxs-lookup"><span data-stu-id="166f9-114">Results from actions that return objects that are not `IActionResult` types will be serialized using the appropriate `IOutputFormatter` implementation.</span></span>

<span data-ttu-id="166f9-115">Para retornar dados em um formato específico de um controlador que herda da classe base `Controller`, use o método auxiliar interno `Json` para retornar JSON e `Content` para texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="166f9-115">To return data in a specific format from a controller that inherits from the `Controller` base class, use the built-in helper method `Json` to return JSON and `Content` for plain text.</span></span> <span data-ttu-id="166f9-116">O método de ação deve retornar o tipo de resultado específico (por exemplo, `JsonResult`) ou `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="166f9-116">Your action method should return either the specific result type (for instance, `JsonResult`) or `IActionResult`.</span></span>

<span data-ttu-id="166f9-117">Retornando dados formatados em JSON:</span><span class="sxs-lookup"><span data-stu-id="166f9-117">Returning JSON-formatted data:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

<span data-ttu-id="166f9-118">Resposta de exemplo desta ação:</span><span class="sxs-lookup"><span data-stu-id="166f9-118">Sample response from this action:</span></span>

![Guia Rede das Ferramentas para Desenvolvedores no Microsoft Edge mostrando que o Tipo de conteúdo da resposta é application/json](formatting/_static/json-response.png)

<span data-ttu-id="166f9-120">Observe que o tipo de conteúdo da resposta é `application/json`, conforme mostrado na lista de solicitações de rede e na seção Cabeçalhos de Resposta.</span><span class="sxs-lookup"><span data-stu-id="166f9-120">Note that the content type of the response is `application/json`, shown both in the list of network requests and in the Response Headers section.</span></span> <span data-ttu-id="166f9-121">Além disso, observe a lista de opções apresentada pelo navegador (nesse caso, Microsoft Edge) no cabeçalho Accept da seção Cabeçalhos de Solicitação.</span><span class="sxs-lookup"><span data-stu-id="166f9-121">Also note the list of options presented by the browser (in this case, Microsoft Edge) in the Accept header in the Request Headers section.</span></span> <span data-ttu-id="166f9-122">A técnica atual é ignorar esse cabeçalho. Abaixo, abordamos como obedecê-lo.</span><span class="sxs-lookup"><span data-stu-id="166f9-122">The current technique is ignoring this header; obeying it is discussed below.</span></span>

<span data-ttu-id="166f9-123">Para retornar dados formatados como texto sem formatação, use `ContentResult` e o auxiliar `Content`:</span><span class="sxs-lookup"><span data-stu-id="166f9-123">To return plain text formatted data, use `ContentResult` and the `Content` helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

<span data-ttu-id="166f9-124">Uma resposta dessa ação:</span><span class="sxs-lookup"><span data-stu-id="166f9-124">A response from this action:</span></span>

![Guia Rede das Ferramentas para Desenvolvedores no Microsoft Edge mostrando que o Tipo de conteúdo da resposta é text/plain](formatting/_static/text-response.png)

<span data-ttu-id="166f9-126">Observe, neste caso, que o `Content-Type` retornado é `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="166f9-126">Note in this case the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="166f9-127">Também obtenha esse mesmo comportamento usando apenas um tipo de resposta de cadeia de caracteres:</span><span class="sxs-lookup"><span data-stu-id="166f9-127">You can also achieve this same behavior using just a string response type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> <span data-ttu-id="166f9-128">Para ações não triviais com vários tipos de retorno ou várias opções (por exemplo, códigos de status HTTP diferentes com base no resultado das operações executadas), prefira `IActionResult` como o tipo de retorno.</span><span class="sxs-lookup"><span data-stu-id="166f9-128">For non-trivial actions with multiple return types or options (for example, different HTTP status codes based on the result of operations performed), prefer `IActionResult` as the return type.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="166f9-129">Negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="166f9-129">Content Negotiation</span></span>

<span data-ttu-id="166f9-130">A negociação de conteúdo (*conneg* de forma abreviada) ocorre quando o cliente especifica um [cabeçalho Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="166f9-130">Content negotiation (*conneg* for short) occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="166f9-131">O formato padrão usado pelo ASP.NET Core MVC é JSON.</span><span class="sxs-lookup"><span data-stu-id="166f9-131">The default format used by ASP.NET Core MVC is JSON.</span></span> <span data-ttu-id="166f9-132">A negociação de conteúdo é implementada por `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="166f9-132">Content negotiation is implemented by `ObjectResult`.</span></span> <span data-ttu-id="166f9-133">Ela também foi desenvolvida com base nos resultados de ação específica de código de status retornados dos métodos auxiliares (que se baseiam em `ObjectResult`).</span><span class="sxs-lookup"><span data-stu-id="166f9-133">It's also built into the status code specific action results returned from the helper methods (which are all based on `ObjectResult`).</span></span> <span data-ttu-id="166f9-134">Também retorne um tipo de modelo (uma classe que você definiu como o tipo de transferência de dados) e a estrutura o encapsulará automaticamente em um `ObjectResult` para você.</span><span class="sxs-lookup"><span data-stu-id="166f9-134">You can also return a model type (a class you've defined as your data transfer type) and the framework will automatically wrap it in an `ObjectResult` for you.</span></span>

<span data-ttu-id="166f9-135">O seguinte método de ação usa os métodos auxiliares `Ok` e `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="166f9-135">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

<span data-ttu-id="166f9-136">Uma resposta formatada em JSON será retornada, a menos que outro formato tenha sido solicitado e o servidor possa retornar o formato solicitado.</span><span class="sxs-lookup"><span data-stu-id="166f9-136">A JSON-formatted response will be returned unless another format was requested and the server can return the requested format.</span></span> <span data-ttu-id="166f9-137">Use uma ferramenta como o [Fiddler](http://www.telerik.com/fiddler) para criar uma solicitação que inclui um cabeçalho Accept e especifique outro formato.</span><span class="sxs-lookup"><span data-stu-id="166f9-137">You can use a tool like [Fiddler](http://www.telerik.com/fiddler) to create a request that includes an Accept header and specify another format.</span></span> <span data-ttu-id="166f9-138">Nesse caso, se o servidor tiver um *formatador* que pode produzir uma resposta no formato solicitado, o resultado será retornado no formato preferencial do cliente.</span><span class="sxs-lookup"><span data-stu-id="166f9-138">In that case, if the server has a *formatter* that can produce a response in the requested format, the result will be returned in the client-preferred format.</span></span>

![Console do Fiddler mostrando uma solicitação GET criada manualmente com o valor application/xml do cabeçalho Accept](formatting/_static/fiddler-composer.png)

<span data-ttu-id="166f9-140">Na captura de tela acima, o Fiddler Composer foi usado para gerar uma solicitação, especificando `Accept: application/xml`.</span><span class="sxs-lookup"><span data-stu-id="166f9-140">In the above screenshot, the Fiddler Composer has been used to generate a request, specifying `Accept: application/xml`.</span></span> <span data-ttu-id="166f9-141">Por padrão, o ASP.NET Core MVC dá suporte apenas ao JSON e, portanto, mesmo quando outro formato é especificado, o resultado retornado ainda é formatado em JSON.</span><span class="sxs-lookup"><span data-stu-id="166f9-141">By default, ASP.NET Core MVC only supports JSON, so even when another format is specified, the result returned is still JSON-formatted.</span></span> <span data-ttu-id="166f9-142">Você verá como adicionar outros formatadores na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="166f9-142">You'll see how to add additional formatters in the next section.</span></span>

<span data-ttu-id="166f9-143">As ações do controlador podem retornar POCOs (Objetos CLR Básicos); nesse caso, o ASP.NET Core MVC criará automaticamente um `ObjectResult` que encapsula o objeto.</span><span class="sxs-lookup"><span data-stu-id="166f9-143">Controller actions can return POCOs (Plain Old CLR Objects), in which case ASP.NET Core MVC automatically creates an `ObjectResult` for you that wraps the object.</span></span> <span data-ttu-id="166f9-144">O cliente receberá o objeto serializado formatado (o formato JSON é o padrão; você pode configurar XML ou outros formatos).</span><span class="sxs-lookup"><span data-stu-id="166f9-144">The client will get the formatted serialized object (JSON format is the default; you can configure XML or other formats).</span></span> <span data-ttu-id="166f9-145">Se o objeto que está sendo retornado for `null`, a estrutura retornará uma resposta `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="166f9-145">If the object being returned is `null`, then the framework will return a `204 No Content` response.</span></span>

<span data-ttu-id="166f9-146">Retornando um tipo de objeto:</span><span class="sxs-lookup"><span data-stu-id="166f9-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

<span data-ttu-id="166f9-147">Na amostra, uma solicitação para um alias de autor válido receberá uma resposta 200 OK com os dados do autor.</span><span class="sxs-lookup"><span data-stu-id="166f9-147">In the sample, a request for a valid author alias will receive a 200 OK response with the author's data.</span></span> <span data-ttu-id="166f9-148">Uma solicitação para um alias inválido receberá uma resposta 204 Sem Conteúdo.</span><span class="sxs-lookup"><span data-stu-id="166f9-148">A request for an invalid alias will receive a 204 No Content response.</span></span> <span data-ttu-id="166f9-149">Capturas de tela mostrando a resposta nos formatos XML e JSON são mostradas abaixo.</span><span class="sxs-lookup"><span data-stu-id="166f9-149">Screenshots showing the response in XML and JSON formats are shown below.</span></span>

### <a name="content-negotiation-process"></a><span data-ttu-id="166f9-150">Processo de negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="166f9-150">Content Negotiation Process</span></span>

<span data-ttu-id="166f9-151">A *negociação* de conteúdo ocorre apenas se um cabeçalho `Accept` é exibido na solicitação.</span><span class="sxs-lookup"><span data-stu-id="166f9-151">Content *negotiation* only takes place if an `Accept` header appears in the request.</span></span> <span data-ttu-id="166f9-152">Quando uma solicitação contiver um cabeçalho Accept, a estrutura enumerará os tipos de mídia no cabeçalho Accept na ordem de preferência e tentará encontrar um formatador que pode produzir uma resposta em um dos formatos especificados pelo cabeçalho Accept.</span><span class="sxs-lookup"><span data-stu-id="166f9-152">When a request contains an accept header, the framework will enumerate the media types in the accept header in preference order and will try to find a formatter that can produce a response in one of the formats specified by the accept header.</span></span> <span data-ttu-id="166f9-153">Caso nenhum formatador que pode atender à solicitação do cliente seja encontrado, a estrutura tentará encontrar o primeiro formatador que pode produzir uma resposta (a menos que o desenvolvedor tenha configurado a opção em `MvcOptions` para retornar 406 Não Aceitável).</span><span class="sxs-lookup"><span data-stu-id="166f9-153">In case no formatter is found that can satisfy the client's request, the framework will try to find the first formatter that can produce a response (unless the developer has configured the option on `MvcOptions` to return 406 Not Acceptable instead).</span></span> <span data-ttu-id="166f9-154">Se a solicitação especificar XML, mas o formatador XML não tiver sido configurado, o formatador JSON será usado.</span><span class="sxs-lookup"><span data-stu-id="166f9-154">If the request specifies XML, but the XML formatter has not been configured, then the JSON formatter will be used.</span></span> <span data-ttu-id="166f9-155">Geralmente, se nenhum formatador que pode fornecer o formato solicitado for configurado, o primeiro formatador que pode formatar o objeto será usado.</span><span class="sxs-lookup"><span data-stu-id="166f9-155">More generally, if no formatter is configured that can provide the requested format, then the first formatter that can format the object is used.</span></span> <span data-ttu-id="166f9-156">Se nenhum cabeçalho for fornecido, o primeiro formatador que pode manipular o objeto a ser retornado será usado para serializar a resposta.</span><span class="sxs-lookup"><span data-stu-id="166f9-156">If no header is given, the first formatter that can handle the object to be returned will be used to serialize the response.</span></span> <span data-ttu-id="166f9-157">Nesse caso, não ocorre nenhuma negociação – o servidor determina qual formato será usado.</span><span class="sxs-lookup"><span data-stu-id="166f9-157">In this case, there isn't any negotiation taking place - the server is determining what format it will use.</span></span>

> [!NOTE]
> <span data-ttu-id="166f9-158">Se o cabeçalho Accept contiver `*/*`, o Cabeçalho será ignorado, a menos que `RespectBrowserAcceptHeader` seja definido como verdadeiro em `MvcOptions`.</span><span class="sxs-lookup"><span data-stu-id="166f9-158">If the Accept header contains `*/*`, the Header will be ignored unless `RespectBrowserAcceptHeader` is set to true on `MvcOptions`.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="166f9-159">Navegadores e negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="166f9-159">Browsers and Content Negotiation</span></span>

<span data-ttu-id="166f9-160">Ao contrário dos clientes de API típicos, os navegadores da Web tendem a fornecer cabeçalhos `Accept` que incluem uma ampla variedade de formatos, incluindo caracteres curinga.</span><span class="sxs-lookup"><span data-stu-id="166f9-160">Unlike typical API clients, web browsers tend to supply `Accept` headers that include a wide array of formats, including wildcards.</span></span> <span data-ttu-id="166f9-161">Por padrão, quando a estrutura detectar que a solicitação é proveniente de um navegador, ela ignorará o cabeçalho `Accept` e retornará o conteúdo no formato padrão configurado do aplicativo (JSON, a menos que outro formato seja configurado).</span><span class="sxs-lookup"><span data-stu-id="166f9-161">By default, when the framework detects that the request is coming from a browser, it will ignore the `Accept` header and instead return the content in the application's configured default format (JSON unless otherwise configured).</span></span> <span data-ttu-id="166f9-162">Isso fornece uma experiência mais consistente no uso de diferentes navegadores para consumir APIs.</span><span class="sxs-lookup"><span data-stu-id="166f9-162">This provides a more consistent experience when using different browsers to consume APIs.</span></span>

<span data-ttu-id="166f9-163">Se preferir que o aplicativo respeite os cabeçalhos Accept do navegador, defina isso como parte da configuração do MVC definindo `RespectBrowserAcceptHeader` como `true` no método `ConfigureServices` em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="166f9-163">If you would prefer your application honor browser accept headers, you can configure this as part of MVC's configuration by setting `RespectBrowserAcceptHeader` to `true` in the `ConfigureServices` method in *Startup.cs*.</span></span>

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a><span data-ttu-id="166f9-164">Configurando formatadores</span><span class="sxs-lookup"><span data-stu-id="166f9-164">Configuring Formatters</span></span>

<span data-ttu-id="166f9-165">Se o aplicativo precisar dar suporte a outros formatos além do padrão de JSON, adicione pacotes NuGet e configure o MVC para dar suporte a eles.</span><span class="sxs-lookup"><span data-stu-id="166f9-165">If your application needs to support additional formats beyond the default of JSON, you can add NuGet packages and configure MVC to support them.</span></span> <span data-ttu-id="166f9-166">Há formatadores separados para entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="166f9-166">There are separate formatters for input and output.</span></span> <span data-ttu-id="166f9-167">Os formatadores de entrada são usados pelo [Model Binding](xref:mvc/models/model-binding); os formatadores de saída são usados para formatar as respostas.</span><span class="sxs-lookup"><span data-stu-id="166f9-167">Input formatters are used by [Model Binding](xref:mvc/models/model-binding); output formatters are used to format responses.</span></span> <span data-ttu-id="166f9-168">Também configure [Formatadores Personalizados](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="166f9-168">You can also configure [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

### <a name="adding-xml-format-support"></a><span data-ttu-id="166f9-169">Adicionando suporte para o formato XML</span><span class="sxs-lookup"><span data-stu-id="166f9-169">Adding XML Format Support</span></span>

<span data-ttu-id="166f9-170">Para adicionar suporte para a formatação XML, instale o pacote NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="166f9-170">To add support for XML formatting, install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

<span data-ttu-id="166f9-171">Adicione os XmlSerializerFormatters à configuração do MVC em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="166f9-171">Add the XmlSerializerFormatters to MVC's configuration in *Startup.cs*:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

<span data-ttu-id="166f9-172">Como alternativa, você pode adicionar apenas o formatador de saída:</span><span class="sxs-lookup"><span data-stu-id="166f9-172">Alternately, you can add just the output formatter:</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlSerializerOutputFormatter());
});
```

<span data-ttu-id="166f9-173">Essas duas abordagens serializarão os resultados usando `System.Xml.Serialization.XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="166f9-173">These two approaches will serialize results using `System.Xml.Serialization.XmlSerializer`.</span></span> <span data-ttu-id="166f9-174">Se preferir, use o `System.Runtime.Serialization.DataContractSerializer` adicionando seu formatador associado:</span><span class="sxs-lookup"><span data-stu-id="166f9-174">If you prefer, you can use the `System.Runtime.Serialization.DataContractSerializer` by adding its associated formatter:</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlDataContractSerializerOutputFormatter());
});
```

<span data-ttu-id="166f9-175">Depois de adicionar suporte para a formatação XML, os métodos do controlador deverão retornar o formato apropriado com base no cabeçalho `Accept` da solicitação, como demonstra este exemplo do Fiddler:</span><span class="sxs-lookup"><span data-stu-id="166f9-175">Once you've added support for XML formatting, your controller methods should return the appropriate format based on the request's `Accept` header, as this Fiddler example demonstrates:</span></span>

![Console do Fiddler: a guia Bruto da solicitação mostra que o valor do cabeçalho Accept é application/xml.](formatting/_static/xml-response.png)

<span data-ttu-id="166f9-178">Na guia Inspetores, é possível ver que a solicitação GET Bruta foi feita com um conjunto de cabeçalhos `Accept: application/xml`.</span><span class="sxs-lookup"><span data-stu-id="166f9-178">You can see in the Inspectors tab that the Raw GET request was made with an `Accept: application/xml` header set.</span></span> <span data-ttu-id="166f9-179">O painel de resposta mostra o cabeçalho `Content-Type: application/xml` e o objeto `Author` foi serializado em XML.</span><span class="sxs-lookup"><span data-stu-id="166f9-179">The response pane shows the `Content-Type: application/xml` header, and the `Author` object has been serialized to XML.</span></span>

<span data-ttu-id="166f9-180">Use a guia Criador para modificar a solicitação para especificar `application/json` no cabeçalho `Accept`.</span><span class="sxs-lookup"><span data-stu-id="166f9-180">Use the Composer tab to modify the request to specify `application/json` in the `Accept` header.</span></span> <span data-ttu-id="166f9-181">Execute a solicitação e a resposta será formatada como JSON:</span><span class="sxs-lookup"><span data-stu-id="166f9-181">Execute the request, and the response will be formatted as JSON:</span></span>

![Console do Fiddler: a guia Bruto da solicitação mostra que o valor do cabeçalho Accept é application/json.](formatting/_static/json-response-fiddler.png)

<span data-ttu-id="166f9-184">Nessa captura de tela, é possível ver a solicitação definir um cabeçalho `Accept: application/json` e a resposta especificar o mesmo como seu `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="166f9-184">In this screenshot, you can see the request sets a header of `Accept: application/json` and the response specifies the same as its `Content-Type`.</span></span> <span data-ttu-id="166f9-185">O objeto `Author` é mostrado no corpo da resposta, em formato JSON.</span><span class="sxs-lookup"><span data-stu-id="166f9-185">The `Author` object is shown in the body of the response, in JSON format.</span></span>

### <a name="forcing-a-particular-format"></a><span data-ttu-id="166f9-186">Forçando um formato específico</span><span class="sxs-lookup"><span data-stu-id="166f9-186">Forcing a Particular Format</span></span>

<span data-ttu-id="166f9-187">Caso deseje restringir os formatos de resposta de uma ação específica, aplique o filtro `[Produces]`.</span><span class="sxs-lookup"><span data-stu-id="166f9-187">If you would like to restrict the response formats for a specific action you can, you can apply the `[Produces]` filter.</span></span> <span data-ttu-id="166f9-188">O filtro `[Produces]` especifica os formatos de resposta de uma ação específica (ou o controlador).</span><span class="sxs-lookup"><span data-stu-id="166f9-188">The `[Produces]` filter specifies the response formats for a specific action (or controller).</span></span> <span data-ttu-id="166f9-189">Como a maioria dos [Filtros](xref:mvc/controllers/filters), isso pode ser aplicado no escopo da ação, do controlador ou global.</span><span class="sxs-lookup"><span data-stu-id="166f9-189">Like most [Filters](xref:mvc/controllers/filters), this can be applied at the action, controller, or global scope.</span></span>

```csharp
[Produces("application/json")]
public class AuthorsController
```

<span data-ttu-id="166f9-190">O filtro `[Produces]` forçará todas as ações em `AuthorsController` a retornar respostas formatadas em JSON, mesmo se outros formatadores foram configurados para o aplicativo e o cliente forneceu um cabeçalho `Accept` solicitando outro formato disponível.</span><span class="sxs-lookup"><span data-stu-id="166f9-190">The `[Produces]` filter will force all actions within the `AuthorsController` to return JSON-formatted responses, even if other formatters were configured for the application and the client provided an `Accept` header requesting a different, available format.</span></span> <span data-ttu-id="166f9-191">Consulte [Filtros](xref:mvc/controllers/filters) para saber mais, incluindo como aplicar filtros globalmente.</span><span class="sxs-lookup"><span data-stu-id="166f9-191">See [Filters](xref:mvc/controllers/filters) to learn more, including how to apply filters globally.</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="166f9-192">Formatadores de casos especiais</span><span class="sxs-lookup"><span data-stu-id="166f9-192">Special Case Formatters</span></span>

<span data-ttu-id="166f9-193">Alguns casos especiais são implementados com formatadores internos.</span><span class="sxs-lookup"><span data-stu-id="166f9-193">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="166f9-194">Por padrão, os tipos de retorno `string` serão formatados como *text/plain* (*text/html*, se solicitado por meio do cabeçalho `Accept`).</span><span class="sxs-lookup"><span data-stu-id="166f9-194">By default, `string` return types will be formatted as *text/plain* (*text/html* if requested via `Accept` header).</span></span> <span data-ttu-id="166f9-195">Esse comportamento pode ser removido com a remoção do `TextOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="166f9-195">This behavior can be removed by removing the `TextOutputFormatter`.</span></span> <span data-ttu-id="166f9-196">Remova formatadores no método `Configure` em *Startup.cs* (mostrado abaixo).</span><span class="sxs-lookup"><span data-stu-id="166f9-196">You remove formatters in the `Configure` method in *Startup.cs* (shown below).</span></span> <span data-ttu-id="166f9-197">Ações que têm um tipo de retorno de objeto de modelo retornarão uma resposta 204 Sem Conteúdo ao retornar `null`.</span><span class="sxs-lookup"><span data-stu-id="166f9-197">Actions that have a model object return type will return a 204 No Content response when returning `null`.</span></span> <span data-ttu-id="166f9-198">Esse comportamento pode ser removido com a remoção do `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="166f9-198">This behavior can be removed by removing the `HttpNoContentOutputFormatter`.</span></span> <span data-ttu-id="166f9-199">O código a seguir remove o `TextOutputFormatter` e o `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="166f9-199">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

<span data-ttu-id="166f9-200">Sem o `TextOutputFormatter`, os tipos de retorno `string` retornam 406 Não Aceitável, por exemplo.</span><span class="sxs-lookup"><span data-stu-id="166f9-200">Without the `TextOutputFormatter`, `string` return types return 406 Not Acceptable, for example.</span></span> <span data-ttu-id="166f9-201">Observe que se um formatador XML existir, ele formatará tipos de retorno `string` se o `TextOutputFormatter` for removido.</span><span class="sxs-lookup"><span data-stu-id="166f9-201">Note that if an XML formatter exists, it will format `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="166f9-202">Sem o `HttpNoContentOutputFormatter`, os objetos nulos são formatados com o formatador configurado.</span><span class="sxs-lookup"><span data-stu-id="166f9-202">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="166f9-203">Por exemplo, o formatador JSON apenas retornará uma resposta com um corpo `null`, enquanto o formatador XML retornará um elemento XML vazio com o atributo `xsi:nil="true"` definido.</span><span class="sxs-lookup"><span data-stu-id="166f9-203">For example, the JSON formatter will simply return a response with a body of `null`, while the XML formatter will return an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="166f9-204">Mapeamentos de URL do formato da resposta</span><span class="sxs-lookup"><span data-stu-id="166f9-204">Response Format URL Mappings</span></span>

<span data-ttu-id="166f9-205">Os clientes podem solicitar um formato específico como parte da URL, como na cadeia de caracteres de consulta ou parte do caminho, ou usando uma extensão de arquivo específica a um formato, como .xml ou .json.</span><span class="sxs-lookup"><span data-stu-id="166f9-205">Clients can request a particular format as part of the URL, such as in the query string or part of the path, or by using a format-specific file extension such as .xml or .json.</span></span> <span data-ttu-id="166f9-206">O mapeamento do caminho da solicitação deve ser especificado na rota que está sendo usada pela API.</span><span class="sxs-lookup"><span data-stu-id="166f9-206">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="166f9-207">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="166f9-207">For example:</span></span>

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

<span data-ttu-id="166f9-208">Essa rota permitirá que o formato solicitado seja especificado como uma extensão de arquivo opcional.</span><span class="sxs-lookup"><span data-stu-id="166f9-208">This route would allow the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="166f9-209">O atributo `[FormatFilter]` verifica a existência do valor de formato no `RouteData` e mapeará o formato da resposta para o formatador adequado quando a resposta for criada.</span><span class="sxs-lookup"><span data-stu-id="166f9-209">The `[FormatFilter]` attribute checks for the existence of the format value in the `RouteData` and will map the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="166f9-210">Rota</span><span class="sxs-lookup"><span data-stu-id="166f9-210">Route</span></span>            |             <span data-ttu-id="166f9-211">Formatador</span><span class="sxs-lookup"><span data-stu-id="166f9-211">Formatter</span></span>              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    <span data-ttu-id="166f9-212">O formatador de saída padrão</span><span class="sxs-lookup"><span data-stu-id="166f9-212">The default output formatter</span></span>    |
| `/products/GetById/5.json` | <span data-ttu-id="166f9-213">O formatador JSON (se configurado)</span><span class="sxs-lookup"><span data-stu-id="166f9-213">The JSON formatter (if configured)</span></span> |
| `/products/GetById/5.xml`  | <span data-ttu-id="166f9-214">O formatador XML (se configurado)</span><span class="sxs-lookup"><span data-stu-id="166f9-214">The XML formatter (if configured)</span></span>  |
