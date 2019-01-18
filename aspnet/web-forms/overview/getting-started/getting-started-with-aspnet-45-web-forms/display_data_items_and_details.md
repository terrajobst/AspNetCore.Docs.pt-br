---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: Itens de dados de exibição e fornece detalhes sobre | Microsoft Docs
author: Erikre
description: Esta série de tutoriais mostrará as Noções básicas de criação de um aplicativo Web Forms do ASP.NET com o ASP.NET 4.7 e o Microsoft Visual Studio 2017
ms.author: riande
ms.date: 1/04/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: acc2f8e78375ef0455d467e2af750ecbee623224
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396214"
---
<a name="display-data-items-and-details"></a><span data-ttu-id="177f9-103">Itens de dados de exibição e detalhes</span><span class="sxs-lookup"><span data-stu-id="177f9-103">Display data items and details</span></span>
====================
<span data-ttu-id="177f9-104">by [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="177f9-104">by [Erik Reitan](https://github.com/Erikre)</span></span>

> <span data-ttu-id="177f9-105">Esta série de tutoriais ensina os conceitos básicos da criação de um aplicativo Web Forms do ASP.NET com o ASP.NET 4.7 e o Microsoft Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="177f9-105">This tutorial series teaches you the basics of building an ASP.NET Web Forms application with ASP.NET 4.7 and Microsoft Visual Studio 2017.</span></span>

<span data-ttu-id="177f9-106">Neste tutorial, você aprenderá como exibir itens de dados e os detalhes do item de dados com o Web Forms do ASP.NET e o Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="177f9-106">In this tutorial, you'll learn how to display data items and data item details with ASP.NET Web Forms and Entity Framework Code First.</span></span> <span data-ttu-id="177f9-107">Este tutorial se baseia no tutorial anterior de "Interface do usuário e navegação" como parte da série de tutoriais de Wingtip Toys Store.</span><span class="sxs-lookup"><span data-stu-id="177f9-107">This tutorial builds on the previous "UI and Navigation" tutorial as part of the Wingtip Toy Store tutorial series.</span></span> <span data-ttu-id="177f9-108">Depois de concluir este tutorial, você verá os produtos na *ProductsList.aspx* página e detalhes do produto sobre o *ProductDetails.aspx* página.</span><span class="sxs-lookup"><span data-stu-id="177f9-108">After completing this tutorial, you'll see products on the *ProductsList.aspx* page and a product's details on the *ProductDetails.aspx* page.</span></span>

## <a name="youll-learn-how-to"></a><span data-ttu-id="177f9-109">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="177f9-109">You'll learn how to:</span></span>

- <span data-ttu-id="177f9-110">Adicionar um controle de dados para exibir produtos do banco de dados</span><span class="sxs-lookup"><span data-stu-id="177f9-110">Add a data control to display products from the database</span></span>
- <span data-ttu-id="177f9-111">Conectar um controle de dados para os dados selecionados</span><span class="sxs-lookup"><span data-stu-id="177f9-111">Connect a data control to the selected data</span></span>
- <span data-ttu-id="177f9-112">Adicionar um controle de dados para exibir detalhes do produto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="177f9-112">Add a data control to display product details from the database</span></span>
- <span data-ttu-id="177f9-113">Recupere um valor de cadeia de caracteres de consulta e use esse valor para limitar os dados recuperados do banco de dados</span><span class="sxs-lookup"><span data-stu-id="177f9-113">Retrieve a value from the query string and use that value to limit the data that's retrieved from the database</span></span>

### <a name="features-introduced-in-this-tutorial"></a><span data-ttu-id="177f9-114">Recursos apresentados neste tutorial:</span><span class="sxs-lookup"><span data-stu-id="177f9-114">Features introduced in this tutorial:</span></span>

- <span data-ttu-id="177f9-115">Model binding</span><span class="sxs-lookup"><span data-stu-id="177f9-115">Model binding</span></span>
- <span data-ttu-id="177f9-116">Provedores de valor</span><span class="sxs-lookup"><span data-stu-id="177f9-116">Value providers</span></span>

## <a name="add-a-data-control"></a><span data-ttu-id="177f9-117">Adicionar um controle de dados</span><span class="sxs-lookup"><span data-stu-id="177f9-117">Add a data control</span></span>

<span data-ttu-id="177f9-118">Você pode usar algumas opções diferentes para associar dados a um controle de servidor.</span><span class="sxs-lookup"><span data-stu-id="177f9-118">You can use a few different options to bind data to a server control.</span></span> <span data-ttu-id="177f9-119">As mais comuns incluem:</span><span class="sxs-lookup"><span data-stu-id="177f9-119">The most common include:</span></span>

 * <span data-ttu-id="177f9-120">Adicionando um controle de fonte de dados</span><span class="sxs-lookup"><span data-stu-id="177f9-120">Adding a data source control</span></span>
 * <span data-ttu-id="177f9-121">Adicionando o código manualmente</span><span class="sxs-lookup"><span data-stu-id="177f9-121">Adding code by hand</span></span>
 * <span data-ttu-id="177f9-122">Usando a associação de modelos</span><span class="sxs-lookup"><span data-stu-id="177f9-122">Using model binding</span></span>

### <a name="use-a-data-source-control-to-bind-data"></a><span data-ttu-id="177f9-123">Usar um controle de fonte de dados para associar dados</span><span class="sxs-lookup"><span data-stu-id="177f9-123">Use a data source control to bind data</span></span>

<span data-ttu-id="177f9-124">Adicionar um controle de fonte de dados permite que você vincule o controle de fonte de dados para o controle que exibe os dados.</span><span class="sxs-lookup"><span data-stu-id="177f9-124">Adding a data source control allows you to link the data source control to the control that displays the data.</span></span> <span data-ttu-id="177f9-125">Com essa abordagem, você pode declarativamente, em vez de programaticamente, conectar controles de servidor para fontes de dados.</span><span class="sxs-lookup"><span data-stu-id="177f9-125">With this approach, you can declaratively,  rather than programmatically, connect server-side controls to data sources.</span></span>

### <a name="code-by-hand-to-bind-data"></a><span data-ttu-id="177f9-126">Código manualmente para associar dados</span><span class="sxs-lookup"><span data-stu-id="177f9-126">Code by hand to bind data</span></span>

<span data-ttu-id="177f9-127">Codificação manual envolve:</span><span class="sxs-lookup"><span data-stu-id="177f9-127">Coding by hand involves:</span></span>

1. <span data-ttu-id="177f9-128">Ler um valor</span><span class="sxs-lookup"><span data-stu-id="177f9-128">Reading a value</span></span>
2. <span data-ttu-id="177f9-129">Verificar se ele é nulo</span><span class="sxs-lookup"><span data-stu-id="177f9-129">Checking if it's null</span></span>
3. <span data-ttu-id="177f9-130">Convertendo-o em um tipo apropriado</span><span class="sxs-lookup"><span data-stu-id="177f9-130">Converting it to an appropriate type</span></span>
4. <span data-ttu-id="177f9-131">Verificando o sucesso de conversão</span><span class="sxs-lookup"><span data-stu-id="177f9-131">Checking conversion success</span></span>
5. <span data-ttu-id="177f9-132">Usando o valor na consulta</span><span class="sxs-lookup"><span data-stu-id="177f9-132">Using the value in the query</span></span> 

<span data-ttu-id="177f9-133">Essa abordagem permite que você tem controle total sobre a lógica de acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="177f9-133">This approach lets you have full control over your data-access logic.</span></span>

### <a name="use-model-binding-to-bind-data"></a><span data-ttu-id="177f9-134">Usar associação de modelo para associar dados</span><span class="sxs-lookup"><span data-stu-id="177f9-134">Use model binding to bind data</span></span>

<span data-ttu-id="177f9-135">Associação de modelos permite que você associe os resultados com muito menos código e lhe dá a capacidade de reutilizar a funcionalidade em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="177f9-135">Model binding lets you bind results with far less code and gives you the ability to reuse the functionality throughout your application.</span></span> <span data-ttu-id="177f9-136">Ele simplifica o trabalho com a lógica de acesso a dados e focada no código enquanto ainda fornecem uma estrutura de vinculação de dados avançada.</span><span class="sxs-lookup"><span data-stu-id="177f9-136">It simplifies working with code-focused data-access logic while still providing a rich, data-binding framework.</span></span>

## <a name="display-products"></a><span data-ttu-id="177f9-137">Exibir produtos</span><span class="sxs-lookup"><span data-stu-id="177f9-137">Display products</span></span>

<span data-ttu-id="177f9-138">Neste tutorial, você usará a associação de modelo para associar dados.</span><span class="sxs-lookup"><span data-stu-id="177f9-138">In this tutorial, you'll use model binding to bind data.</span></span> <span data-ttu-id="177f9-139">Para configurar um controle de dados para usar a associação de modelo para selecionar dados, defina o controle `SelectMethod` propriedade para um nome de método no código da página.</span><span class="sxs-lookup"><span data-stu-id="177f9-139">To configure a data control to use model binding to select data, you set the control's `SelectMethod` property to a method name in the page's code.</span></span> <span data-ttu-id="177f9-140">O controle de dados chama o método no momento apropriado no ciclo de vida da página e associa automaticamente os dados retornados.</span><span class="sxs-lookup"><span data-stu-id="177f9-140">The data control calls the method at the appropriate time in the page life cycle and automatically binds the returned data.</span></span> <span data-ttu-id="177f9-141">Não é necessário chamar explicitamente o `DataBind` método.</span><span class="sxs-lookup"><span data-stu-id="177f9-141">There's no need to explicitly call the `DataBind` method.</span></span>

1. <span data-ttu-id="177f9-142">Na **Gerenciador de soluções**, abra *ProductList. aspx*.</span><span class="sxs-lookup"><span data-stu-id="177f9-142">In **Solution Explorer**, open *ProductList.aspx*.</span></span>
2. <span data-ttu-id="177f9-143">Substitua a marcação existente com essa marcação:</span><span class="sxs-lookup"><span data-stu-id="177f9-143">Replace the existing markup with this markup:</span></span>   

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

<span data-ttu-id="177f9-144">Esse código usa um **ListView** controle chamado `productList` para exibir produtos.</span><span class="sxs-lookup"><span data-stu-id="177f9-144">This code uses a **ListView** control named `productList` to display products.</span></span>

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

<span data-ttu-id="177f9-145">Com modelos e estilos, você define como o **ListView** controle exibe dados.</span><span class="sxs-lookup"><span data-stu-id="177f9-145">With templates and styles, you define how the **ListView** control displays data.</span></span> <span data-ttu-id="177f9-146">Ele é útil para dados em qualquer estrutura de repetição.</span><span class="sxs-lookup"><span data-stu-id="177f9-146">It's useful for data in any repeating structure.</span></span> <span data-ttu-id="177f9-147">Embora isso **ListView** exemplo simplesmente exibe dados de banco de dados, você pode também, sem código, permitir que os usuários para editar, inserir e excluir dados e para classificar e dados da página.</span><span class="sxs-lookup"><span data-stu-id="177f9-147">Though this **ListView** example simply displays database data, you can also, without code, enable users to edit, insert, and delete data, and to sort and page data.</span></span>

<span data-ttu-id="177f9-148">Definindo o `ItemType` propriedade em de **ListView** controlar, a expressão de associação de dados `Item` está disponível e o controle torna-se com rigidez de tipos.</span><span class="sxs-lookup"><span data-stu-id="177f9-148">By setting the `ItemType` property in the **ListView** control, the data-binding expression `Item` is available and the control becomes strongly typed.</span></span> <span data-ttu-id="177f9-149">Conforme mencionado no tutorial anterior, você pode selecionar os detalhes do objeto de Item com o IntelliSense, como especificar o `ProductName`:</span><span class="sxs-lookup"><span data-stu-id="177f9-149">As mentioned in the previous tutorial, you can select Item object details with IntelliSense, such as specifying the `ProductName`:</span></span>

![Exibir dados de itens e detalhes - IntelliSense](display_data_items_and_details/_static/image1.png)

<span data-ttu-id="177f9-151">Você também estiver usando a associação de modelo para especificar um `SelectMethod` valor.</span><span class="sxs-lookup"><span data-stu-id="177f9-151">You're also using model binding to specify a `SelectMethod` value.</span></span> <span data-ttu-id="177f9-152">Esse valor (`GetProducts`) corresponde ao método que você adicionará ao código de trás para exibir produtos na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="177f9-152">This value (`GetProducts`) corresponds to the method you'll add to the code behind to display products in the next step.</span></span>

### <a name="add-code-to-display-products"></a><span data-ttu-id="177f9-153">Adicione código para exibir produtos</span><span class="sxs-lookup"><span data-stu-id="177f9-153">Add code to display products</span></span>

<span data-ttu-id="177f9-154">Nesta etapa, você adicionará código para preencher a **ListView** controle com os dados de produto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="177f9-154">In this step, you'll add code to populate the **ListView** control with product data from the database.</span></span> <span data-ttu-id="177f9-155">O código suporta mostrando todos os produtos e categoria individual.</span><span class="sxs-lookup"><span data-stu-id="177f9-155">The code supports showing all products and  individual category products.</span></span>

1. <span data-ttu-id="177f9-156">Na **Gerenciador de soluções**, clique com botão direito *ProductList. aspx* e, em seguida, selecione **Exibir código**.</span><span class="sxs-lookup"><span data-stu-id="177f9-156">In **Solution Explorer**, right-click *ProductList.aspx* and then select **View Code**.</span></span>
2. <span data-ttu-id="177f9-157">Substitua o código existente na *ProductList.aspx.cs* arquivo com este:</span><span class="sxs-lookup"><span data-stu-id="177f9-157">Replace the existing code in the *ProductList.aspx.cs* file with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

<span data-ttu-id="177f9-158">Este código mostra a `GetProducts` método que o **ListView** do controle `ItemType` referências de propriedade no *ProductList. aspx* página.</span><span class="sxs-lookup"><span data-stu-id="177f9-158">This code shows the `GetProducts` method that the **ListView** control's `ItemType` property references in the *ProductList.aspx* page.</span></span> <span data-ttu-id="177f9-159">Para limitar os resultados a uma categoria de banco de dados específico, o código define o `categoryId` valor do valor de cadeia de caracteres de consulta passado para o *ProductList. aspx* página quando o *ProductList. aspx* é de página para onde navegar.</span><span class="sxs-lookup"><span data-stu-id="177f9-159">To limit the results to a specific database category, the code sets the `categoryId` value from the query string value passed to the *ProductList.aspx* page when the *ProductList.aspx* page is navigated to.</span></span> <span data-ttu-id="177f9-160">O `QueryStringAttribute` classe de `System.Web.ModelBinding` namespace é usado para recuperar o valor da variável de cadeia de caracteres de consulta `id`.</span><span class="sxs-lookup"><span data-stu-id="177f9-160">The `QueryStringAttribute` class in the `System.Web.ModelBinding` namespace is used to retrieve the value of the query string variable `id`.</span></span> <span data-ttu-id="177f9-161">Isso instrui a ligação de modelo para tentar associar um valor da cadeia de consulta para o `categoryId` parâmetro em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="177f9-161">This instructs model binding to try to bind a value from the query string to the `categoryId` parameter at run time.</span></span>

<span data-ttu-id="177f9-162">Quando uma categoria válida é passada como uma cadeia de caracteres de consulta para a página, os resultados da consulta são limitados aos produtos no banco de dados que correspondem a `categoryId` valor.</span><span class="sxs-lookup"><span data-stu-id="177f9-162">When a valid category is passed as a query string to the page, the results of the query are limited to those products in the database that match the `categoryId` value.</span></span> <span data-ttu-id="177f9-163">Por exemplo, se o *ProductsList.aspx* URL da página é este:</span><span class="sxs-lookup"><span data-stu-id="177f9-163">For instance, if the *ProductsList.aspx* page URL is this:</span></span>


[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

<span data-ttu-id="177f9-164">A página exibe apenas os produtos em que o `categoryId` é igual a `1`.</span><span class="sxs-lookup"><span data-stu-id="177f9-164">The page displays only the products where the `categoryId` equals `1`.</span></span>

<span data-ttu-id="177f9-165">Todos os produtos serão exibidos se nenhuma cadeia de caracteres de consulta é incluído quando o *ProductList. aspx* página for chamada.</span><span class="sxs-lookup"><span data-stu-id="177f9-165">All products are displayed if no query string is included when the *ProductList.aspx* page is called.</span></span>

<span data-ttu-id="177f9-166">As fontes de valores para esses métodos são chamadas de *provedores de valor* (como *QueryString*), e os atributos de parâmetro que indicam qual provedor de valor a ser usado são denominados *atributos de provedor de valor* (como `id`).</span><span class="sxs-lookup"><span data-stu-id="177f9-166">The sources of values for these methods are referred to as *value providers* (such as *QueryString*), and the parameter attributes that indicate which value provider to use are referred to as *value provider attributes* (such as `id`).</span></span> <span data-ttu-id="177f9-167">O ASP.NET inclui provedores de valor e os atributos correspondentes para todas as fontes típicas da entrada do usuário em um aplicativo Web Forms, como a cadeia de caracteres de consulta, cookies, valores de formulário, controles, estado de exibição, o estado de sessão e as propriedades de perfil.</span><span class="sxs-lookup"><span data-stu-id="177f9-167">ASP.NET includes value providers and corresponding attributes for all of the typical sources of user input in a Web Forms application such as the query string, cookies, form values, controls, view state, session state, and profile properties.</span></span> <span data-ttu-id="177f9-168">Você também pode escrever provedores de valor personalizados.</span><span class="sxs-lookup"><span data-stu-id="177f9-168">You can also write custom value providers.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="177f9-169">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="177f9-169">Run the application</span></span>

<span data-ttu-id="177f9-170">Execute o aplicativo agora para exibir todos os produtos ou produtos de uma categoria.</span><span class="sxs-lookup"><span data-stu-id="177f9-170">Run the application now to view all products or a category's products.</span></span>

1. <span data-ttu-id="177f9-171">Pressione **F5** enquanto estiver no Visual Studio para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="177f9-171">Press **F5** while in Visual Studio to run the application.</span></span>  
   <span data-ttu-id="177f9-172">O navegador é aberta e mostra a *default. aspx* página.</span><span class="sxs-lookup"><span data-stu-id="177f9-172">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="177f9-173">Selecione **carros** no menu de navegação da categoria de produto.</span><span class="sxs-lookup"><span data-stu-id="177f9-173">Select **Cars** from the product category navigation menu.</span></span>  
   <span data-ttu-id="177f9-174">O *ProductList. aspx* página é exibida mostrando apenas **carros** produtos da categoria.</span><span class="sxs-lookup"><span data-stu-id="177f9-174">The *ProductList.aspx* page displays showing only **Cars** category products.</span></span> <span data-ttu-id="177f9-175">Mais tarde neste tutorial, você exibirá detalhes do produto.</span><span class="sxs-lookup"><span data-stu-id="177f9-175">Later in this tutorial, you'll display product details.</span></span>  

    ![Exibir dados de itens e detalhes - carros](display_data_items_and_details/_static/image2.png)

3. <span data-ttu-id="177f9-177">Selecione **produtos** no menu de navegação na parte superior.</span><span class="sxs-lookup"><span data-stu-id="177f9-177">Select **Products** from the navigation menu at the top.</span></span>  
   <span data-ttu-id="177f9-178">Novamente, o *ProductList. aspx* página for exibida, no entanto, desta vez, ele mostra a lista completa de produtos.</span><span class="sxs-lookup"><span data-stu-id="177f9-178">Again, the *ProductList.aspx* page is displayed, however this time it shows the entire list of products.</span></span>   

    ![Exibir dados de itens e detalhes - produtos](display_data_items_and_details/_static/image3.png)

4. <span data-ttu-id="177f9-180">Feche o navegador e retorne ao Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="177f9-180">Close the browser and return to Visual Studio.</span></span>

### <a name="add-a-data-control-to-display-product-details"></a><span data-ttu-id="177f9-181">Adicionar um controle de dados para exibir detalhes do produto</span><span class="sxs-lookup"><span data-stu-id="177f9-181">Add a data control to display product details</span></span>

<span data-ttu-id="177f9-182">Em seguida, você modificará a marcação na *ProductDetails.aspx* página que você adicionou no tutorial anterior para exibir informações de produto específico.</span><span class="sxs-lookup"><span data-stu-id="177f9-182">Next, you'll modify the markup in the *ProductDetails.aspx* page that you added in the previous tutorial to display specific product information.</span></span>

1. <span data-ttu-id="177f9-183">Na **Gerenciador de soluções**, abra *ProductDetails.aspx*.</span><span class="sxs-lookup"><span data-stu-id="177f9-183">In **Solution Explorer**, open *ProductDetails.aspx*.</span></span>

2. <span data-ttu-id="177f9-184">Substitua a marcação existente com essa marcação:</span><span class="sxs-lookup"><span data-stu-id="177f9-184">Replace the existing markup with this markup:</span></span>

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

    <span data-ttu-id="177f9-185">Esse código usa um **FormView** controle para exibir detalhes do produto específico.</span><span class="sxs-lookup"><span data-stu-id="177f9-185">This code uses a **FormView** control to display specific product details.</span></span> <span data-ttu-id="177f9-186">Essa marcação usa métodos, como os métodos usados para exibir dados na *ProductList. aspx* página.</span><span class="sxs-lookup"><span data-stu-id="177f9-186">This markup uses methods like the methods used to display data in the *ProductList.aspx* page.</span></span> <span data-ttu-id="177f9-187">O **FormView** controle é usado para exibir um único registro por vez de uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="177f9-187">The **FormView** control is used to display a single record at a time from a data source.</span></span> <span data-ttu-id="177f9-188">Quando você usa o **FormView** controle, você cria modelos para exibir e editar valores de associação de dados.</span><span class="sxs-lookup"><span data-stu-id="177f9-188">When you use the **FormView** control, you create templates to display and edit data-bound values.</span></span> <span data-ttu-id="177f9-189">Esses modelos contêm controles, expressões de associação, e formatação que definem a aparência e a funcionalidade do formulário.</span><span class="sxs-lookup"><span data-stu-id="177f9-189">These templates contain controls, binding expressions, and formatting that define the form's look and functionality.</span></span>

<span data-ttu-id="177f9-190">Conectar-se a marcação anterior para o banco de dados requer código adicional.</span><span class="sxs-lookup"><span data-stu-id="177f9-190">Connecting the previous markup to the database requires additional code.</span></span>

1. <span data-ttu-id="177f9-191">Na **Gerenciador de soluções**, clique com botão direito *ProductDetails.aspx* e, em seguida, clique em **Exibir código**.</span><span class="sxs-lookup"><span data-stu-id="177f9-191">In **Solution Explorer**, right-click *ProductDetails.aspx* and then click **View Code**.</span></span>  
   <span data-ttu-id="177f9-192">O *ProductDetails.aspx.cs* arquivo é exibido.</span><span class="sxs-lookup"><span data-stu-id="177f9-192">The *ProductDetails.aspx.cs* file is displayed.</span></span>

2. <span data-ttu-id="177f9-193">Substitua o código existente com este código:</span><span class="sxs-lookup"><span data-stu-id="177f9-193">Replace the existing code with this code:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

<span data-ttu-id="177f9-194">Esse código verifica se há um "`productID`" valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="177f9-194">This code checks for a "`productID`" query-string value.</span></span> <span data-ttu-id="177f9-195">Se um valor de cadeia de consulta válido for encontrado, o produto correspondente é exibido.</span><span class="sxs-lookup"><span data-stu-id="177f9-195">If a valid query-string value is found, the matching product is displayed.</span></span> <span data-ttu-id="177f9-196">Se a cadeia de consulta não for encontrada, ou seu valor não é válido, nenhum produto será exibido.</span><span class="sxs-lookup"><span data-stu-id="177f9-196">If the query-string isn't found, or its value isn't valid, no product is displayed.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="177f9-197">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="177f9-197">Run the application</span></span>

<span data-ttu-id="177f9-198">Agora você pode executar o aplicativo para ver um produto individual exibido com base na ID de produto.</span><span class="sxs-lookup"><span data-stu-id="177f9-198">Now you can run the application to see an individual product displayed based on product ID.</span></span>

1. <span data-ttu-id="177f9-199">Pressione **F5** enquanto estiver no Visual Studio para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="177f9-199">Press **F5** while in Visual Studio to run the application.</span></span>  
   <span data-ttu-id="177f9-200">O navegador é aberta e mostra a *default. aspx* página.</span><span class="sxs-lookup"><span data-stu-id="177f9-200">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="177f9-201">Selecione **barcos** no menu de navegação de categoria.</span><span class="sxs-lookup"><span data-stu-id="177f9-201">Select **Boats** from the category navigation menu.</span></span>  
   <span data-ttu-id="177f9-202">O *ProductList. aspx* página é exibida.</span><span class="sxs-lookup"><span data-stu-id="177f9-202">The *ProductList.aspx* page is displayed.</span></span>

3. <span data-ttu-id="177f9-203">Selecione **Paper barco** na lista de produtos.</span><span class="sxs-lookup"><span data-stu-id="177f9-203">Select **Paper Boat** from the product list.</span></span>
   <span data-ttu-id="177f9-204">O *ProductDetails.aspx* página é exibida.</span><span class="sxs-lookup"><span data-stu-id="177f9-204">The *ProductDetails.aspx* page is displayed.</span></span>

    ![Exibir dados de itens e detalhes - produtos](display_data_items_and_details/_static/image4.png)
    
4. <span data-ttu-id="177f9-206">Feche o navegador.</span><span class="sxs-lookup"><span data-stu-id="177f9-206">Close the browser.</span></span>


## <a name="additional-resources"></a><span data-ttu-id="177f9-207">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="177f9-207">Additional resources</span></span>

[<span data-ttu-id="177f9-208">Recuperando e exibindo dados com a associação de modelos e formulários da web</span><span class="sxs-lookup"><span data-stu-id="177f9-208">Retrieving and displaying data with model binding and web forms</span></span>](../../presenting-and-managing-data/model-binding/retrieving-data.md)

## <a name="next-steps"></a><span data-ttu-id="177f9-209">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="177f9-209">Next steps</span></span>

<span data-ttu-id="177f9-210">Neste tutorial, você adicionou marcação e código para exibir detalhes do produto e produtos.</span><span class="sxs-lookup"><span data-stu-id="177f9-210">In this tutorial, you added markup and code to display products and product details.</span></span> <span data-ttu-id="177f9-211">Você aprendeu sobre controles de dados fortemente tipados, associação de modelos e provedores de valor.</span><span class="sxs-lookup"><span data-stu-id="177f9-211">You learned about strongly typed data controls, model binding, and value providers.</span></span> <span data-ttu-id="177f9-212">O próximo tutorial, você adicionará um carrinho de compras para o aplicativo de exemplo Wingtip Toys.</span><span class="sxs-lookup"><span data-stu-id="177f9-212">In the next tutorial, you'll add a shopping cart to the Wingtip Toys sample application.</span></span> 

> [!div class="step-by-step"]
> <span data-ttu-id="177f9-213">[Anterior](ui_and_navigation.md)
> [Próximo](shopping-cart.md)</span><span class="sxs-lookup"><span data-stu-id="177f9-213">[Previous](ui_and_navigation.md)
[Next](shopping-cart.md)</span></span>
