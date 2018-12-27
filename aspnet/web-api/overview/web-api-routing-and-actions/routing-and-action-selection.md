---
uid: web-api/overview/web-api-routing-and-actions/routing-and-action-selection
title: Roteamento e seleção de ação na API Web ASP.NET | Microsoft Docs
author: MikeWasson
description: ''
ms.author: riande
ms.date: 12/14/2018
ms.assetid: bcf2d223-cb7f-411e-be05-f43e96a14015
msc.legacyurl: /web-api/overview/web-api-routing-and-actions/routing-and-action-selection
msc.type: authoredcontent
ms.openlocfilehash: ce54181996376cb5dde3b91c10c16f33b3c6a570
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425166"
---
<a name="routing-and-action-selection-in-aspnet-web-api"></a><span data-ttu-id="97f93-102">Roteamento e seleção de ação na API Web ASP.NET</span><span class="sxs-lookup"><span data-stu-id="97f93-102">Routing and Action Selection in ASP.NET Web API</span></span>
====================
<span data-ttu-id="97f93-103">por [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="97f93-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="97f93-104">Este artigo descreve como o API Web ASP.NET encaminha uma solicitação HTTP para uma ação específica em um controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-104">This article describes how ASP.NET Web API routes an HTTP request to a particular action on a controller.</span></span>

> [!NOTE]
> <span data-ttu-id="97f93-105">Para obter uma visão geral do roteamento, consulte [roteamento na API Web ASP.NET](routing-in-aspnet-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="97f93-105">For a high-level overview of routing, see [Routing in ASP.NET Web API](routing-in-aspnet-web-api.md).</span></span>


<span data-ttu-id="97f93-106">Este artigo examina os detalhes do processo de roteamento.</span><span class="sxs-lookup"><span data-stu-id="97f93-106">This article looks at the details of the routing process.</span></span> <span data-ttu-id="97f93-107">Se você criar um projeto de API da Web e localizar que algumas solicitações não obtém roteado da maneira esperada, espero que este artigo vai ajudar.</span><span class="sxs-lookup"><span data-stu-id="97f93-107">If you create a Web API project and find that some requests don't get routed the way you expect, hopefully this article will help.</span></span>

<span data-ttu-id="97f93-108">Roteamento tem três fases principais:</span><span class="sxs-lookup"><span data-stu-id="97f93-108">Routing has three main phases:</span></span>

1. <span data-ttu-id="97f93-109">O URI para um modelo de rota de correspondência.</span><span class="sxs-lookup"><span data-stu-id="97f93-109">Matching the URI to a route template.</span></span>
2. <span data-ttu-id="97f93-110">Selecionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-110">Selecting a controller.</span></span>
3. <span data-ttu-id="97f93-111">Selecionando uma ação.</span><span class="sxs-lookup"><span data-stu-id="97f93-111">Selecting an action.</span></span>

<span data-ttu-id="97f93-112">Você pode substituir algumas partes do processo com seus próprios comportamentos personalizados.</span><span class="sxs-lookup"><span data-stu-id="97f93-112">You can replace some parts of the process with your own custom behaviors.</span></span> <span data-ttu-id="97f93-113">Neste artigo, descrevo o comportamento padrão.</span><span class="sxs-lookup"><span data-stu-id="97f93-113">In this article, I describe the default behavior.</span></span> <span data-ttu-id="97f93-114">No final, eu Observe os lugares onde você pode personalizar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="97f93-114">At the end, I note the places where you can customize the behavior.</span></span>

## <a name="route-templates"></a><span data-ttu-id="97f93-115">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="97f93-115">Route Templates</span></span>

<span data-ttu-id="97f93-116">Um modelo de rota é semelhante a um caminho de URI, mas ele pode ter valores de espaço reservado, indicada com chaves:</span><span class="sxs-lookup"><span data-stu-id="97f93-116">A route template looks similar to a URI path, but it can have placeholder values, indicated with curly braces:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample1.ps1)]

<span data-ttu-id="97f93-117">Quando você cria uma rota, você pode fornecer valores padrão para alguns ou todos os espaços reservados:</span><span class="sxs-lookup"><span data-stu-id="97f93-117">When you create a route, you can provide default values for some or all of the placeholders:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample2.cs)]

<span data-ttu-id="97f93-118">Você também pode fornecer restrições, que restringem como um segmento do URI pode corresponder a um espaço reservado:</span><span class="sxs-lookup"><span data-stu-id="97f93-118">You can also provide constraints, which restrict how a URI segment can match a placeholder:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample3.js)]

<span data-ttu-id="97f93-119">O framework tenta corresponder os segmentos no caminho de URI para o modelo.</span><span class="sxs-lookup"><span data-stu-id="97f93-119">The framework tries to match the segments in the URI path to the template.</span></span> <span data-ttu-id="97f93-120">Literais no modelo devem corresponder exatamente.</span><span class="sxs-lookup"><span data-stu-id="97f93-120">Literals in the template must match exactly.</span></span> <span data-ttu-id="97f93-121">Um espaço reservado corresponde a qualquer valor, a menos que você especifica restrições.</span><span class="sxs-lookup"><span data-stu-id="97f93-121">A placeholder matches any value, unless you specify constraints.</span></span> <span data-ttu-id="97f93-122">A estrutura não corresponde a outras partes do URI, como o nome do host ou os parâmetros de consulta.</span><span class="sxs-lookup"><span data-stu-id="97f93-122">The framework does not match other parts of the URI, such as the host name or the query parameters.</span></span> <span data-ttu-id="97f93-123">O framework selecionará a primeira rota na tabela de rotas que corresponda ao URI.</span><span class="sxs-lookup"><span data-stu-id="97f93-123">The framework selects the first route in the route table that matches the URI.</span></span>

<span data-ttu-id="97f93-124">Há dois espaços reservados especiais: "{controlador}" e "{action}".</span><span class="sxs-lookup"><span data-stu-id="97f93-124">There are two special placeholders: "{controller}" and "{action}".</span></span>

- <span data-ttu-id="97f93-125">"{controlador}" fornece o nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-125">"{controller}" provides the name of the controller.</span></span>
- <span data-ttu-id="97f93-126">"{action}" fornece o nome da ação.</span><span class="sxs-lookup"><span data-stu-id="97f93-126">"{action}" provides the name of the action.</span></span> <span data-ttu-id="97f93-127">Na API da Web, a convenção comum é omitir "{action}".</span><span class="sxs-lookup"><span data-stu-id="97f93-127">In Web API, the usual convention is to omit "{action}".</span></span>

### <a name="defaults"></a><span data-ttu-id="97f93-128">Padrão</span><span class="sxs-lookup"><span data-stu-id="97f93-128">Defaults</span></span>

<span data-ttu-id="97f93-129">Se você fornecer padrões, a rota corresponderá a um URI que não tem esses segmentos.</span><span class="sxs-lookup"><span data-stu-id="97f93-129">If you provide defaults, the route will match a URI that is missing those segments.</span></span> <span data-ttu-id="97f93-130">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="97f93-130">For example:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample4.cs)]

<span data-ttu-id="97f93-131">Os URIs `http://localhost/api/products/all` e `http://localhost/api/products` correspondem à rota anterior.</span><span class="sxs-lookup"><span data-stu-id="97f93-131">The URIs `http://localhost/api/products/all` and `http://localhost/api/products` match the preceding route.</span></span> <span data-ttu-id="97f93-132">No último URI, o ausente `{category}` segmento é atribuído o valor padrão `all`.</span><span class="sxs-lookup"><span data-stu-id="97f93-132">In the latter URI, the missing `{category}` segment is assigned the default value `all`.</span></span>

### <a name="route-dictionary"></a><span data-ttu-id="97f93-133">Dicionário de rota</span><span class="sxs-lookup"><span data-stu-id="97f93-133">Route Dictionary</span></span>

<span data-ttu-id="97f93-134">Se o framework encontra uma correspondência para um URI, ele cria um dicionário que contém o valor para cada espaço reservado.</span><span class="sxs-lookup"><span data-stu-id="97f93-134">If the framework finds a match for a URI, it creates a dictionary that contains the value for each placeholder.</span></span> <span data-ttu-id="97f93-135">As chaves são os nomes de espaço reservado, não incluindo as chaves.</span><span class="sxs-lookup"><span data-stu-id="97f93-135">The keys are the placeholder names, not including the curly braces.</span></span> <span data-ttu-id="97f93-136">Os valores são obtidos do caminho de URI ou dos padrões.</span><span class="sxs-lookup"><span data-stu-id="97f93-136">The values are taken from the URI path or from the defaults.</span></span> <span data-ttu-id="97f93-137">O dicionário é armazenado na **IHttpRouteData** objeto.</span><span class="sxs-lookup"><span data-stu-id="97f93-137">The dictionary is stored in the **IHttpRouteData** object.</span></span>

<span data-ttu-id="97f93-138">Durante essa fase de correspondência de rotas, o especial "{controlador}" e espaços reservados de "{action}" são tratados como os outros espaços reservados.</span><span class="sxs-lookup"><span data-stu-id="97f93-138">During this route-matching phase, the special "{controller}" and "{action}" placeholders are treated just like the other placeholders.</span></span> <span data-ttu-id="97f93-139">Eles simplesmente são armazenados no dicionário com os outros valores.</span><span class="sxs-lookup"><span data-stu-id="97f93-139">They are simply stored in the dictionary with the other values.</span></span>

<span data-ttu-id="97f93-140">Um padrão pode ter o valor especial **RouteParameter.Optional**.</span><span class="sxs-lookup"><span data-stu-id="97f93-140">A default can have the special value **RouteParameter.Optional**.</span></span> <span data-ttu-id="97f93-141">Se um espaço reservado é atribuído a esse valor, o valor não é adicionado ao dicionário de rota.</span><span class="sxs-lookup"><span data-stu-id="97f93-141">If a placeholder gets assigned this value, the value is not added to the route dictionary.</span></span> <span data-ttu-id="97f93-142">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="97f93-142">For example:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample5.cs)]

<span data-ttu-id="97f93-143">Para o caminho URI "api/produtos", o dicionário de rota conterá:</span><span class="sxs-lookup"><span data-stu-id="97f93-143">For the URI path "api/products", the route dictionary will contain:</span></span>

- <span data-ttu-id="97f93-144">controlador: "produtos"</span><span class="sxs-lookup"><span data-stu-id="97f93-144">controller: "products"</span></span>
- <span data-ttu-id="97f93-145">categoria: "all"</span><span class="sxs-lookup"><span data-stu-id="97f93-145">category: "all"</span></span>

<span data-ttu-id="97f93-146">Para "api/produtos/toys/123", no entanto, o dicionário de rota conterá:</span><span class="sxs-lookup"><span data-stu-id="97f93-146">For "api/products/toys/123", however, the route dictionary will contain:</span></span>

- <span data-ttu-id="97f93-147">controlador: "produtos"</span><span class="sxs-lookup"><span data-stu-id="97f93-147">controller: "products"</span></span>
- <span data-ttu-id="97f93-148">categoria: "toys"</span><span class="sxs-lookup"><span data-stu-id="97f93-148">category: "toys"</span></span>
- <span data-ttu-id="97f93-149">ID: "123"</span><span class="sxs-lookup"><span data-stu-id="97f93-149">id: "123"</span></span>

<span data-ttu-id="97f93-150">Os padrões também podem incluir um valor que não aparece em qualquer lugar no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="97f93-150">The defaults can also include a value that does not appear anywhere in the route template.</span></span> <span data-ttu-id="97f93-151">Se a rota corresponde, esse valor é armazenado no dicionário.</span><span class="sxs-lookup"><span data-stu-id="97f93-151">If the route matches, that value is stored in the dictionary.</span></span> <span data-ttu-id="97f93-152">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="97f93-152">For example:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample6.cs)]

<span data-ttu-id="97f93-153">Se o caminho do URI é "api/raiz/8", o dicionário conterá dois valores:</span><span class="sxs-lookup"><span data-stu-id="97f93-153">If the URI path is "api/root/8", the dictionary will contain two values:</span></span>

- <span data-ttu-id="97f93-154">controlador: "clientes"</span><span class="sxs-lookup"><span data-stu-id="97f93-154">controller: "customers"</span></span>
- <span data-ttu-id="97f93-155">ID: "8"</span><span class="sxs-lookup"><span data-stu-id="97f93-155">id: "8"</span></span>

## <a name="selecting-a-controller"></a><span data-ttu-id="97f93-156">Selecionar um controlador</span><span class="sxs-lookup"><span data-stu-id="97f93-156">Selecting a Controller</span></span>

<span data-ttu-id="97f93-157">Seleção de controlador é tratada pelos **IHttpControllerSelector.SelectController** método.</span><span class="sxs-lookup"><span data-stu-id="97f93-157">Controller selection is handled by the **IHttpControllerSelector.SelectController** method.</span></span> <span data-ttu-id="97f93-158">Esse método usa um **HttpRequestMessage** instância e retorna um **HttpControllerDescriptor**.</span><span class="sxs-lookup"><span data-stu-id="97f93-158">This method takes an **HttpRequestMessage** instance and returns an **HttpControllerDescriptor**.</span></span> <span data-ttu-id="97f93-159">A implementação padrão é fornecida pelo **DefaultHttpControllerSelector** classe.</span><span class="sxs-lookup"><span data-stu-id="97f93-159">The default implementation is provided by the **DefaultHttpControllerSelector** class.</span></span> <span data-ttu-id="97f93-160">Essa classe usa um algoritmo simples:</span><span class="sxs-lookup"><span data-stu-id="97f93-160">This class uses a straightforward algorithm:</span></span>

1. <span data-ttu-id="97f93-161">Examine o dicionário de rota para a chave "controller".</span><span class="sxs-lookup"><span data-stu-id="97f93-161">Look in the route dictionary for the key "controller".</span></span>
2. <span data-ttu-id="97f93-162">O valor para essa chave e acrescente a cadeia de caracteres "Controller" para obter o nome do tipo de controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-162">Take the value for this key and append the string "Controller" to get the controller type name.</span></span>
3. <span data-ttu-id="97f93-163">Procure por um controlador de API da Web com esse nome de tipo.</span><span class="sxs-lookup"><span data-stu-id="97f93-163">Look for a Web API controller with this type name.</span></span>

<span data-ttu-id="97f93-164">Por exemplo, se o dicionário de rota contém o par chave-valor "controlador" = "produtos", em seguida, o tipo de controlador é "ProductsController".</span><span class="sxs-lookup"><span data-stu-id="97f93-164">For example, if the route dictionary contains the key-value pair "controller" = "products", then the controller type is "ProductsController".</span></span> <span data-ttu-id="97f93-165">Se não houver nenhum tipo de correspondência ou várias correspondências, a estrutura retornará um erro ao cliente.</span><span class="sxs-lookup"><span data-stu-id="97f93-165">If there is no matching type, or multiple matches, the framework returns an error to the client.</span></span>

<span data-ttu-id="97f93-166">Etapa 3, **DefaultHttpControllerSelector** usa o **IHttpControllerTypeResolver** a interface para obter a lista de tipos de controlador de API da Web.</span><span class="sxs-lookup"><span data-stu-id="97f93-166">For step 3, **DefaultHttpControllerSelector** uses the **IHttpControllerTypeResolver** interface to get the list of Web API controller types.</span></span> <span data-ttu-id="97f93-167">A implementação padrão de **IHttpControllerTypeResolver** retorna todas as classes públicas que implementam (a) **IHttpController**, (b) são não abstrata e (c) tem um nome que termina em "Controller".</span><span class="sxs-lookup"><span data-stu-id="97f93-167">The default implementation of **IHttpControllerTypeResolver** returns all public classes that (a) implement **IHttpController**, (b) are not abstract, and (c) have a name that ends in "Controller".</span></span>

## <a name="action-selection"></a><span data-ttu-id="97f93-168">Seleção de ação</span><span class="sxs-lookup"><span data-stu-id="97f93-168">Action Selection</span></span>

<span data-ttu-id="97f93-169">Depois de selecionar o controlador, o framework seleciona a ação chamando o **IHttpActionSelector.SelectAction** método.</span><span class="sxs-lookup"><span data-stu-id="97f93-169">After selecting the controller, the framework selects the action by calling the **IHttpActionSelector.SelectAction** method.</span></span> <span data-ttu-id="97f93-170">Esse método usa um **HttpControllerContext** e retorna um **HttpActionDescriptor**.</span><span class="sxs-lookup"><span data-stu-id="97f93-170">This method takes an **HttpControllerContext** and returns an **HttpActionDescriptor**.</span></span>

<span data-ttu-id="97f93-171">A implementação padrão é fornecida pelo **ApiControllerActionSelector** classe.</span><span class="sxs-lookup"><span data-stu-id="97f93-171">The default implementation is provided by the **ApiControllerActionSelector** class.</span></span> <span data-ttu-id="97f93-172">Para selecionar uma ação, ele aborda o seguinte:</span><span class="sxs-lookup"><span data-stu-id="97f93-172">To select an action, it looks at the following:</span></span>

- <span data-ttu-id="97f93-173">O método HTTP da solicitação.</span><span class="sxs-lookup"><span data-stu-id="97f93-173">The HTTP method of the request.</span></span>
- <span data-ttu-id="97f93-174">O espaço reservado de "{action}" no modelo de rota, se estiver presente.</span><span class="sxs-lookup"><span data-stu-id="97f93-174">The "{action}" placeholder in the route template, if present.</span></span>
- <span data-ttu-id="97f93-175">Os parâmetros das ações no controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-175">The parameters of the actions on the controller.</span></span>

<span data-ttu-id="97f93-176">Antes de examinar o algoritmo de seleção, precisamos entender algumas coisas sobre as ações do controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-176">Before looking at the selection algorithm, we need to understand some things about controller actions.</span></span>

<span data-ttu-id="97f93-177">**Quais métodos do controlador são considerados "ações"?**</span><span class="sxs-lookup"><span data-stu-id="97f93-177">**Which methods on the controller are considered "actions"?**</span></span> <span data-ttu-id="97f93-178">Ao selecionar uma ação, a estrutura considera apenas a métodos de instância pública no controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-178">When selecting an action, the framework only looks at public instance methods on the controller.</span></span> <span data-ttu-id="97f93-179">Além disso, ele exclui ["nome especial"](https://msdn.microsoft.com/library/system.reflection.methodbase.isspecialname) métodos (construtores, eventos, sobrecargas de operador e assim por diante) e métodos herdados da **ApiController** classe.</span><span class="sxs-lookup"><span data-stu-id="97f93-179">Also, it excludes ["special name"](https://msdn.microsoft.com/library/system.reflection.methodbase.isspecialname) methods (constructors, events, operator overloads, and so forth), and methods inherited from the **ApiController** class.</span></span>

<span data-ttu-id="97f93-180">**Métodos HTTP.**</span><span class="sxs-lookup"><span data-stu-id="97f93-180">**HTTP Methods.**</span></span> <span data-ttu-id="97f93-181">O framework escolhe apenas as ações que corresponde ao método HTTP da solicitação, determinado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="97f93-181">The framework only chooses actions that match the HTTP method of the request, determined as follows:</span></span>

1. <span data-ttu-id="97f93-182">Você pode especificar o método HTTP com um atributo: **AcceptVerbs**, **HttpDelete**, **HttpGet**, **HttpHead**, **HttpOptions**, **HttpPatch**, **HttpPost**, ou **HttpPut**.</span><span class="sxs-lookup"><span data-stu-id="97f93-182">You can specify the HTTP method with an attribute: **AcceptVerbs**, **HttpDelete**, **HttpGet**, **HttpHead**, **HttpOptions**, **HttpPatch**, **HttpPost**, or **HttpPut**.</span></span>
2. <span data-ttu-id="97f93-183">Caso contrário, se o nome do método do controlador começa com "Get", "Post", "Put", "Delete", "Head", "Opções" ou "Patch", em seguida, por convenção, a ação dá suporte a esse método HTTP.</span><span class="sxs-lookup"><span data-stu-id="97f93-183">Otherwise, if the name of the controller method starts with "Get", "Post", "Put", "Delete", "Head", "Options", or "Patch", then by convention the action supports that HTTP method.</span></span>
3. <span data-ttu-id="97f93-184">Se nenhuma das opções acima, o método dá suporte à POSTAGEM.</span><span class="sxs-lookup"><span data-stu-id="97f93-184">If none of the above, the method supports POST.</span></span>

<span data-ttu-id="97f93-185">**Associações de parâmetro.**</span><span class="sxs-lookup"><span data-stu-id="97f93-185">**Parameter Bindings.**</span></span> <span data-ttu-id="97f93-186">Uma associação de parâmetro é como a API da Web cria um valor para um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="97f93-186">A parameter binding is how Web API creates a value for a parameter.</span></span> <span data-ttu-id="97f93-187">Aqui está a regra padrão para a associação de parâmetro:</span><span class="sxs-lookup"><span data-stu-id="97f93-187">Here is the default rule for parameter binding:</span></span>

- <span data-ttu-id="97f93-188">Tipos simples são retirados do URI.</span><span class="sxs-lookup"><span data-stu-id="97f93-188">Simple types are taken from the URI.</span></span>
- <span data-ttu-id="97f93-189">Tipos complexos são retirados do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="97f93-189">Complex types are taken from the request body.</span></span>

<span data-ttu-id="97f93-190">Tipos simples incluem todos os [tipos primitivos do .NET Framework](https://msdn.microsoft.com/library/system.type.isprimitive), além de **DateTime**, **Decimal**, **Guid**, **cadeia de caracteres** , e **TimeSpan**.</span><span class="sxs-lookup"><span data-stu-id="97f93-190">Simple types include all of the [.NET Framework primitive types](https://msdn.microsoft.com/library/system.type.isprimitive), plus **DateTime**, **Decimal**, **Guid**, **String**, and **TimeSpan**.</span></span> <span data-ttu-id="97f93-191">Para cada ação, no máximo um parâmetro pode ler o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="97f93-191">For each action, at most one parameter can read the request body.</span></span>

> [!NOTE]
> <span data-ttu-id="97f93-192">É possível substituir as regras de associação padrão.</span><span class="sxs-lookup"><span data-stu-id="97f93-192">It is possible to override the default binding rules.</span></span> <span data-ttu-id="97f93-193">Ver [associação de parâmetros de API da Web nos bastidores](https://blogs.msdn.com/b/jmstall/archive/2012/05/11/webapi-parameter-binding-under-the-hood.aspx).</span><span class="sxs-lookup"><span data-stu-id="97f93-193">See [WebAPI Parameter binding under the hood](https://blogs.msdn.com/b/jmstall/archive/2012/05/11/webapi-parameter-binding-under-the-hood.aspx).</span></span>


<span data-ttu-id="97f93-194">Com esse plano de fundo, aqui está o algoritmo de seleção de ação.</span><span class="sxs-lookup"><span data-stu-id="97f93-194">With that background, here is the action selection algorithm.</span></span>

1. <span data-ttu-id="97f93-195">Crie uma lista de todas as ações no controlador que corresponde ao método de solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="97f93-195">Create a list of all actions on the controller that match the HTTP request method.</span></span>
2. <span data-ttu-id="97f93-196">Se o dicionário de rota tem uma entrada de "ação", remova as ações cujo nome não corresponde a esse valor.</span><span class="sxs-lookup"><span data-stu-id="97f93-196">If the route dictionary has an "action" entry, remove actions whose name does not match this value.</span></span>
3. <span data-ttu-id="97f93-197">Tenta corresponder os parâmetros de ação para o URI, da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="97f93-197">Try to match action parameters to the URI, as follows:</span></span> 

    1. <span data-ttu-id="97f93-198">Para cada ação, obtenha uma lista de parâmetros que são um tipo simples, onde a associação obtém o parâmetro do URI.</span><span class="sxs-lookup"><span data-stu-id="97f93-198">For each action, get a list of the parameters that are a simple type, where the binding gets the parameter from the URI.</span></span> <span data-ttu-id="97f93-199">Exclua parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="97f93-199">Exclude optional parameters.</span></span>
    2. <span data-ttu-id="97f93-200">Nesta lista, tente localizar uma correspondência para cada nome de parâmetro, no dicionário de rota ou na cadeia de caracteres de consulta do URI.</span><span class="sxs-lookup"><span data-stu-id="97f93-200">From this list, try to find a match for each parameter name, either in the route dictionary or in the URI query string.</span></span> <span data-ttu-id="97f93-201">Correspondências diferenciam maiusculas de minúsculas e não dependem da ordem do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="97f93-201">Matches are case insensitive and do not depend on the parameter order.</span></span>
    3. <span data-ttu-id="97f93-202">Selecione uma ação em que cada parâmetro na lista tem uma correspondência no URI.</span><span class="sxs-lookup"><span data-stu-id="97f93-202">Select an action where every parameter in the list has a match in the URI.</span></span>
    4. <span data-ttu-id="97f93-203">Se mais que uma ação atenda a esses critérios, escolha aquela com as maioria das correspondências de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="97f93-203">If more that one action meets these criteria, pick the one with the most parameter matches.</span></span>
4. <span data-ttu-id="97f93-204">Ignorar as ações com o **[NonAction]** atributo.</span><span class="sxs-lookup"><span data-stu-id="97f93-204">Ignore actions with the **[NonAction]** attribute.</span></span>

<span data-ttu-id="97f93-205">Etapa 3 # é provavelmente mais confusos.</span><span class="sxs-lookup"><span data-stu-id="97f93-205">Step #3 is probably the most confusing.</span></span> <span data-ttu-id="97f93-206">A ideia básica é que um parâmetro pode obter seu valor do URI, de corpo da solicitação ou de uma associação personalizada.</span><span class="sxs-lookup"><span data-stu-id="97f93-206">The basic idea is that a parameter can get its value either from the URI, from the request body, or from a custom binding.</span></span> <span data-ttu-id="97f93-207">Para parâmetros que vêm do URI, queremos garantir que o URI, na verdade, contém um valor para esse parâmetro, no caminho (por meio do dicionário de rota) ou na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="97f93-207">For parameters that come from the URI, we want to ensure that the URI actually contains a value for that parameter, either in the path (via the route dictionary) or in the query string.</span></span>

<span data-ttu-id="97f93-208">Por exemplo, considere a seguinte ação:</span><span class="sxs-lookup"><span data-stu-id="97f93-208">For example, consider the following action:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample7.cs)]

<span data-ttu-id="97f93-209">O *id* parâmetro está associado ao URI.</span><span class="sxs-lookup"><span data-stu-id="97f93-209">The *id* parameter binds to the URI.</span></span> <span data-ttu-id="97f93-210">Portanto, essa ação só pode corresponder a um URI que contém um valor para "id", no dicionário de rota ou na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="97f93-210">Therefore, this action can only match a URI that contains a value for "id", either in the route dictionary or in the query string.</span></span>

<span data-ttu-id="97f93-211">Parâmetros opcionais são uma exceção, pois eles são opcionais.</span><span class="sxs-lookup"><span data-stu-id="97f93-211">Optional parameters are an exception, because they are optional.</span></span> <span data-ttu-id="97f93-212">Para um parâmetro opcional, é Okey se a associação não é possível obter o valor do URI.</span><span class="sxs-lookup"><span data-stu-id="97f93-212">For an optional parameter, it's OK if the binding can't get the value from the URI.</span></span>

<span data-ttu-id="97f93-213">Tipos complexos são uma exceção por uma razão diferente.</span><span class="sxs-lookup"><span data-stu-id="97f93-213">Complex types are an exception for a different reason.</span></span> <span data-ttu-id="97f93-214">Um tipo complexo só pode ser associado ao URI por meio de uma associação personalizada.</span><span class="sxs-lookup"><span data-stu-id="97f93-214">A complex type can only bind to the URI through a custom binding.</span></span> <span data-ttu-id="97f93-215">Mas, nesse caso, a estrutura não é possível saber com antecedência se o parâmetro se associaria a um URI específico.</span><span class="sxs-lookup"><span data-stu-id="97f93-215">But in that case, the framework cannot know in advance whether the parameter would bind to a particular URI.</span></span> <span data-ttu-id="97f93-216">Para descobrir, seriam necessárias invocar a associação.</span><span class="sxs-lookup"><span data-stu-id="97f93-216">To find out, it would need to invoke the binding.</span></span> <span data-ttu-id="97f93-217">É o objetivo do algoritmo de seleção Selecionar uma ação a partir da descrição estática, antes de chamar quaisquer associações.</span><span class="sxs-lookup"><span data-stu-id="97f93-217">The goal of the selection algorithm is to select an action from the static description, before invoking any bindings.</span></span> <span data-ttu-id="97f93-218">Portanto, os tipos complexos são excluídos do algoritmo de correspondência.</span><span class="sxs-lookup"><span data-stu-id="97f93-218">Therefore, complex types are excluded from the matching algorithm.</span></span>

<span data-ttu-id="97f93-219">Depois que a ação é selecionada, todas as associações de parâmetro são invocadas.</span><span class="sxs-lookup"><span data-stu-id="97f93-219">After the action is selected, all parameter bindings are invoked.</span></span>

<span data-ttu-id="97f93-220">Resumo:</span><span class="sxs-lookup"><span data-stu-id="97f93-220">Summary:</span></span>

- <span data-ttu-id="97f93-221">A ação deve coincidir com o método HTTP da solicitação.</span><span class="sxs-lookup"><span data-stu-id="97f93-221">The action must match the HTTP method of the request.</span></span>
- <span data-ttu-id="97f93-222">O nome da ação deve corresponder à entrada de "ação" no dicionário de rota, se estiver presente.</span><span class="sxs-lookup"><span data-stu-id="97f93-222">The action name must match the "action" entry in the route dictionary, if present.</span></span>
- <span data-ttu-id="97f93-223">Para cada parâmetro da ação, se o parâmetro é obtido do URI, em seguida, o nome do parâmetro deve ser encontrado no dicionário de rota ou na cadeia de caracteres de consulta do URI.</span><span class="sxs-lookup"><span data-stu-id="97f93-223">For every parameter of the action, if the parameter is taken from the URI, then the parameter name must be found either in the route dictionary or in the URI query string.</span></span> <span data-ttu-id="97f93-224">(Parâmetros com tipos complexos e parâmetros opcionais são excluídos.)</span><span class="sxs-lookup"><span data-stu-id="97f93-224">(Optional parameters and parameters with complex types are excluded.)</span></span>
- <span data-ttu-id="97f93-225">Tenta corresponder o maior número de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="97f93-225">Try to match the most number of parameters.</span></span> <span data-ttu-id="97f93-226">A melhor correspondência pode ser um método sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="97f93-226">The best match might be a method with no parameters.</span></span>

## <a name="extended-example"></a><span data-ttu-id="97f93-227">Exemplo estendido</span><span class="sxs-lookup"><span data-stu-id="97f93-227">Extended Example</span></span>

<span data-ttu-id="97f93-228">Rotas:</span><span class="sxs-lookup"><span data-stu-id="97f93-228">Routes:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample8.cs)]

<span data-ttu-id="97f93-229">Controlador:</span><span class="sxs-lookup"><span data-stu-id="97f93-229">Controller:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample9.cs)]

<span data-ttu-id="97f93-230">Solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="97f93-230">HTTP request:</span></span>

[!code-console[Main](routing-and-action-selection/samples/sample10.cmd)]

### <a name="route-matching"></a><span data-ttu-id="97f93-231">Correspondência de rotas</span><span class="sxs-lookup"><span data-stu-id="97f93-231">Route Matching</span></span>

<span data-ttu-id="97f93-232">O URI coincida com a rota chamada "DefaultApi".</span><span class="sxs-lookup"><span data-stu-id="97f93-232">The URI matches the route named "DefaultApi".</span></span> <span data-ttu-id="97f93-233">O dicionário de rota contém as seguintes entradas:</span><span class="sxs-lookup"><span data-stu-id="97f93-233">The route dictionary contains the following entries:</span></span>

- <span data-ttu-id="97f93-234">controlador: "produtos"</span><span class="sxs-lookup"><span data-stu-id="97f93-234">controller: "products"</span></span>
- <span data-ttu-id="97f93-235">ID: "1"</span><span class="sxs-lookup"><span data-stu-id="97f93-235">id: "1"</span></span>

<span data-ttu-id="97f93-236">O dicionário de rota não contêm parâmetros de cadeia de caracteres de consulta, "versão" e "Detalhes", mas eles ainda serão considerados durante a seleção de ação.</span><span class="sxs-lookup"><span data-stu-id="97f93-236">The route dictionary does not contain the query string parameters, "version" and "details", but these will still be considered during action selection.</span></span>

### <a name="controller-selection"></a><span data-ttu-id="97f93-237">Seleção de controlador</span><span class="sxs-lookup"><span data-stu-id="97f93-237">Controller Selection</span></span>

<span data-ttu-id="97f93-238">Da entrada "controlador" no dicionário de rota, o tipo de controlador é `ProductsController`.</span><span class="sxs-lookup"><span data-stu-id="97f93-238">From the "controller" entry in the route dictionary, the controller type is `ProductsController`.</span></span>

### <a name="action-selection"></a><span data-ttu-id="97f93-239">Seleção de ação</span><span class="sxs-lookup"><span data-stu-id="97f93-239">Action Selection</span></span>

<span data-ttu-id="97f93-240">A solicitação HTTP é uma solicitação GET.</span><span class="sxs-lookup"><span data-stu-id="97f93-240">The HTTP request is a GET request.</span></span> <span data-ttu-id="97f93-241">As ações do controlador que dão suporte a GET são `GetAll`, `GetById`, e `FindProductsByName`.</span><span class="sxs-lookup"><span data-stu-id="97f93-241">The controller actions that support GET are `GetAll`, `GetById`, and `FindProductsByName`.</span></span> <span data-ttu-id="97f93-242">O dicionário de rota não contém uma entrada para "action", portanto, não precisamos corresponder ao nome da ação.</span><span class="sxs-lookup"><span data-stu-id="97f93-242">The route dictionary does not contain an entry for "action", so we don't need to match the action name.</span></span>

<span data-ttu-id="97f93-243">Em seguida, tentamos corresponder aos nomes de parâmetro para as ações, observar apenas as ações GET.</span><span class="sxs-lookup"><span data-stu-id="97f93-243">Next, we try to match parameter names for the actions, looking only at the GET actions.</span></span>

| <span data-ttu-id="97f93-244">Ação</span><span class="sxs-lookup"><span data-stu-id="97f93-244">Action</span></span> | <span data-ttu-id="97f93-245">Parâmetros de correspondência</span><span class="sxs-lookup"><span data-stu-id="97f93-245">Parameters to Match</span></span> |
| --- | --- |
| `GetAll` | <span data-ttu-id="97f93-246">nenhum</span><span class="sxs-lookup"><span data-stu-id="97f93-246">none</span></span> |
| `GetById` | <span data-ttu-id="97f93-247">"id"</span><span class="sxs-lookup"><span data-stu-id="97f93-247">"id"</span></span> |
| `FindProductsByName` | <span data-ttu-id="97f93-248">"name"</span><span class="sxs-lookup"><span data-stu-id="97f93-248">"name"</span></span> |

<span data-ttu-id="97f93-249">Observe que o *versão* parâmetro de `GetById` não é considerado, porque ele é um parâmetro opcional.</span><span class="sxs-lookup"><span data-stu-id="97f93-249">Notice that the *version* parameter of `GetById` is not considered, because it is an optional parameter.</span></span>

<span data-ttu-id="97f93-250">O `GetAll` método corresponde aos superficialmente.</span><span class="sxs-lookup"><span data-stu-id="97f93-250">The `GetAll` method matches trivially.</span></span> <span data-ttu-id="97f93-251">O `GetById` método também corresponde, pois o dicionário de rota contém "id".</span><span class="sxs-lookup"><span data-stu-id="97f93-251">The `GetById` method also matches, because the route dictionary contains "id".</span></span> <span data-ttu-id="97f93-252">O `FindProductsByName` não corresponde ao método.</span><span class="sxs-lookup"><span data-stu-id="97f93-252">The `FindProductsByName` method does not match.</span></span>

<span data-ttu-id="97f93-253">O `GetById` método wins, como ele corresponde a um parâmetro, versus sem parâmetros para `GetAll`.</span><span class="sxs-lookup"><span data-stu-id="97f93-253">The `GetById` method wins, because it matches one parameter, versus no parameters for `GetAll`.</span></span> <span data-ttu-id="97f93-254">O método é invocado com os seguintes valores de parâmetro:</span><span class="sxs-lookup"><span data-stu-id="97f93-254">The method is invoked with the following parameter values:</span></span>

- <span data-ttu-id="97f93-255">*id* = 1</span><span class="sxs-lookup"><span data-stu-id="97f93-255">*id* = 1</span></span>
- <span data-ttu-id="97f93-256">*versão* = 1,5</span><span class="sxs-lookup"><span data-stu-id="97f93-256">*version* = 1.5</span></span>

<span data-ttu-id="97f93-257">Observe que, embora *versão* não foi usado no algoritmo de seleção, o valor do parâmetro vem da cadeia de caracteres de consulta do URI.</span><span class="sxs-lookup"><span data-stu-id="97f93-257">Notice that even though *version* was not used in the selection algorithm, the value of the parameter comes from the URI query string.</span></span>

## <a name="extension-points"></a><span data-ttu-id="97f93-258">Pontos de extensão</span><span class="sxs-lookup"><span data-stu-id="97f93-258">Extension Points</span></span>

<span data-ttu-id="97f93-259">API da Web fornece pontos de extensão para algumas partes do processo de roteamento.</span><span class="sxs-lookup"><span data-stu-id="97f93-259">Web API provides extension points for some parts of the routing process.</span></span>

| <span data-ttu-id="97f93-260">Interface</span><span class="sxs-lookup"><span data-stu-id="97f93-260">Interface</span></span> | <span data-ttu-id="97f93-261">Descrição</span><span class="sxs-lookup"><span data-stu-id="97f93-261">Description</span></span> |
| --- | --- |
| <span data-ttu-id="97f93-262">**IHttpControllerSelector**</span><span class="sxs-lookup"><span data-stu-id="97f93-262">**IHttpControllerSelector**</span></span> | <span data-ttu-id="97f93-263">Seleciona o controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-263">Selects the controller.</span></span> |
| <span data-ttu-id="97f93-264">**IHttpControllerTypeResolver**</span><span class="sxs-lookup"><span data-stu-id="97f93-264">**IHttpControllerTypeResolver**</span></span> | <span data-ttu-id="97f93-265">Obtém a lista de tipos de controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-265">Gets the list of controller types.</span></span> <span data-ttu-id="97f93-266">O **DefaultHttpControllerSelector** escolhe o tipo de controlador dessa lista.</span><span class="sxs-lookup"><span data-stu-id="97f93-266">The **DefaultHttpControllerSelector** chooses the controller type from this list.</span></span> |
| <span data-ttu-id="97f93-267">**IAssembliesResolver**</span><span class="sxs-lookup"><span data-stu-id="97f93-267">**IAssembliesResolver**</span></span> | <span data-ttu-id="97f93-268">Obtém a lista de assemblies de projeto.</span><span class="sxs-lookup"><span data-stu-id="97f93-268">Gets the list of project assemblies.</span></span> <span data-ttu-id="97f93-269">O **IHttpControllerTypeResolver** interface usa essa lista para localizar os tipos de controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-269">The **IHttpControllerTypeResolver** interface uses this list to find the controller types.</span></span> |
| <span data-ttu-id="97f93-270">**IHttpControllerActivator**</span><span class="sxs-lookup"><span data-stu-id="97f93-270">**IHttpControllerActivator**</span></span> | <span data-ttu-id="97f93-271">Cria novas instâncias de controlador.</span><span class="sxs-lookup"><span data-stu-id="97f93-271">Creates new controller instances.</span></span> |
| <span data-ttu-id="97f93-272">**IHttpActionSelector**</span><span class="sxs-lookup"><span data-stu-id="97f93-272">**IHttpActionSelector**</span></span> | <span data-ttu-id="97f93-273">Seleciona a ação.</span><span class="sxs-lookup"><span data-stu-id="97f93-273">Selects the action.</span></span> |
| <span data-ttu-id="97f93-274">**IHttpActionInvoker**</span><span class="sxs-lookup"><span data-stu-id="97f93-274">**IHttpActionInvoker**</span></span> | <span data-ttu-id="97f93-275">Invoca a ação.</span><span class="sxs-lookup"><span data-stu-id="97f93-275">Invokes the action.</span></span> |

<span data-ttu-id="97f93-276">Para fornecer sua própria implementação de qualquer uma dessas interfaces, use o **serviços** coleta sobre o **HttpConfiguration** objeto:</span><span class="sxs-lookup"><span data-stu-id="97f93-276">To provide your own implementation for any of these interfaces, use the **Services** collection on the **HttpConfiguration** object:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample11.cs)]
