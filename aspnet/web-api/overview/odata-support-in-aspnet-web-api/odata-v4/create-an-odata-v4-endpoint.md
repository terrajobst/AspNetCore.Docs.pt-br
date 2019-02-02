---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
title: Criar um ponto de extremidade OData v4 usando a API Web ASP.NET 2.2 | Microsoft Docs
author: MikeWasson
description: O Open Data Protocol (OData) é um protocolo de acesso de dados para a web. O OData fornece uma maneira uniforme para consultar e manipular os conjuntos de dados por meio de operações de CRUD...
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 1e1927c0-ded1-4752-80fd-a146628d2f09
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
msc.type: authoredcontent
ms.openlocfilehash: c6a4aa4eb563fd77d5afd9248175d5f5b7984d19
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667564"
---
# <a name="create-an-odata-v4-endpoint-using-aspnet-web-api"></a><span data-ttu-id="cc138-104">Criar um ponto de extremidade OData v4 usando a API Web ASP.NET</span><span class="sxs-lookup"><span data-stu-id="cc138-104">Create an OData v4 Endpoint Using ASP.NET Web API</span></span> 

> <span data-ttu-id="cc138-105">O Open Data Protocol (OData) é um protocolo de acesso de dados para a web.</span><span class="sxs-lookup"><span data-stu-id="cc138-105">The Open Data Protocol (OData) is a data access protocol for the web.</span></span> <span data-ttu-id="cc138-106">O OData fornece uma maneira uniforme para consultar e manipular os conjuntos de dados por meio de operações de CRUD (criar, ler, atualizar e excluir).</span><span class="sxs-lookup"><span data-stu-id="cc138-106">OData provides a uniform way to query and manipulate data sets through CRUD operations (create, read, update, and delete).</span></span>
>
> <span data-ttu-id="cc138-107">API Web ASP.NET oferece suporte a v3 e v4 do protocolo.</span><span class="sxs-lookup"><span data-stu-id="cc138-107">ASP.NET Web API supports both v3 and v4 of the protocol.</span></span> <span data-ttu-id="cc138-108">Você pode até ter um ponto de extremidade de v4 que é executado lado a lado com um ponto de extremidade v3.</span><span class="sxs-lookup"><span data-stu-id="cc138-108">You can even have a v4 endpoint that runs side-by-side with a v3 endpoint.</span></span>
>
> <span data-ttu-id="cc138-109">Este tutorial mostra como criar um ponto de extremidade de v4 do OData que suporta operações CRUD.</span><span class="sxs-lookup"><span data-stu-id="cc138-109">This tutorial shows how to create an OData v4 endpoint that supports CRUD operations.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="cc138-110">Versões de software usadas no tutorial</span><span class="sxs-lookup"><span data-stu-id="cc138-110">Software versions used in the tutorial</span></span>
>
> - <span data-ttu-id="cc138-111">5.2 da API da Web</span><span class="sxs-lookup"><span data-stu-id="cc138-111">Web API 5.2</span></span>
> - <span data-ttu-id="cc138-112">OData v4</span><span class="sxs-lookup"><span data-stu-id="cc138-112">OData v4</span></span>
> - <span data-ttu-id="cc138-113">Visual Studio 2017 (Baixe o Visual Studio 2017 [aqui](https://visualstudio.microsoft.com/downloads/))</span><span class="sxs-lookup"><span data-stu-id="cc138-113">Visual Studio 2017 (download Visual Studio 2017 [here](https://visualstudio.microsoft.com/downloads/))</span></span>
> - <span data-ttu-id="cc138-114">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="cc138-114">Entity Framework 6</span></span>
> - <span data-ttu-id="cc138-115">.NET 4.7.2</span><span class="sxs-lookup"><span data-stu-id="cc138-115">.NET 4.7.2</span></span>
>
> ## <a name="tutorial-versions"></a><span data-ttu-id="cc138-116">Versões de tutoriais</span><span class="sxs-lookup"><span data-stu-id="cc138-116">Tutorial versions</span></span>
>
> <span data-ttu-id="cc138-117">Para o OData versão 3, consulte [criando um ponto de extremidade OData v3](../odata-v3/creating-an-odata-endpoint.md).</span><span class="sxs-lookup"><span data-stu-id="cc138-117">For the OData Version 3, see [Creating an OData v3 Endpoint](../odata-v3/creating-an-odata-endpoint.md).</span></span>

## <a name="create-the-visual-studio-project"></a><span data-ttu-id="cc138-118">Criar o projeto do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc138-118">Create the Visual Studio Project</span></span>

<span data-ttu-id="cc138-119">No Visual Studio, do **arquivo** menu, selecione **New** &gt; **projeto**.</span><span class="sxs-lookup"><span data-stu-id="cc138-119">In Visual Studio, from the **File** menu, select **New** &gt; **Project**.</span></span>

<span data-ttu-id="cc138-120">Expandir **Installed** &gt; **Visual C#**  &gt; **Web**e selecione o **aplicativo Web ASP.NET (.NET Framework)**  modelo.</span><span class="sxs-lookup"><span data-stu-id="cc138-120">Expand **Installed** &gt; **Visual C#** &gt; **Web**, and select the **ASP.NET Web Application (.NET Framework)** template.</span></span> <span data-ttu-id="cc138-121">Nomeie o projeto &quot;ProductService&quot;.</span><span class="sxs-lookup"><span data-stu-id="cc138-121">Name the project &quot;ProductService&quot;.</span></span>

[![](create-an-odata-v4-endpoint/_static/image7.png)](create-an-odata-v4-endpoint/_static/image7.png)

<span data-ttu-id="cc138-122">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="cc138-122">Select **OK**.</span></span>



[![](create-an-odata-v4-endpoint/_static/image8.png)](create-an-odata-v4-endpoint/_static/image8.png)

<span data-ttu-id="cc138-123">Selecione o **vazio** modelo.</span><span class="sxs-lookup"><span data-stu-id="cc138-123">Select the **Empty** template.</span></span> <span data-ttu-id="cc138-124">Sob **adicionar pastas e os principais referências para:**, selecione **API da Web**.</span><span class="sxs-lookup"><span data-stu-id="cc138-124">Under **Add folders and core references for:**, select **Web API**.</span></span> <span data-ttu-id="cc138-125">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="cc138-125">Select **OK**.</span></span>

## <a name="install-the-odata-packages"></a><span data-ttu-id="cc138-126">Instalar os pacotes de OData</span><span class="sxs-lookup"><span data-stu-id="cc138-126">Install the OData packages</span></span>

<span data-ttu-id="cc138-127">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** &gt; **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="cc138-127">From the **Tools** menu, select **NuGet Package Manager** &gt; **Package Manager Console**.</span></span> <span data-ttu-id="cc138-128">Na janela do Console do Gerenciador de pacotes, digite:</span><span class="sxs-lookup"><span data-stu-id="cc138-128">In the Package Manager Console window, type:</span></span>

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample1.cmd)]

<span data-ttu-id="cc138-129">Este comando instala os últimos pacotes NuGet do OData.</span><span class="sxs-lookup"><span data-stu-id="cc138-129">This command installs the latest OData NuGet packages.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="cc138-130">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="cc138-130">Add a model class</span></span>

<span data-ttu-id="cc138-131">Um *modelo* é um objeto que representa uma entidade de dados em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc138-131">A *model* is an object that represents a data entity in your application.</span></span>

<span data-ttu-id="cc138-132">No Gerenciador de Soluções, clique com o botão direito na pasta de modelos (Models).</span><span class="sxs-lookup"><span data-stu-id="cc138-132">In Solution Explorer, right-click the Models folder.</span></span> <span data-ttu-id="cc138-133">No menu de contexto, selecione **Add** &gt; **classe**.</span><span class="sxs-lookup"><span data-stu-id="cc138-133">From the context menu, select **Add** &gt; **Class**.</span></span>

[![](create-an-odata-v4-endpoint/_static/image6.png)](create-an-odata-v4-endpoint/_static/image5.png)

> [!NOTE]
> <span data-ttu-id="cc138-134">Por convenção, as classes de modelo são colocadas na pasta modelos, mas você não precisa seguir essa convenção em seus próprios projetos.</span><span class="sxs-lookup"><span data-stu-id="cc138-134">By convention, model classes are placed in the Models folder, but you don't have to follow this convention in your own projects.</span></span>


<span data-ttu-id="cc138-135">Nomeie a classe `Product`.</span><span class="sxs-lookup"><span data-stu-id="cc138-135">Name the class `Product`.</span></span> <span data-ttu-id="cc138-136">No arquivo Product.cs, substitua o código clichê com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="cc138-136">In the Product.cs file, replace the boilerplate code with the following:</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample2.cs)]

<span data-ttu-id="cc138-137">O `Id` propriedade é a chave de entidade.</span><span class="sxs-lookup"><span data-stu-id="cc138-137">The `Id` property is the entity key.</span></span> <span data-ttu-id="cc138-138">Os clientes podem consultar entidades por chave.</span><span class="sxs-lookup"><span data-stu-id="cc138-138">Clients can query entities by key.</span></span> <span data-ttu-id="cc138-139">Por exemplo, para obter o produto com ID 5, o URI é `/Products(5)`.</span><span class="sxs-lookup"><span data-stu-id="cc138-139">For example, to get the product with ID of 5, the URI is `/Products(5)`.</span></span> <span data-ttu-id="cc138-140">O `Id` propriedade também será a chave primária no banco de dados back-end.</span><span class="sxs-lookup"><span data-stu-id="cc138-140">The `Id` property will also be the primary key in the back-end database.</span></span>

## <a name="enable-entity-framework"></a><span data-ttu-id="cc138-141">Habilitar o Entity Framework</span><span class="sxs-lookup"><span data-stu-id="cc138-141">Enable Entity Framework</span></span>

<span data-ttu-id="cc138-142">Para este tutorial, vamos usar Code First do Entity Framework (EF) para criar o banco de dados de back-end.</span><span class="sxs-lookup"><span data-stu-id="cc138-142">For this tutorial, we'll use Entity Framework (EF) Code First to create the back-end database.</span></span>

> [!NOTE]
> <span data-ttu-id="cc138-143">Web API OData não exige que o EF.</span><span class="sxs-lookup"><span data-stu-id="cc138-143">Web API OData does not require EF.</span></span> <span data-ttu-id="cc138-144">Use qualquer camada de acesso a dados que pode ser traduzidos a entidades de banco de dados em modelos.</span><span class="sxs-lookup"><span data-stu-id="cc138-144">Use any data-access layer that can translate database entities into models.</span></span>


<span data-ttu-id="cc138-145">Primeiro, instale o pacote do NuGet para o EF.</span><span class="sxs-lookup"><span data-stu-id="cc138-145">First, install the NuGet package for EF.</span></span> <span data-ttu-id="cc138-146">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** &gt; **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="cc138-146">From the **Tools** menu, select **NuGet Package Manager** &gt; **Package Manager Console**.</span></span> <span data-ttu-id="cc138-147">Na janela do Console do Gerenciador de pacotes, digite:</span><span class="sxs-lookup"><span data-stu-id="cc138-147">In the Package Manager Console window, type:</span></span>

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample3.cmd)]

<span data-ttu-id="cc138-148">Abra o arquivo Web. config e adicione a seguinte seção dentro de **configuração** elemento após o **configSections** elemento.</span><span class="sxs-lookup"><span data-stu-id="cc138-148">Open the Web.config file, and add the following section inside the **configuration** element, after the **configSections** element.</span></span>

[!code-xml[Main](create-an-odata-v4-endpoint/samples/sample4.xml?highlight=6)]

<span data-ttu-id="cc138-149">Essa configuração adiciona uma cadeia de caracteres de conexão para um banco de dados LocalDB.</span><span class="sxs-lookup"><span data-stu-id="cc138-149">This setting adds a connection string for a LocalDB database.</span></span> <span data-ttu-id="cc138-150">Este banco de dados será usado quando você executa o aplicativo localmente.</span><span class="sxs-lookup"><span data-stu-id="cc138-150">This database will be used when you run the app locally.</span></span>

<span data-ttu-id="cc138-151">Em seguida, adicione uma classe chamada `ProductsContext` na pasta modelos:</span><span class="sxs-lookup"><span data-stu-id="cc138-151">Next, add a class named `ProductsContext` to the Models folder:</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample5.cs)]

<span data-ttu-id="cc138-152">No construtor, `"name=ProductsContext"` fornece o nome da cadeia de caracteres de conexão.</span><span class="sxs-lookup"><span data-stu-id="cc138-152">In the constructor, `"name=ProductsContext"` gives the name of the connection string.</span></span>

## <a name="configure-the-odata-endpoint"></a><span data-ttu-id="cc138-153">Configurar o ponto de extremidade OData</span><span class="sxs-lookup"><span data-stu-id="cc138-153">Configure the OData endpoint</span></span>

<span data-ttu-id="cc138-154">Abra o arquivo de aplicativo\_Start/WebApiConfig.cs.</span><span class="sxs-lookup"><span data-stu-id="cc138-154">Open the file App\_Start/WebApiConfig.cs.</span></span> <span data-ttu-id="cc138-155">Adicione o seguinte **usando** instruções:</span><span class="sxs-lookup"><span data-stu-id="cc138-155">Add the following **using** statements:</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample6.cs)]

<span data-ttu-id="cc138-156">Em seguida, adicione o seguinte código para o **registrar** método:</span><span class="sxs-lookup"><span data-stu-id="cc138-156">Then add the following code to the **Register** method:</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample7.cs)]

<span data-ttu-id="cc138-157">Esse código faz duas coisas:</span><span class="sxs-lookup"><span data-stu-id="cc138-157">This code does two things:</span></span>

- <span data-ttu-id="cc138-158">Cria um modelo de dados de entidade (EDM).</span><span class="sxs-lookup"><span data-stu-id="cc138-158">Creates an Entity Data Model (EDM).</span></span>
- <span data-ttu-id="cc138-159">Adiciona uma rota.</span><span class="sxs-lookup"><span data-stu-id="cc138-159">Adds a route.</span></span>

<span data-ttu-id="cc138-160">Um EDM é um modelo abstrato dos dados.</span><span class="sxs-lookup"><span data-stu-id="cc138-160">An EDM is an abstract model of the data.</span></span> <span data-ttu-id="cc138-161">O EDM é usado para criar o documento de metadados de serviço.</span><span class="sxs-lookup"><span data-stu-id="cc138-161">The EDM is used to create the service metadata document.</span></span> <span data-ttu-id="cc138-162">O **ODataConventionModelBuilder** classe cria um EDM usando as convenções de nomenclatura padrão.</span><span class="sxs-lookup"><span data-stu-id="cc138-162">The **ODataConventionModelBuilder** class creates an EDM by using default naming conventions.</span></span> <span data-ttu-id="cc138-163">Essa abordagem requer o mínimo de código.</span><span class="sxs-lookup"><span data-stu-id="cc138-163">This approach requires the least code.</span></span> <span data-ttu-id="cc138-164">Se você quiser mais controle sobre o EDM, você pode usar o **ODataModelBuilder** classe para criar o EDM com a adição de propriedades, chaves e propriedades de navegação explicitamente.</span><span class="sxs-lookup"><span data-stu-id="cc138-164">If you want more control over the EDM, you can use the **ODataModelBuilder** class to create the EDM by adding properties, keys, and navigation properties explicitly.</span></span>

<span data-ttu-id="cc138-165">Um *rota* informa à API Web como rotear solicitações HTTP para o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="cc138-165">A *route* tells Web API how to route HTTP requests to the endpoint.</span></span> <span data-ttu-id="cc138-166">Para criar uma rota do OData v4, chame o **MapODataServiceRoute** método de extensão.</span><span class="sxs-lookup"><span data-stu-id="cc138-166">To create an OData v4 route, call the **MapODataServiceRoute** extension method.</span></span>

<span data-ttu-id="cc138-167">Se seu aplicativo tiver vários pontos de extremidade OData, crie uma rota separada para cada.</span><span class="sxs-lookup"><span data-stu-id="cc138-167">If your application has multiple OData endpoints, create a separate route for each.</span></span> <span data-ttu-id="cc138-168">Dê a cada rota de um nome de rota exclusivo e um prefixo.</span><span class="sxs-lookup"><span data-stu-id="cc138-168">Give each route a unique route name and prefix.</span></span>

## <a name="add-the-odata-controller"></a><span data-ttu-id="cc138-169">Adicionar o controlador de OData</span><span class="sxs-lookup"><span data-stu-id="cc138-169">Add the OData controller</span></span>

<span data-ttu-id="cc138-170">Um *controlador* é uma classe que manipula as solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc138-170">A *controller* is a class that handles HTTP requests.</span></span> <span data-ttu-id="cc138-171">Você criar um controlador separado para cada conjunto de entidades em seu serviço OData.</span><span class="sxs-lookup"><span data-stu-id="cc138-171">You create a separate controller for each entity set in your OData service.</span></span> <span data-ttu-id="cc138-172">Neste tutorial, você criará um controlador, para o `Product` entidade.</span><span class="sxs-lookup"><span data-stu-id="cc138-172">In this tutorial, you will create one controller, for the `Product` entity.</span></span>

<span data-ttu-id="cc138-173">No Gerenciador de soluções, clique com botão direito na pasta controladores e selecione **Add** &gt; **classe**.</span><span class="sxs-lookup"><span data-stu-id="cc138-173">In Solution Explorer, right-click the Controllers folder and select **Add** &gt; **Class**.</span></span> <span data-ttu-id="cc138-174">Nomeie a classe `ProductsController`.</span><span class="sxs-lookup"><span data-stu-id="cc138-174">Name the class `ProductsController`.</span></span>

> [!NOTE]
> <span data-ttu-id="cc138-175">A versão deste tutorial para OData v3 usa o **Adicionar controlador** scaffolding.</span><span class="sxs-lookup"><span data-stu-id="cc138-175">The version of this tutorial for OData v3 uses the **Add Controller** scaffolding.</span></span> <span data-ttu-id="cc138-176">Atualmente, não há nenhum scaffolding para OData v4.</span><span class="sxs-lookup"><span data-stu-id="cc138-176">Currently, there is no scaffolding for OData v4.</span></span>


<span data-ttu-id="cc138-177">Substitua o código clichê em ProductsController.cs pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="cc138-177">Replace the boilerplate code in ProductsController.cs with the following.</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample8.cs)]

<span data-ttu-id="cc138-178">O controlador usa o `ProductsContext` classe para acessar o banco de dados usando o EF.</span><span class="sxs-lookup"><span data-stu-id="cc138-178">The controller uses the `ProductsContext` class to access the database using EF.</span></span> <span data-ttu-id="cc138-179">Observe que o controlador substitui o **Dispose** método descartar o **ProductsContext**.</span><span class="sxs-lookup"><span data-stu-id="cc138-179">Notice that the controller overrides the **Dispose** method to dispose of the **ProductsContext**.</span></span>

<span data-ttu-id="cc138-180">Isso é o ponto de partida para o controlador.</span><span class="sxs-lookup"><span data-stu-id="cc138-180">This is the starting point for the controller.</span></span> <span data-ttu-id="cc138-181">Em seguida, adicionaremos métodos para todas as operações CRUD.</span><span class="sxs-lookup"><span data-stu-id="cc138-181">Next, we'll add methods for all of the CRUD operations.</span></span>

## <a name="query-the-entity-set"></a><span data-ttu-id="cc138-182">O conjunto de entidades de consulta</span><span class="sxs-lookup"><span data-stu-id="cc138-182">Query the entity set</span></span>

<span data-ttu-id="cc138-183">Adicione os seguintes métodos para `ProductsController`.</span><span class="sxs-lookup"><span data-stu-id="cc138-183">Add the following methods to `ProductsController`.</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample9.cs)]

<span data-ttu-id="cc138-184">A versão sem parâmetros do `Get` método retorna toda a coleção de produtos.</span><span class="sxs-lookup"><span data-stu-id="cc138-184">The parameterless version of the `Get` method returns the entire Products collection.</span></span> <span data-ttu-id="cc138-185">O `Get` método com um *chave* parâmetro procura um produto por sua chave (nesse caso, o `Id` propriedade).</span><span class="sxs-lookup"><span data-stu-id="cc138-185">The `Get` method with a *key* parameter looks up a product by its key (in this case, the `Id` property).</span></span>

<span data-ttu-id="cc138-186">O **[EnableQuery]** atributo permite que os clientes modificar a consulta, usando as opções de consulta como $filter, $sort e $page.</span><span class="sxs-lookup"><span data-stu-id="cc138-186">The **[EnableQuery]** attribute enables clients to modify the query, by using query options such as $filter, $sort, and $page.</span></span> <span data-ttu-id="cc138-187">Para obter mais informações, consulte [que dão suporte a opções de consulta OData](../supporting-odata-query-options.md).</span><span class="sxs-lookup"><span data-stu-id="cc138-187">For more information, see [Supporting OData Query Options](../supporting-odata-query-options.md).</span></span>

## <a name="add-an-entity-to-the-entity-set"></a><span data-ttu-id="cc138-188">Adicionar uma entidade para o conjunto de entidades</span><span class="sxs-lookup"><span data-stu-id="cc138-188">Add an entity to the entity set</span></span>

<span data-ttu-id="cc138-189">Para habilitar clientes para adicionar um novo produto no banco de dados, adicione o seguinte método à `ProductsController`.</span><span class="sxs-lookup"><span data-stu-id="cc138-189">To enable clients to add a new product to the database, add the following method to `ProductsController`.</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample10.cs)]

## <a name="update-an-entity"></a><span data-ttu-id="cc138-190">Atualizar uma entidade</span><span class="sxs-lookup"><span data-stu-id="cc138-190">Update an entity</span></span>

<span data-ttu-id="cc138-191">OData dá suporte a dois semânticas diferentes para atualizar uma entidade, PATCH e PUT.</span><span class="sxs-lookup"><span data-stu-id="cc138-191">OData supports two different semantics for updating an entity, PATCH and PUT.</span></span>

- <span data-ttu-id="cc138-192">PATCH executa uma atualização parcial.</span><span class="sxs-lookup"><span data-stu-id="cc138-192">PATCH performs a partial update.</span></span> <span data-ttu-id="cc138-193">O cliente especifica apenas as propriedades a serem atualizadas.</span><span class="sxs-lookup"><span data-stu-id="cc138-193">The client specifies just the properties to update.</span></span>
- <span data-ttu-id="cc138-194">PUT substitui a entidade inteira.</span><span class="sxs-lookup"><span data-stu-id="cc138-194">PUT replaces the entire entity.</span></span>

<span data-ttu-id="cc138-195">A desvantagem de PUT é que o cliente deve enviar valores para todas as propriedades na entidade, incluindo os valores que não estão mudando.</span><span class="sxs-lookup"><span data-stu-id="cc138-195">The disadvantage of PUT is that the client must send values for all of the properties in the entity, including values that are not changing.</span></span> <span data-ttu-id="cc138-196">O [especificação OData](http://docs.oasis-open.org/odata/odata/v4.0/os/part1-protocol/odata-v4.0-os-part1-protocol.html#_Toc372793719) afirma que o PATCH é preferencial.</span><span class="sxs-lookup"><span data-stu-id="cc138-196">The [OData spec](http://docs.oasis-open.org/odata/odata/v4.0/os/part1-protocol/odata-v4.0-os-part1-protocol.html#_Toc372793719) states that PATCH is preferred.</span></span>

<span data-ttu-id="cc138-197">Em qualquer caso, aqui está o código para métodos PUT e PATCH:</span><span class="sxs-lookup"><span data-stu-id="cc138-197">In any case, here is the code for both PATCH and PUT methods:</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample11.cs)]

<span data-ttu-id="cc138-198">No caso de PATCH, o controlador usa o **Delta&lt;T&gt;**  tipo para controlar as alterações.</span><span class="sxs-lookup"><span data-stu-id="cc138-198">In the case of PATCH, the controller uses the **Delta&lt;T&gt;** type to track the changes.</span></span>

## <a name="delete-an-entity"></a><span data-ttu-id="cc138-199">Excluir uma entidade</span><span class="sxs-lookup"><span data-stu-id="cc138-199">Delete an entity</span></span>

<span data-ttu-id="cc138-200">Para habilitar clientes excluir um produto do banco de dados, adicione o seguinte método à `ProductsController`.</span><span class="sxs-lookup"><span data-stu-id="cc138-200">To enable clients to delete a product from the database, add the following method to `ProductsController`.</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample12.cs)]
