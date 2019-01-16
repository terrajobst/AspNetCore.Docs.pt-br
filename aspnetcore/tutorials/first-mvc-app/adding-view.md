---
title: Adicionar uma exibição a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Adicionando uma exibição a um aplicativo ASP.NET Core MVC simples
ms.author: riande
ms.date: 03/04/2017
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 321ffd6b0168d4befc950a58035d19561e879491
ms.sourcegitcommit: ec71fd5a988f927ae301813aae5ff764feb3bb6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249445"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="c15ff-103">Adicionar uma exibição a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="c15ff-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="c15ff-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c15ff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c15ff-105">Nesta seção, você modifica a classe `HelloWorldController` para que ela use os arquivos de exibição do [Razor](xref:mvc/views/razor) para encapsular corretamente o processo de geração de respostas HTML para um cliente.</span><span class="sxs-lookup"><span data-stu-id="c15ff-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="c15ff-106">Crie um arquivo de modelo de exibição usando o Razor.</span><span class="sxs-lookup"><span data-stu-id="c15ff-106">You create a view template file using Razor.</span></span> <span data-ttu-id="c15ff-107">Os modelos de exibição baseados no Razor têm uma extensão de arquivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c15ff-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="c15ff-108">Eles fornecem uma maneira elegante de criar a saída HTML com o C#.</span><span class="sxs-lookup"><span data-stu-id="c15ff-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="c15ff-109">Atualmente, o método `Index` retorna uma cadeia de caracteres com uma mensagem que é embutida em código na classe do controlador.</span><span class="sxs-lookup"><span data-stu-id="c15ff-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="c15ff-110">Na classe `HelloWorldController`, substitua o método `Index` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="c15ff-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="c15ff-111">O código anterior chama o método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> do controlador.</span><span class="sxs-lookup"><span data-stu-id="c15ff-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="c15ff-112">Ele usa um modelo de exibição para gerar uma resposta HTML.</span><span class="sxs-lookup"><span data-stu-id="c15ff-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="c15ff-113">Métodos do controlador (também conhecidos como *métodos de ação*), como o método `Index` acima, geralmente retornam um <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou uma classe derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), não um tipo como `string`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="c15ff-114">Adicionar uma exibição</span><span class="sxs-lookup"><span data-stu-id="c15ff-114">Add a view</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c15ff-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c15ff-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c15ff-116">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="c15ff-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="c15ff-117">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="c15ff-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="c15ff-118">Na caixa de diálogo **Adicionar Novo Item – MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="c15ff-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="c15ff-119">Na caixa de pesquisa no canto superior direito, insira *exibição*</span><span class="sxs-lookup"><span data-stu-id="c15ff-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="c15ff-120">Selecione **Exibição Razor**</span><span class="sxs-lookup"><span data-stu-id="c15ff-120">Select **Razor View**</span></span>

  * <span data-ttu-id="c15ff-121">Mantenha o valor da caixa **Nome**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c15ff-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="c15ff-122">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="c15ff-122">Select **Add**</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c15ff-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c15ff-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c15ff-125">Adicione uma exibição `Index` ao `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="c15ff-126">Adicione uma nova pasta chamada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="c15ff-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="c15ff-127">Adicione um novo arquivo à pasta *Views/HelloWorld* chamada *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c15ff-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c15ff-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c15ff-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c15ff-129">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="c15ff-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="c15ff-130">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="c15ff-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="c15ff-131">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="c15ff-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="c15ff-132">Selecione **Web** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="c15ff-132">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="c15ff-133">Selecione **Arquivo HTML vazio** no painel central.</span><span class="sxs-lookup"><span data-stu-id="c15ff-133">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="c15ff-134">Digite *Index.cshtml* na caixa **Nome**.</span><span class="sxs-lookup"><span data-stu-id="c15ff-134">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="c15ff-135">Selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="c15ff-135">Select **New**.</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view.png)

---  
<!-- End of VS tabs -->

<span data-ttu-id="c15ff-137">Substitua o conteúdo do arquivo de exibição *Views/HelloWorld/Index.cshtml* do Razor pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="c15ff-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="c15ff-138">Navegue para `https://localhost:xxxx/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-138">Navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="c15ff-139">O método `Index` no `HelloWorldController` não fez muita coisa: ele executou a instrução `return View();`, que especificou que o método deve usar um arquivo de modelo de exibição para renderizar uma resposta para o navegador.</span><span class="sxs-lookup"><span data-stu-id="c15ff-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="c15ff-140">Como você não especificou explicitamente o nome do arquivo do modelo de exibição, o MVC usou como padrão o arquivo de exibição *Index.cshtml* na pasta */Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="c15ff-140">Because you didn't explicitly specify the name of the view template file, MVC defaulted to using the *Index.cshtml* view file in the */Views/HelloWorld* folder.</span></span> <span data-ttu-id="c15ff-141">A imagem abaixo mostra a cadeia de caracteres “Olá de nosso modelo de exibição!”</span><span class="sxs-lookup"><span data-stu-id="c15ff-141">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="c15ff-142">embutida em código na exibição.</span><span class="sxs-lookup"><span data-stu-id="c15ff-142">hard-coded in the view.</span></span>

![Janela do navegador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="c15ff-144">Alterar exibições e páginas de layout</span><span class="sxs-lookup"><span data-stu-id="c15ff-144">Change views and layout pages</span></span>

<span data-ttu-id="c15ff-145">Selecione os links de menu (**MvcMovie**, **Página Inicial** e **Privacidade**).</span><span class="sxs-lookup"><span data-stu-id="c15ff-145">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="c15ff-146">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="c15ff-146">Each page shows the same menu layout.</span></span> <span data-ttu-id="c15ff-147">O layout de menu é implementado no arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c15ff-147">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="c15ff-148">Abra o arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c15ff-148">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="c15ff-149">Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site.</span><span class="sxs-lookup"><span data-stu-id="c15ff-149">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="c15ff-150">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-150">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="c15ff-151">`RenderBody` é um espaço reservado em que todas as páginas específicas à exibição criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="c15ff-151">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="c15ff-152">Por exemplo, se você selecionar o link **Privacidade**, a exibição **Views/Home/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-152">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="c15ff-153">Alterar o título, o rodapé e o link de menu no arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="c15ff-153">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="c15ff-154">Nos elementos de título e de rodapé, altere `MvcMovie` para `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-154">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="c15ff-155">Altere o elemento de âncora `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` para `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-155">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="c15ff-156">A seguinte marcação mostra as alterações realçadas:</span><span class="sxs-lookup"><span data-stu-id="c15ff-156">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24)]

<span data-ttu-id="c15ff-157">Na marcação anterior, o `asp-area` [atributo do Auxiliar de Marca de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) foi omitido porque este aplicativo não está usando [Áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="c15ff-157">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="c15ff-158">**Observação**: O controlador `Movies` não foi implementado.</span><span class="sxs-lookup"><span data-stu-id="c15ff-158">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="c15ff-159">Neste ponto, o link `Movie App` não está funcionando.</span><span class="sxs-lookup"><span data-stu-id="c15ff-159">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="c15ff-160">Salve suas alterações e selecione o link **Privacidade**.</span><span class="sxs-lookup"><span data-stu-id="c15ff-160">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="c15ff-161">Observe como o título na guia do navegador agora exibe **Privacidade – Aplicativo de Filme**, em vez de **Privacidade – Filme Mvc**:</span><span class="sxs-lookup"><span data-stu-id="c15ff-161">Notice how the title on the browser tab displays **Privacy - Movie App** instead of **Privacy - Mvc Movie**:</span></span>

![Guia Privacidade](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="c15ff-163">Selecione o link **Página Inicial** e observe que o texto do título e de âncora também exibem **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="c15ff-163">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="c15ff-164">Conseguimos fazer a alteração uma vez no modelo de layout e fazer com que todas as páginas no site refletissem o novo texto do link e o novo título.</span><span class="sxs-lookup"><span data-stu-id="c15ff-164">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="c15ff-165">Examine o arquivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c15ff-165">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="c15ff-166">O arquivo *Views/_ViewStart.cshtml* mostra o arquivo *Views/Shared/_Layout.cshtml* em cada exibição.</span><span class="sxs-lookup"><span data-stu-id="c15ff-166">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="c15ff-167">A propriedade `Layout` pode ser usada para definir outra exibição de layout ou defina-a como `null` para que nenhum arquivo de layout seja usado.</span><span class="sxs-lookup"><span data-stu-id="c15ff-167">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="c15ff-168">Altere o título e o elemento `<h2>` do arquivo de exibição *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c15ff-168">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="c15ff-169">O título e o elemento `<h2>` são ligeiramente diferentes para que possa ver qual parte do código altera a exibição.</span><span class="sxs-lookup"><span data-stu-id="c15ff-169">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="c15ff-170">`ViewData["Title"] = "Movie List";` no código acima define a propriedade `Title` do dicionário `ViewData` como “Lista de Filmes”.</span><span class="sxs-lookup"><span data-stu-id="c15ff-170">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="c15ff-171">A propriedade `Title` é usada no elemento HTML `<title>` na página de layout:</span><span class="sxs-lookup"><span data-stu-id="c15ff-171">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="c15ff-172">Salve as alterações e navegue para `https://localhost:xxxx/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-172">Save the change and navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="c15ff-173">Observe que o título do navegador, o cabeçalho primário e os títulos secundários foram alterados.</span><span class="sxs-lookup"><span data-stu-id="c15ff-173">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="c15ff-174">(Se as alterações não forem exibidas no navegador, talvez o conteúdo armazenado em cache esteja sendo exibido.</span><span class="sxs-lookup"><span data-stu-id="c15ff-174">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="c15ff-175">Pressione Ctrl+F5 no navegador para forçar a resposta do servidor a ser carregada.) O título do navegador é criado com `ViewData["Title"]` que definimos no modelo de exibição *Index.cshtml* e o “– Aplicativo de Filme” adicional adicionado no arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="c15ff-175">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="c15ff-176">Observe também como o conteúdo no modelo de exibição *Index.cshtml* foi mesclado com o modelo de exibição *Views/Shared/_Layout.cshtml* e uma única resposta HTML foi enviada para o navegador.</span><span class="sxs-lookup"><span data-stu-id="c15ff-176">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="c15ff-177">Os modelos de layout facilitam realmente a realização de alterações que se aplicam a todas as páginas do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c15ff-177">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="c15ff-178">Para saber mais, consulte [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="c15ff-178">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Exibição de Lista de Filmes](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="c15ff-180">Apesar disso, nossos poucos “dados” (nesse caso, a mensagem “Olá de nosso modelo de exibição!”)</span><span class="sxs-lookup"><span data-stu-id="c15ff-180">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="c15ff-181">são embutidos em código.</span><span class="sxs-lookup"><span data-stu-id="c15ff-181">message) is hard-coded, though.</span></span> <span data-ttu-id="c15ff-182">O aplicativo MVC tem um “V” (exibição) e você tem um “C” (controlador), mas ainda nenhum “M” (modelo).</span><span class="sxs-lookup"><span data-stu-id="c15ff-182">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="c15ff-183">Passando dados do controlador para a exibição</span><span class="sxs-lookup"><span data-stu-id="c15ff-183">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="c15ff-184">As ações do controlador são invocadas em resposta a uma solicitação de URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="c15ff-184">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="c15ff-185">Uma classe de controlador é o local em que o código é escrito e que manipula as solicitações recebidas do navegador.</span><span class="sxs-lookup"><span data-stu-id="c15ff-185">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="c15ff-186">O controlador recupera dados de uma fonte de dados e decide qual tipo de resposta será enviada novamente para o navegador.</span><span class="sxs-lookup"><span data-stu-id="c15ff-186">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="c15ff-187">Modelos de exibição podem ser usados em um controlador para gerar e formatar uma resposta HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="c15ff-187">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="c15ff-188">Os controladores são responsáveis por fornecer os dados necessários para que um modelo de exibição renderize uma resposta.</span><span class="sxs-lookup"><span data-stu-id="c15ff-188">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="c15ff-189">Uma melhor prática: modelos de exibição **não** devem executar a lógica de negócios nem interagir diretamente com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c15ff-189">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="c15ff-190">Em vez disso, um modelo de exibição deve funcionar somente com os dados fornecidos pelo controlador.</span><span class="sxs-lookup"><span data-stu-id="c15ff-190">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="c15ff-191">Manter essa “separação de preocupações” ajuda a manter o código limpo, testável e com capacidade de manutenção.</span><span class="sxs-lookup"><span data-stu-id="c15ff-191">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="c15ff-192">Atualmente, o método `Welcome` na classe `HelloWorldController` usa um parâmetro `name` e um `ID` e, em seguida, gera os valores diretamente no navegador.</span><span class="sxs-lookup"><span data-stu-id="c15ff-192">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="c15ff-193">Em vez de fazer com que o controlador renderize a resposta como uma cadeia de caracteres, altere o controlador para que ele use um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="c15ff-193">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="c15ff-194">O modelo de exibição gera uma resposta dinâmica, o que significa que é necessário passar bits de dados apropriados do controlador para a exibição para gerar a resposta.</span><span class="sxs-lookup"><span data-stu-id="c15ff-194">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="c15ff-195">Faça isso fazendo com que o controlador coloque os dados dinâmicos (parâmetros) que o modelo de exibição precisa em um dicionário `ViewData` que pode ser acessado em seguida pelo modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="c15ff-195">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="c15ff-196">Em *HelloWorldController.cs*, altere o método `Welcome` para adicionar um valor `Message` e `NumTimes` ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-196">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="c15ff-197">O dicionário `ViewData` é um objeto dinâmico, o que significa que qualquer tipo pode ser usado. O objeto `ViewData` não tem nenhuma propriedade definida até que você insira algo nele.</span><span class="sxs-lookup"><span data-stu-id="c15ff-197">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="c15ff-198">O sistema de [model binding](xref:mvc/models/model-binding) MVC mapeia automaticamente os parâmetros nomeados (`name` e `numTimes`) da cadeia de consulta na barra de endereços para os parâmetros no método.</span><span class="sxs-lookup"><span data-stu-id="c15ff-198">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="c15ff-199">O arquivo *HelloWorldController.cs* completo tem esta aparência:</span><span class="sxs-lookup"><span data-stu-id="c15ff-199">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="c15ff-200">O objeto de dicionário `ViewData` contém dados que serão passados para a exibição.</span><span class="sxs-lookup"><span data-stu-id="c15ff-200">The `ViewData` dictionary object contains data that will be passed to the view.</span></span> 

<span data-ttu-id="c15ff-201">Crie um modelo de exibição Boas-vindas chamado *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c15ff-201">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="c15ff-202">Você criará um loop no modelo de exibição *Welcome.cshtml* que exibe “Olá” `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-202">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="c15ff-203">Substitua o conteúdo de *Views/HelloWorld/Welcome.cshtml* pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="c15ff-203">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="c15ff-204">Salve as alterações e navegue para a seguinte URL:</span><span class="sxs-lookup"><span data-stu-id="c15ff-204">Save your changes and browse to the following URL:</span></span>

`https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="c15ff-205">Os dados são obtidos da URL e passados para o controlador usando o [associador de modelo MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="c15ff-205">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="c15ff-206">O controlador empacota os dados em um dicionário `ViewData` e passa esse objeto para a exibição.</span><span class="sxs-lookup"><span data-stu-id="c15ff-206">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="c15ff-207">Em seguida, a exibição renderiza os dados como HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="c15ff-207">The view then renders the data as HTML to the browser.</span></span>

![Exibição de privacidade que mostra um rótulo Boas-vindas e a frase Olá, Ricardo mostrada quatro vezes](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="c15ff-209">No exemplo acima, o dicionário `ViewData` foi usado para passar dados do controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="c15ff-209">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="c15ff-210">Mais adiante no tutorial, um modelo de exibição será usado para passar dados de um controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="c15ff-210">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="c15ff-211">A abordagem de modelo de exibição para passar dados é geralmente a preferida em relação à abordagem do dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="c15ff-211">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="c15ff-212">Para obter mais informações, confira [Quando usar ViewBag, ViewData ou TempData ](http://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="c15ff-212">See [When to use ViewBag, ViewData, or TempData ](http://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="c15ff-213">No próximo tutorial, será criado um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="c15ff-213">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c15ff-214">[Anterior](adding-controller.md)
> [Próximo](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="c15ff-214">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>
