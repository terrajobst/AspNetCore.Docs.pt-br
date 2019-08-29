---
title: Model binding no ASP.NET Core
author: rick-anderson
description: Saiba como funciona o model binding no ASP.NET Core e como personalizar seu comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 05/31/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 298e305cf918117ec2d313060a7420a1e721a365
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975290"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="472f4-103">Model binding no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="472f4-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="472f4-104">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="472f4-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="472f4-105">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="472f4-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="472f4-106">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="472f4-106">What is Model binding</span></span>

<span data-ttu-id="472f4-107">Controladores e Razor Pages funcionam com os dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="472f4-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="472f4-108">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="472f4-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="472f4-109">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="472f4-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="472f4-110">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="472f4-110">Model binding automates this process.</span></span> <span data-ttu-id="472f4-111">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="472f4-111">The model binding system:</span></span>

* <span data-ttu-id="472f4-112">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="472f4-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="472f4-113">Fornece os dados a controladores e Razor Pages em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="472f4-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="472f4-114">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="472f4-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="472f4-115">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="472f4-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="472f4-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="472f4-116">Example</span></span>

<span data-ttu-id="472f4-117">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="472f4-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="472f4-118">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="472f4-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="472f4-119">O model binding passa pelas etapas a seguir depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="472f4-119">Model binding goes though the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="472f4-120">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="472f4-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="472f4-121">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="472f4-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="472f4-122">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="472f4-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="472f4-123">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="472f4-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="472f4-124">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="472f4-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="472f4-125">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="472f4-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="472f4-126">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="472f4-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="472f4-127">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="472f4-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="472f4-128">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="472f4-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="472f4-129">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="472f4-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="472f4-130">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="472f4-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="472f4-131">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="472f4-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="472f4-132">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="472f4-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="472f4-133">Destinos</span><span class="sxs-lookup"><span data-stu-id="472f4-133">Targets</span></span>

<span data-ttu-id="472f4-134">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="472f4-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="472f4-135">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="472f4-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="472f4-136">Os parâmetros do método do manipulador do Razor Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="472f4-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="472f4-137">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="472f4-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="472f4-138">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="472f4-138">[BindProperty] attribute</span></span>

<span data-ttu-id="472f4-139">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="472f4-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="472f4-140">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="472f4-140">[BindProperties] attribute</span></span>

<span data-ttu-id="472f4-141">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="472f4-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="472f4-142">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="472f4-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="472f4-143">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="472f4-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="472f4-144">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="472f4-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="472f4-145">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="472f4-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="472f4-146">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="472f4-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="472f4-147">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="472f4-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="472f4-148">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="472f4-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="472f4-149">Origens</span><span class="sxs-lookup"><span data-stu-id="472f4-149">Sources</span></span>

<span data-ttu-id="472f4-150">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="472f4-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="472f4-151">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="472f4-151">Form fields</span></span> 
1. <span data-ttu-id="472f4-152">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="472f4-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="472f4-153">Dados de rota</span><span class="sxs-lookup"><span data-stu-id="472f4-153">Route data</span></span>
1. <span data-ttu-id="472f4-154">Parâmetros de cadeia de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="472f4-154">Query string parameters</span></span>
1. <span data-ttu-id="472f4-155">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="472f4-155">Uploaded files</span></span> 

<span data-ttu-id="472f4-156">Para cada propriedade ou parâmetro de destino, as fontes são verificadas na ordem indicada nesta lista.</span><span class="sxs-lookup"><span data-stu-id="472f4-156">For each target parameter or property, the sources are scanned in the order indicated in this list.</span></span> <span data-ttu-id="472f4-157">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="472f4-157">There are a few exceptions:</span></span>

* <span data-ttu-id="472f4-158">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="472f4-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="472f4-159">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="472f4-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="472f4-160">Se o comportamento padrão não obtiver os resultados certos, você poderá usar um dos seguintes atributos para especificar a origem a ser usada para qualquer determinado destino.</span><span class="sxs-lookup"><span data-stu-id="472f4-160">If the default behavior doesn't give the right results, you can use one of the following attributes to specify the source to use for any given target.</span></span> 

* <span data-ttu-id="472f4-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) – obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="472f4-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="472f4-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) – obtém valores dos dados de rota.</span><span class="sxs-lookup"><span data-stu-id="472f4-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="472f4-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) – obtém valores de campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="472f4-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="472f4-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) – obtém os valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="472f4-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="472f4-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) – obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="472f4-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="472f4-166">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="472f4-166">These attributes:</span></span>

* <span data-ttu-id="472f4-167">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="472f4-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="472f4-168">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="472f4-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="472f4-169">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="472f4-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="472f4-170">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="472f4-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="472f4-171">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="472f4-171">[FromBody] attribute</span></span>

<span data-ttu-id="472f4-172">Os dados do corpo da solicitação são analisados usando formatadores de entrada específicos do tipo de conteúdo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="472f4-172">The request body data is parsed by using input formatters specific to the content type of the request.</span></span> <span data-ttu-id="472f4-173">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="472f4-173">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="472f4-174">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="472f4-174">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="472f4-175">O tempo de execução do ASP.NET Core delega a responsabilidade de ler o fluxo da solicitação ao formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="472f4-175">The ASP.NET Core runtime delegates the responsibility of reading the request stream to the input formatter.</span></span> <span data-ttu-id="472f4-176">Depois que o fluxo da solicitação é lido, ele não fica mais disponível para ser lido novamente para associar outros parâmetros `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="472f4-176">Once the request stream is read, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="472f4-177">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="472f4-177">Additional sources</span></span>

<span data-ttu-id="472f4-178">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="472f4-178">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="472f4-179">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="472f4-179">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="472f4-180">Por exemplo, talvez você queira dados de cookies ou estado de sessão.</span><span class="sxs-lookup"><span data-stu-id="472f4-180">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="472f4-181">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="472f4-181">To get data from a new source:</span></span>

* <span data-ttu-id="472f4-182">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="472f4-182">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="472f4-183">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="472f4-183">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="472f4-184">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="472f4-184">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="472f4-185">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) e um exemplo de [alocador](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) que obtém valores de cookies.</span><span class="sxs-lookup"><span data-stu-id="472f4-185">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="472f4-186">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="472f4-186">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="472f4-187">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="472f4-187">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="472f4-188">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="472f4-188">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="472f4-189">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="472f4-189">No source for a model property</span></span>

<span data-ttu-id="472f4-190">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="472f4-190">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="472f4-191">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="472f4-191">The property is set to null or a default value:</span></span>

* <span data-ttu-id="472f4-192">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="472f4-192">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="472f4-193">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="472f4-193">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="472f4-194">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="472f4-194">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="472f4-195">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="472f4-195">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="472f4-196">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="472f4-196">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="472f4-197">Se o estado do modelo precisar ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o [atributo [BindRequired]](#bindrequired-attribute).</span><span class="sxs-lookup"><span data-stu-id="472f4-197">If model state should be invalidated when nothing is found in form fields for a model property, use the [[BindRequired] attribute](#bindrequired-attribute).</span></span>

<span data-ttu-id="472f4-198">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="472f4-198">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="472f4-199">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="472f4-199">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="472f4-200">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="472f4-200">Type conversion errors</span></span>

<span data-ttu-id="472f4-201">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="472f4-201">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="472f4-202">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="472f4-202">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="472f4-203">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="472f4-203">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="472f4-204">Em uma Razor Page, exiba novamente a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="472f4-204">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="472f4-205">Validação no lado do cliente captura a maioria dos dados inválidos que de outra forma seriam enviados a um formulário do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="472f4-205">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="472f4-206">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="472f4-206">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="472f4-207">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="472f4-207">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="472f4-208">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="472f4-208">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="472f4-209">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="472f4-209">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="472f4-210">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="472f4-210">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="472f4-211">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="472f4-211">The invalid input does appear in an error message.</span></span> <span data-ttu-id="472f4-212">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="472f4-212">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="472f4-213">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="472f4-213">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="472f4-214">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="472f4-214">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="472f4-215">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="472f4-215">Simple types</span></span>

<span data-ttu-id="472f4-216">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="472f4-216">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="472f4-217">Booliano</span><span class="sxs-lookup"><span data-stu-id="472f4-217">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="472f4-218">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="472f4-218">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="472f4-219">Char</span><span class="sxs-lookup"><span data-stu-id="472f4-219">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="472f4-220">DateTime</span><span class="sxs-lookup"><span data-stu-id="472f4-220">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="472f4-221">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="472f4-221">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="472f4-222">Decimal</span><span class="sxs-lookup"><span data-stu-id="472f4-222">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="472f4-223">Duplo</span><span class="sxs-lookup"><span data-stu-id="472f4-223">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="472f4-224">Enum</span><span class="sxs-lookup"><span data-stu-id="472f4-224">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="472f4-225">Guid</span><span class="sxs-lookup"><span data-stu-id="472f4-225">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="472f4-226">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="472f4-226">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="472f4-227">Simples</span><span class="sxs-lookup"><span data-stu-id="472f4-227">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="472f4-228">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="472f4-228">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="472f4-229">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="472f4-229">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="472f4-230">Uri</span><span class="sxs-lookup"><span data-stu-id="472f4-230">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="472f4-231">Versão</span><span class="sxs-lookup"><span data-stu-id="472f4-231">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="472f4-232">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="472f4-232">Complex types</span></span>

<span data-ttu-id="472f4-233">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="472f4-233">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="472f4-234">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="472f4-234">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="472f4-235">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="472f4-235">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="472f4-236">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="472f4-236">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="472f4-237">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="472f4-237">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="472f4-238">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="472f4-238">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="472f4-239">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="472f4-239">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="472f4-240">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="472f4-240">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="472f4-241">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="472f4-241">Prefix = parameter name</span></span>

<span data-ttu-id="472f4-242">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="472f4-242">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="472f4-243">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="472f4-243">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="472f4-244">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="472f4-244">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="472f4-245">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="472f4-245">Prefix = property name</span></span>

<span data-ttu-id="472f4-246">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="472f4-246">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="472f4-247">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="472f4-247">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="472f4-248">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="472f4-248">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="472f4-249">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="472f4-249">Custom prefix</span></span>

<span data-ttu-id="472f4-250">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="472f4-250">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="472f4-251">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="472f4-251">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="472f4-252">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="472f4-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="472f4-253">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="472f4-253">Attributes for complex type targets</span></span>

<span data-ttu-id="472f4-254">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="472f4-254">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="472f4-255">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="472f4-255">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="472f4-256">Eles não afetam os formatadores de entrada, que processam corpos de solicitação XML e JSON postados.</span><span class="sxs-lookup"><span data-stu-id="472f4-256">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="472f4-257">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="472f4-257">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="472f4-258">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="472f4-258">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="472f4-259">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="472f4-259">[BindRequired] attribute</span></span>

<span data-ttu-id="472f4-260">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="472f4-260">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="472f4-261">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="472f4-261">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="472f4-262">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="472f4-262">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="472f4-263">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="472f4-263">[BindNever] attribute</span></span>

<span data-ttu-id="472f4-264">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="472f4-264">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="472f4-265">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="472f4-265">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="472f4-266">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="472f4-266">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="472f4-267">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="472f4-267">[Bind] attribute</span></span>

<span data-ttu-id="472f4-268">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="472f4-268">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="472f4-269">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="472f4-269">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="472f4-270">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="472f4-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="472f4-271">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="472f4-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="472f4-272">O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*.</span><span class="sxs-lookup"><span data-stu-id="472f4-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="472f4-273">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="472f4-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="472f4-274">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="472f4-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="472f4-275">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="472f4-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="472f4-276">Coleções</span><span class="sxs-lookup"><span data-stu-id="472f4-276">Collections</span></span>

<span data-ttu-id="472f4-277">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="472f4-277">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="472f4-278">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="472f4-278">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="472f4-279">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="472f4-279">For example:</span></span>

* <span data-ttu-id="472f4-280">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="472f4-280">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="472f4-281">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="472f4-281">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="472f4-282">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="472f4-282">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="472f4-283">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="472f4-283">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="472f4-284">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="472f4-284">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="472f4-285">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="472f4-285">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="472f4-286">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="472f4-286">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="472f4-287">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="472f4-287">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="472f4-288">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="472f4-288">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="472f4-289">Dicionários</span><span class="sxs-lookup"><span data-stu-id="472f4-289">Dictionaries</span></span>

<span data-ttu-id="472f4-290">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="472f4-290">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="472f4-291">Se nenhuma correspondência for encontrada, procurará um dos formatos compatível sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="472f4-291">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="472f4-292">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="472f4-292">For example:</span></span>

* <span data-ttu-id="472f4-293">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="472f4-293">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="472f4-294">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="472f4-294">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="472f4-295">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="472f4-295">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="472f4-296">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="472f4-296">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="472f4-297">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="472f4-297">selectedCourses["2000"]="Economics"</span></span>

## <a name="special-data-types"></a><span data-ttu-id="472f4-298">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="472f4-298">Special data types</span></span>

<span data-ttu-id="472f4-299">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="472f4-299">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="472f4-300">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="472f4-300">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="472f4-301">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="472f4-301">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="472f4-302">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="472f4-302">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="472f4-303">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="472f4-303">CancellationToken</span></span>

<span data-ttu-id="472f4-304">usado para cancelar a atividade em controladores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="472f4-304">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="472f4-305">FormCollection</span><span class="sxs-lookup"><span data-stu-id="472f4-305">FormCollection</span></span>

<span data-ttu-id="472f4-306">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="472f4-306">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="472f4-307">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="472f4-307">Input formatters</span></span>

<span data-ttu-id="472f4-308">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="472f4-308">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="472f4-309">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="472f4-309">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="472f4-310">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="472f4-310">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="472f4-311">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="472f4-311">You can add other formatters for other content types.</span></span>

<span data-ttu-id="472f4-312">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="472f4-312">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="472f4-313">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="472f4-313">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="472f4-314">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="472f4-314">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="472f4-315">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="472f4-315">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="472f4-316">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="472f4-316">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="472f4-317">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="472f4-317">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="472f4-318">Para obter mais informações, veja [Introdução à serialização XML](https://docs.microsoft.com/en-us/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="472f4-318">For more information, see [Introducing XML Serialization](https://docs.microsoft.com/en-us/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="472f4-319">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="472f4-319">Exclude specified types from model binding</span></span>

<span data-ttu-id="472f4-320">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="472f4-320">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="472f4-321">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="472f4-321">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="472f4-322">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="472f4-322">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="472f4-323">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="472f4-323">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="472f4-324">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="472f4-324">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="472f4-325">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="472f4-325">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="472f4-326">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="472f4-326">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="472f4-327">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="472f4-327">Custom model binders</span></span>

<span data-ttu-id="472f4-328">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="472f4-328">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="472f4-329">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="472f4-329">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="472f4-330">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="472f4-330">Manual model binding</span></span>

<span data-ttu-id="472f4-331">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="472f4-331">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="472f4-332">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="472f4-332">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="472f4-333">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="472f4-333">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="472f4-334">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="472f4-334">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="472f4-335">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="472f4-335">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="472f4-336">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="472f4-336">[FromServices] attribute</span></span>

<span data-ttu-id="472f4-337">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="472f4-337">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="472f4-338">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="472f4-338">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="472f4-339">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="472f4-339">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="472f4-340">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="472f4-340">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="472f4-341">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="472f4-341">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
