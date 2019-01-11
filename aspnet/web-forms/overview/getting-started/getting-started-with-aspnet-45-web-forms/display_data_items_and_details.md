---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: Itens de dados de exibição e fornece detalhes sobre | Microsoft Docs
author: Erikre
description: Esta série de tutoriais ensinará os conceitos básicos da criação de um aplicativo Web Forms do ASP.NET com o ASP.NET 4.7 e o Microsoft Visual Studio Community 2017 para a Web
ms.author: riande
ms.date: 1/09/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: 73ae1660f5d6e3e28c1c155e745a62936e3502df
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207428"
---
<a name="display-data-items-and-details"></a><span data-ttu-id="e47da-103">Itens de dados de exibição e detalhes</span><span class="sxs-lookup"><span data-stu-id="e47da-103">Display data items and details</span></span>
====================
<span data-ttu-id="e47da-104">por [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="e47da-104">by [Erik Reitan](https://github.com/Erikre)</span></span>

> <span data-ttu-id="e47da-105">Esta série de tutoriais ensina os conceitos básicos da criação de um aplicativo Web Forms do ASP.NET com o ASP.NET 4.7 e o Microsoft Visual Studio Community 2017 para a Web.</span><span class="sxs-lookup"><span data-stu-id="e47da-105">This tutorial series teaches you the basics of building an ASP.NET Web Forms application with ASP.NET 4.7 and Microsoft Visual Studio Community 2017 for the Web.</span></span>

<span data-ttu-id="e47da-106">Neste tutorial, você aprenderá como exibir itens de dados e os detalhes do item de dados com o Web Forms do ASP.NET e o Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="e47da-106">In this tutorial, you learn how to display data items and data item details with ASP.NET Web Forms and Entity Framework Code First.</span></span> <span data-ttu-id="e47da-107">Este tutorial se baseia no tutorial anterior de "Interface do usuário e navegação" como parte da série de tutoriais de Wingtip Toys Store.</span><span class="sxs-lookup"><span data-stu-id="e47da-107">This tutorial builds on the previous "UI and Navigation" tutorial as part of the Wingtip Toy Store tutorial series.</span></span> <span data-ttu-id="e47da-108">No tutorial concluído, produtos na *ProductsList.aspx* página e detalhes do produto sobre o *ProductDetails.aspx* página são exibidos.</span><span class="sxs-lookup"><span data-stu-id="e47da-108">In the completed tutorial, products on the *ProductsList.aspx* page and a product's details on the *ProductDetails.aspx* page are displayed.</span></span>

## <a name="what-you-learn"></a><span data-ttu-id="e47da-109">O que você aprenderá</span><span class="sxs-lookup"><span data-stu-id="e47da-109">What you learn</span></span>

- <span data-ttu-id="e47da-110">Adicione um controle de dados para exibir os produtos de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e47da-110">Add a data control to display database products.</span></span>
- <span data-ttu-id="e47da-111">Conecte-se um controle de dados para dados selecionados.</span><span class="sxs-lookup"><span data-stu-id="e47da-111">Connect a data control to selected data.</span></span>
- <span data-ttu-id="e47da-112">Adicione um controle de dados para exibir detalhes do produto.</span><span class="sxs-lookup"><span data-stu-id="e47da-112">Add a data control to display product details.</span></span>
- <span data-ttu-id="e47da-113">Analisar um valor de cadeia de caracteres de consulta e usá-lo para filtrar os dados do banco de dados recuperado.</span><span class="sxs-lookup"><span data-stu-id="e47da-113">Parse a query string value and use it to filter retrieved database data.</span></span>

<span data-ttu-id="e47da-114">Recursos apresentados neste tutorial incluem a associação de modelos e provedores de valor.</span><span class="sxs-lookup"><span data-stu-id="e47da-114">Features introduced in this tutorial include model binding and value providers.</span></span>

## <a name="add-a-data-control-to-display-products"></a><span data-ttu-id="e47da-115">Adicionar um controle de dados para exibir produtos</span><span class="sxs-lookup"><span data-stu-id="e47da-115">Add a data control to display products</span></span>
 
<span data-ttu-id="e47da-116">Você tem algumas opções para associar dados a um controle de servidor.</span><span class="sxs-lookup"><span data-stu-id="e47da-116">You have a few options to bind data to a server control.</span></span> <span data-ttu-id="e47da-117">As mais comuns incluem:</span><span class="sxs-lookup"><span data-stu-id="e47da-117">The most common include:</span></span>

 * <span data-ttu-id="e47da-118">Adicionando um controle de fonte de dados</span><span class="sxs-lookup"><span data-stu-id="e47da-118">Adding a data source control</span></span>
 * <span data-ttu-id="e47da-119">Adicionando o código manualmente</span><span class="sxs-lookup"><span data-stu-id="e47da-119">Adding code by hand</span></span>
 * <span data-ttu-id="e47da-120">Implementando a associação de modelos</span><span class="sxs-lookup"><span data-stu-id="e47da-120">Implementing model binding</span></span>

### <a name="use-a-data-source-control-to-bind-data"></a><span data-ttu-id="e47da-121">Usar um controle de fonte de dados para associar dados</span><span class="sxs-lookup"><span data-stu-id="e47da-121">Use a data source control to bind data</span></span>

<span data-ttu-id="e47da-122">Adicionar um controle de fonte de dados vincula o controle de fonte de dados para o controle que exibe os dados.</span><span class="sxs-lookup"><span data-stu-id="e47da-122">Adding a data source control links the data source control to the control that displays the data.</span></span> <span data-ttu-id="e47da-123">Com essa abordagem, você pode declarativamente, em vez de programaticamente, conectar controles de servidor para fontes de dados.</span><span class="sxs-lookup"><span data-stu-id="e47da-123">With this approach, you can declaratively, rather than programmatically, connect server-side controls to data sources.</span></span>

### <a name="code-by-hand-to-bind-data"></a><span data-ttu-id="e47da-124">Código manualmente para associar dados</span><span class="sxs-lookup"><span data-stu-id="e47da-124">Code by hand to bind data</span></span>

<span data-ttu-id="e47da-125">Codificação manual envolve:</span><span class="sxs-lookup"><span data-stu-id="e47da-125">Coding by hand involves:</span></span>

1. <span data-ttu-id="e47da-126">Ler um valor</span><span class="sxs-lookup"><span data-stu-id="e47da-126">Reading a value</span></span>
2. <span data-ttu-id="e47da-127">Verificar se ele é nulo</span><span class="sxs-lookup"><span data-stu-id="e47da-127">Checking if it's null</span></span>
3. <span data-ttu-id="e47da-128">Convertendo-o em um tipo apropriado</span><span class="sxs-lookup"><span data-stu-id="e47da-128">Converting it to an appropriate type</span></span>
4. <span data-ttu-id="e47da-129">Verificando o sucesso de conversão</span><span class="sxs-lookup"><span data-stu-id="e47da-129">Checking conversion success</span></span>
5. <span data-ttu-id="e47da-130">Fazer uma consulta com o valor convertido</span><span class="sxs-lookup"><span data-stu-id="e47da-130">Making a query with the converted value</span></span> 

<span data-ttu-id="e47da-131">Com essa abordagem, você tem controle total sobre a lógica de acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="e47da-131">With this approach, you have full control over your data-access logic.</span></span>

### <a name="use-model-binding-to-bind-data"></a><span data-ttu-id="e47da-132">Usar associação de modelo para associar dados</span><span class="sxs-lookup"><span data-stu-id="e47da-132">Use model binding to bind data</span></span>

<span data-ttu-id="e47da-133">Com a associação de modelo, você associa os resultados com muito menos código e fornece a capacidade de reutilizar a funcionalidade em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e47da-133">With model binding, you bind results with far less code and it gives you the ability to reuse the functionality throughout your application.</span></span> <span data-ttu-id="e47da-134">Ele simplifica o trabalho com a lógica de acesso a dados e focada no código enquanto ainda fornecem uma estrutura de vinculação de dados avançada.</span><span class="sxs-lookup"><span data-stu-id="e47da-134">It simplifies working with code-focused data-access logic while still providing a rich, data-binding framework.</span></span>

## <a name="display-products"></a><span data-ttu-id="e47da-135">Exibir produtos</span><span class="sxs-lookup"><span data-stu-id="e47da-135">Display products</span></span>

<span data-ttu-id="e47da-136">Neste tutorial, você pode usar a associação de modelo para associar dados.</span><span class="sxs-lookup"><span data-stu-id="e47da-136">In this tutorial, you use model binding to bind data.</span></span> <span data-ttu-id="e47da-137">Para configurar um controle de dados para usar a associação de modelo para selecionar dados, defina o controle `SelectMethod` propriedade para um método no código da página.</span><span class="sxs-lookup"><span data-stu-id="e47da-137">To configure a data control to use model binding to select data, you set the control's `SelectMethod` property to a method in the page's code.</span></span> <span data-ttu-id="e47da-138">O controle de dados chama o método no momento apropriado no ciclo de vida da página e associa automaticamente os dados retornados.</span><span class="sxs-lookup"><span data-stu-id="e47da-138">The data control calls the method at the appropriate time in the page life cycle and automatically binds the returned data.</span></span> <span data-ttu-id="e47da-139">Não é necessário chamar explicitamente o `DataBind` método.</span><span class="sxs-lookup"><span data-stu-id="e47da-139">There's no need to explicitly call the `DataBind` method.</span></span>

<span data-ttu-id="e47da-140">Trabalhar com as etapas a seguir, modifique *ProductList. aspx* marcação para exibir produtos.</span><span class="sxs-lookup"><span data-stu-id="e47da-140">Working through the following steps, you modify *ProductList.aspx* markup to display products.</span></span>

1. <span data-ttu-id="e47da-141">Na **Gerenciador de soluções**, abra *ProductList. aspx*.</span><span class="sxs-lookup"><span data-stu-id="e47da-141">In **Solution Explorer**, open *ProductList.aspx*.</span></span>

2. <span data-ttu-id="e47da-142">Substitua a marcação existente pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="e47da-142">Replace the existing markup with the following markup:</span></span> 

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

<span data-ttu-id="e47da-143">A marcação anterior usa um **ListView** controle chamado `productList` para exibir produtos.</span><span class="sxs-lookup"><span data-stu-id="e47da-143">The preceding markup uses a **ListView** control named `productList` to display products.</span></span>

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

<span data-ttu-id="e47da-144">Com modelos e estilos, você define como o **ListView** controle exibe dados.</span><span class="sxs-lookup"><span data-stu-id="e47da-144">With templates and styles, you define how the **ListView** control displays data.</span></span> <span data-ttu-id="e47da-145">Ele é útil para dados em qualquer estrutura de repetição.</span><span class="sxs-lookup"><span data-stu-id="e47da-145">It's useful for data in any repeating structure.</span></span> <span data-ttu-id="e47da-146">Embora isso **ListView** exemplo simplesmente exibe dados de banco de dados, você pode também, sem código, permitir que os usuários para editar, inserir e excluir dados e para classificar e dados da página.</span><span class="sxs-lookup"><span data-stu-id="e47da-146">Though this **ListView** example simply displays database data, you can also, without code, enable users to edit, insert, and delete data, and to sort and page data.</span></span>

<span data-ttu-id="e47da-147">Quando você define o `ItemType` propriedade em de **ListView** controlar, a expressão de associação de dados `Item` está disponível e o controle torna-se com rigidez de tipos.</span><span class="sxs-lookup"><span data-stu-id="e47da-147">When you set the `ItemType` property in the **ListView** control, the data-binding expression `Item` is available and the control becomes strongly typed.</span></span> <span data-ttu-id="e47da-148">Conforme mencionado no tutorial anterior, você pode selecionar os detalhes do objeto de Item com o IntelliSense, como especificar o `ProductName`:</span><span class="sxs-lookup"><span data-stu-id="e47da-148">As mentioned in the previous tutorial, you can select Item object details with IntelliSense, such as specifying the `ProductName`:</span></span>

![Exibir dados de itens e detalhes - IntelliSense](display_data_items_and_details/_static/image1.png)

<span data-ttu-id="e47da-150">Com a associação de modelo, você está especificando um `SelectMethod` valor (`GetProducts`).</span><span class="sxs-lookup"><span data-stu-id="e47da-150">With model binding, you're specifying a `SelectMethod` value (`GetProducts`).</span></span> <span data-ttu-id="e47da-151">Esse é o método que você adiciona ao código de trás para exibir produtos na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="e47da-151">This is the method you add to the code behind to display products in the next step.</span></span>

### <a name="add-code-to-display-products"></a><span data-ttu-id="e47da-152">Adicione código para exibir produtos</span><span class="sxs-lookup"><span data-stu-id="e47da-152">Add code to display products</span></span>

<span data-ttu-id="e47da-153">Nesta etapa, você adiciona código para preencher a **ListView** controle com os dados de produto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e47da-153">In this step, you add code to populate the **ListView** control with database product data.</span></span> <span data-ttu-id="e47da-154">O código suporta mostrando todos os produtos e categoria individual.</span><span class="sxs-lookup"><span data-stu-id="e47da-154">The code supports showing all products and individual category products.</span></span>

1. <span data-ttu-id="e47da-155">Na **Gerenciador de soluções**, clique com botão direito *ProductList. aspx* e, em seguida, selecione **Exibir código**.</span><span class="sxs-lookup"><span data-stu-id="e47da-155">In **Solution Explorer**, right-click *ProductList.aspx* and then select **View Code**.</span></span>
2. <span data-ttu-id="e47da-156">Substitua o código existente na *ProductList.aspx.cs* arquivo com este:</span><span class="sxs-lookup"><span data-stu-id="e47da-156">Replace the existing code in the *ProductList.aspx.cs* file with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

<span data-ttu-id="e47da-157">Este código mostra a `GetProducts` método que o **ListView** do controle `ItemType` referências de propriedade em *ProductList. aspx*.</span><span class="sxs-lookup"><span data-stu-id="e47da-157">This code shows the `GetProducts` method that the **ListView** control's `ItemType` property references in *ProductList.aspx*.</span></span> <span data-ttu-id="e47da-158">Para limitar os resultados a uma categoria de banco de dados específico, o código define a `categoryId` o valor da cadeia de consulta passada para *ProductList. aspx*.</span><span class="sxs-lookup"><span data-stu-id="e47da-158">To limit the results to a specific database category, the code sets the `categoryId` value from the query string passed to *ProductList.aspx*.</span></span> <span data-ttu-id="e47da-159">O `QueryStringAttribute` classe de `System.Web.ModelBinding` namespace é usado para recuperar a variável de cadeia de caracteres de consulta `id`do valor.</span><span class="sxs-lookup"><span data-stu-id="e47da-159">The `QueryStringAttribute` class in the `System.Web.ModelBinding` namespace is used to retrieve the query string variable `id`'s value.</span></span> <span data-ttu-id="e47da-160">Isso instrui a associação de modelo, em tempo de execução, associar um valor de cadeia de caracteres de consulta para o `categoryId` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="e47da-160">This instructs model binding to, at run time, bind a query string value to the `categoryId` parameter.</span></span>

<span data-ttu-id="e47da-161">Quando uma categoria válida (`categoryId`) é passado, os resultados são limitados aos produtos de banco de dados da categoria.</span><span class="sxs-lookup"><span data-stu-id="e47da-161">When a valid category (`categoryId`) is passed, the results are limited to that category's database products.</span></span> <span data-ttu-id="e47da-162">Por exemplo, se o *ProductsList.aspx* URL da página é este:</span><span class="sxs-lookup"><span data-stu-id="e47da-162">For instance, if the *ProductsList.aspx* page URL is this:</span></span>

[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

<span data-ttu-id="e47da-163">A página exibe apenas os produtos em que o `categoryId` é igual a `1`.</span><span class="sxs-lookup"><span data-stu-id="e47da-163">The page displays only the products where the `categoryId` equals `1`.</span></span>

<span data-ttu-id="e47da-164">Todos os produtos serão exibidos se nenhuma cadeia de caracteres de consulta é passada.</span><span class="sxs-lookup"><span data-stu-id="e47da-164">All products are displayed if no query string is passed.</span></span>

<span data-ttu-id="e47da-165">As fontes de valor para esses métodos são chamadas *provedores de valor* (como `QueryString`), e os atributos de parâmetro que indicam qual provedor de valor a ser usado são chamados *atributos de provedor de valor* ( como `id`).</span><span class="sxs-lookup"><span data-stu-id="e47da-165">The value sources for these methods are called *value providers* (such as `QueryString`), and the parameter attributes that indicate which value provider to use are called *value provider attributes* (such as `id`).</span></span> <span data-ttu-id="e47da-166">O ASP.NET inclui provedores de valor e os atributos de todos os Web Forms aplicativo usuário entradas fontes típicas.</span><span class="sxs-lookup"><span data-stu-id="e47da-166">ASP.NET includes value providers and attributes for all typical Web Forms application user input sources.</span></span> <span data-ttu-id="e47da-167">Isso inclui a cadeia de caracteres de consulta, cookies, valores de formulário, controles, estado de exibição, o estado de sessão e as propriedades de perfil.</span><span class="sxs-lookup"><span data-stu-id="e47da-167">These include the query string, cookies, form values, controls, view state, session state, and profile properties.</span></span> <span data-ttu-id="e47da-168">Você também pode escrever provedores de valor personalizados.</span><span class="sxs-lookup"><span data-stu-id="e47da-168">You can also write custom value providers.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="e47da-169">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e47da-169">Run the application</span></span>

<span data-ttu-id="e47da-170">Execute o aplicativo agora para exibir todos os produtos ou produtos de uma categoria.</span><span class="sxs-lookup"><span data-stu-id="e47da-170">Run the application now to view all products or a category's products.</span></span>

1. <span data-ttu-id="e47da-171">No Visual Studio, pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e47da-171">In Visual Studio, press **F5** to run the application.</span></span>
 <span data-ttu-id="e47da-172">O navegador é aberta e mostra a *default. aspx* página.</span><span class="sxs-lookup"><span data-stu-id="e47da-172">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="e47da-173">No menu de categoria do produto, selecione **carros**.</span><span class="sxs-lookup"><span data-stu-id="e47da-173">From the product category menu, select **Cars**.</span></span>

   <span data-ttu-id="e47da-174">O *ProductList. aspx* página for exibida, mostrando apenas os produtos da **carros** categoria.</span><span class="sxs-lookup"><span data-stu-id="e47da-174">The *ProductList.aspx* page appears, showing only products from the **Cars** category.</span></span> <span data-ttu-id="e47da-175">Posteriormente no tutorial, é possível exibir detalhes do produto.</span><span class="sxs-lookup"><span data-stu-id="e47da-175">Later in this tutorial, you display product details.</span></span>

    ![Exibir dados de itens e detalhes - carros](display_data_items_and_details/_static/image2.png)

3. <span data-ttu-id="e47da-177">Selecione **produtos** no menu superior.</span><span class="sxs-lookup"><span data-stu-id="e47da-177">Select **Products** from the top menu.</span></span>
 <span data-ttu-id="e47da-178">O *ProductList. aspx* página agora exibe todos os produtos.</span><span class="sxs-lookup"><span data-stu-id="e47da-178">The *ProductList.aspx* page now displays all products.</span></span> 

    ![Exibir dados de itens e detalhes - produtos](display_data_items_and_details/_static/image3.png)

4. <span data-ttu-id="e47da-180">Feche o navegador e retorne ao Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e47da-180">Close the browser and return to Visual Studio.</span></span>

### <a name="add-a-data-control-to-display-product-details"></a><span data-ttu-id="e47da-181">Adicionar um controle de dados para exibir detalhes do produto</span><span class="sxs-lookup"><span data-stu-id="e47da-181">Add a Data Control to display product details</span></span>

<span data-ttu-id="e47da-182">Modificar a *ProductDetails.aspx* marcação que você adicionou no tutorial anterior para exibir informações de produto específico:</span><span class="sxs-lookup"><span data-stu-id="e47da-182">Modify the *ProductDetails.aspx* markup that you added in the previous tutorial to display specific product information:</span></span>

1. <span data-ttu-id="e47da-183">Na **Gerenciador de soluções**, abra *ProductDetails.aspx*.</span><span class="sxs-lookup"><span data-stu-id="e47da-183">In **Solution Explorer**, open *ProductDetails.aspx*.</span></span>

2. <span data-ttu-id="e47da-184">Substitua a marcação existente com essa marcação:</span><span class="sxs-lookup"><span data-stu-id="e47da-184">Replace the existing markup with this markup:</span></span>

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

<span data-ttu-id="e47da-185">Essa marcação usa um **FormView** controle para exibir detalhes do produto específico.</span><span class="sxs-lookup"><span data-stu-id="e47da-185">This markup uses a **FormView** control to display specific product details.</span></span> <span data-ttu-id="e47da-186">Ele usa os métodos, como aqueles usados para exibir dados no *ProductList. aspx*.</span><span class="sxs-lookup"><span data-stu-id="e47da-186">It uses methods like those used to display data in *ProductList.aspx*.</span></span> <span data-ttu-id="e47da-187">O **FormView** controle é usado para exibir um único registro por vez de uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="e47da-187">The **FormView** control is used to display a single record at a time from a data source.</span></span> <span data-ttu-id="e47da-188">Quando você usa o **FormView** controle, você cria modelos para exibir e editar valores de associação de dados.</span><span class="sxs-lookup"><span data-stu-id="e47da-188">When you use the **FormView** control, you create templates to display and edit data-bound values.</span></span> <span data-ttu-id="e47da-189">Esses modelos contêm controles, expressões de associação, e formatação que definem a aparência e a funcionalidade do formulário.</span><span class="sxs-lookup"><span data-stu-id="e47da-189">These templates contain controls, binding expressions, and formatting that define the form's look and functionality.</span></span>

<span data-ttu-id="e47da-190">Conectar-se a marcação anterior para o banco de dados requer código adicional.</span><span class="sxs-lookup"><span data-stu-id="e47da-190">Connecting the previous markup to the database requires additional code.</span></span>

1. <span data-ttu-id="e47da-191">Na **Gerenciador de soluções**, clique com botão direito *ProductDetails.aspx* e, em seguida, selecione **Exibir código**.</span><span class="sxs-lookup"><span data-stu-id="e47da-191">In **Solution Explorer**, right-click *ProductDetails.aspx* and then select **View Code**.</span></span>  
   <span data-ttu-id="e47da-192">O *ProductDetails.aspx.cs* arquivo é exibido.</span><span class="sxs-lookup"><span data-stu-id="e47da-192">The *ProductDetails.aspx.cs* file is displayed.</span></span>

2. <span data-ttu-id="e47da-193">Substitua o código existente com este:</span><span class="sxs-lookup"><span data-stu-id="e47da-193">Replace the existing code with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

<span data-ttu-id="e47da-194">Esse código verifica se há um "`productID`" valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="e47da-194">This code checks for a "`productID`" query string value.</span></span> <span data-ttu-id="e47da-195">Se um valor válido for encontrado, o produto correspondente é exibido.</span><span class="sxs-lookup"><span data-stu-id="e47da-195">If a valid value is found, the matching product is displayed.</span></span> <span data-ttu-id="e47da-196">Se a cadeia de caracteres de consulta não for encontrada, ou seu valor não é válido, nenhum produto será exibido.</span><span class="sxs-lookup"><span data-stu-id="e47da-196">If the query string isn't found, or its value isn't valid, no product is displayed.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="e47da-197">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e47da-197">Run the application</span></span>

<span data-ttu-id="e47da-198">Agora você pode executar o aplicativo para ver os detalhes do produto específico com base no ID do produto.</span><span class="sxs-lookup"><span data-stu-id="e47da-198">Now you can run the application to see specific product details based on product ID.</span></span>

1. <span data-ttu-id="e47da-199">No Visual Studio, pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e47da-199">In Visual Studio, press **F5** to run the application.</span></span>  
 <span data-ttu-id="e47da-200">O navegador abre *default. aspx*.</span><span class="sxs-lookup"><span data-stu-id="e47da-200">The browser opens to *Default.aspx*.</span></span>

2. <span data-ttu-id="e47da-201">No menu de categoria, selecione **barcos**.</span><span class="sxs-lookup"><span data-stu-id="e47da-201">From the category menu, select **Boats**.</span></span>  
 <span data-ttu-id="e47da-202">O *ProductList. aspx* página é exibida.</span><span class="sxs-lookup"><span data-stu-id="e47da-202">The *ProductList.aspx* page is displayed.</span></span>

3. <span data-ttu-id="e47da-203">Selecione **barco do papel**.</span><span class="sxs-lookup"><span data-stu-id="e47da-203">Select **Paper Boat**.</span></span>  
 <span data-ttu-id="e47da-204">O *ProductDetails.aspx* página é exibida.</span><span class="sxs-lookup"><span data-stu-id="e47da-204">The *ProductDetails.aspx* page is displayed.</span></span>   

    ![Exibir dados de itens e detalhes - produtos](display_data_items_and_details/_static/image4.png)

<span data-ttu-id="e47da-206">O próximo tutorial, você adiciona um carrinho de compras no aplicativo Wingtip Toys.</span><span class="sxs-lookup"><span data-stu-id="e47da-206">In the next tutorial, you add a shopping cart to the Wingtip Toys application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e47da-207">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e47da-207">Additional resources</span></span>

[<span data-ttu-id="e47da-208">Recuperando e exibindo dados com a associação de modelos e formulários da web</span><span class="sxs-lookup"><span data-stu-id="e47da-208">Retrieving and displaying data with model binding and web forms</span></span>](../../presenting-and-managing-data/model-binding/retrieving-data.md)

> [!div class="step-by-step"]
> <span data-ttu-id="e47da-209">[Anterior](ui_and_navigation.md)
> [Próximo](shopping-cart.md)</span><span class="sxs-lookup"><span data-stu-id="e47da-209">[Previous](ui_and_navigation.md)
[Next](shopping-cart.md)</span></span>
