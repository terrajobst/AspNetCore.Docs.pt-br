---
title: Adicionar uma exibição a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Adicionando uma exibição a um aplicativo ASP.NET Core MVC simples
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 5510fb6844452571ca764e21640f0bd16444c782
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171980"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="1c393-103">Adicionar uma exibição a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="1c393-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="1c393-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1c393-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1c393-105">Nesta seção, você modifica a classe `HelloWorldController` para que ela use os arquivos de exibição do [Razor](xref:mvc/views/razor) para encapsular corretamente o processo de geração de respostas HTML para um cliente.</span><span class="sxs-lookup"><span data-stu-id="1c393-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="1c393-106">Crie um arquivo de modelo de exibição usando o Razor.</span><span class="sxs-lookup"><span data-stu-id="1c393-106">You create a view template file using Razor.</span></span> <span data-ttu-id="1c393-107">Os modelos de exibição baseados no Razor têm uma extensão de arquivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="1c393-108">Eles fornecem uma maneira elegante de criar a saída HTML com o C#.</span><span class="sxs-lookup"><span data-stu-id="1c393-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="1c393-109">Atualmente, o método `Index` retorna uma cadeia de caracteres com uma mensagem que é embutida em código na classe do controlador.</span><span class="sxs-lookup"><span data-stu-id="1c393-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="1c393-110">Na classe `HelloWorldController`, substitua o método `Index` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1c393-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="1c393-111">O código anterior chama o método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> do controlador.</span><span class="sxs-lookup"><span data-stu-id="1c393-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="1c393-112">Ele usa um modelo de exibição para gerar uma resposta HTML.</span><span class="sxs-lookup"><span data-stu-id="1c393-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="1c393-113">Métodos do controlador (também conhecidos como *métodos de ação*), como o método `Index` acima, geralmente retornam um <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou uma classe derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), não um tipo como `string`.</span><span class="sxs-lookup"><span data-stu-id="1c393-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="1c393-114">Adicionar uma exibição</span><span class="sxs-lookup"><span data-stu-id="1c393-114">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1c393-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c393-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c393-116">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1c393-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="1c393-117">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="1c393-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="1c393-118">Na caixa de diálogo **Adicionar Novo Item – MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="1c393-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="1c393-119">Na caixa de pesquisa no canto superior direito, insira *exibição*</span><span class="sxs-lookup"><span data-stu-id="1c393-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="1c393-120">Selecione **Exibição Razor**</span><span class="sxs-lookup"><span data-stu-id="1c393-120">Select **Razor View**</span></span>

  * <span data-ttu-id="1c393-121">Mantenha o valor da caixa **Nome**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="1c393-122">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="1c393-122">Select **Add**</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1c393-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c393-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c393-125">Adicione uma exibição `Index` ao `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="1c393-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="1c393-126">Adicione uma nova pasta chamada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1c393-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="1c393-127">Adicione um novo arquivo à pasta *Views/HelloWorld* chamada *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1c393-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1c393-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1c393-129">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1c393-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="1c393-130">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="1c393-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="1c393-131">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="1c393-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="1c393-132">Selecione **ASP .NET Core** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="1c393-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="1c393-133">Selecione a **página exibição do MVC** no painel central.</span><span class="sxs-lookup"><span data-stu-id="1c393-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="1c393-134">Digite *index* na caixa **nome** .</span><span class="sxs-lookup"><span data-stu-id="1c393-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="1c393-135">Selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="1c393-135">Select **New**.</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="1c393-137">Substitua o conteúdo do arquivo de exibição *Views/HelloWorld/Index.cshtml* do Razor pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="1c393-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="1c393-138">Navegue até `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="1c393-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="1c393-139">O método `Index` no `HelloWorldController` não fez muita coisa: ele executou a instrução `return View();`, que especificou que o método deve usar um arquivo de modelo de exibição para renderizar uma resposta para o navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="1c393-140">Como um nome de arquivo de modo de exibição não foi especificado, o MVC é padronizado para usar o arquivo de exibição padrão.</span><span class="sxs-lookup"><span data-stu-id="1c393-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="1c393-141">O arquivo de exibição padrão tem o mesmo nome que o método (`Index`), portanto, é usado */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-141">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="1c393-142">A imagem abaixo mostra a cadeia de caracteres “Olá de nosso modelo de exibição!”</span><span class="sxs-lookup"><span data-stu-id="1c393-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="1c393-143">embutida em código na exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-143">hard-coded in the view.</span></span>

![Janela do navegador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="1c393-145">Alterar exibições e páginas de layout</span><span class="sxs-lookup"><span data-stu-id="1c393-145">Change views and layout pages</span></span>

<span data-ttu-id="1c393-146">Selecione os links de menu (**MvcMovie**, **Página Inicial** e **Privacidade**).</span><span class="sxs-lookup"><span data-stu-id="1c393-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="1c393-147">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="1c393-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="1c393-148">O layout de menu é implementado no arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="1c393-149">Abra o arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1c393-150">Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site.</span><span class="sxs-lookup"><span data-stu-id="1c393-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="1c393-151">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="1c393-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="1c393-152">`RenderBody` é um espaço reservado em que todas as páginas específicas à exibição criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="1c393-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="1c393-153">Por exemplo, se você selecionar o link **Privacidade**, a exibição **Views/Home/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="1c393-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="1c393-154">Alterar o título, o rodapé e o link de menu no arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="1c393-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="1c393-155">Substitua o conteúdo do arquivo *views/Shared/_Layout. cshtml* pela marcação a seguir.</span><span class="sxs-lookup"><span data-stu-id="1c393-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="1c393-156">As alterações são realçadas:</span><span class="sxs-lookup"><span data-stu-id="1c393-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="1c393-157">A marcação anterior fez as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="1c393-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="1c393-158">3 ocorrências de `MvcMovie` para `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="1c393-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="1c393-159">O elemento de âncora `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` para `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="1c393-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="1c393-160">Na marcação anterior, o [atributo auxiliar de marca de ancoragem](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-area=""` e o valor do atributo foi omitido porque esse aplicativo não está usando [áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="1c393-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="1c393-161">**Observação**: o controlador de `Movies` não foi implementado.</span><span class="sxs-lookup"><span data-stu-id="1c393-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="1c393-162">Neste ponto, o link `Movie App` não está funcionando.</span><span class="sxs-lookup"><span data-stu-id="1c393-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="1c393-163">Salve suas alterações e selecione o link **Privacidade**.</span><span class="sxs-lookup"><span data-stu-id="1c393-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="1c393-164">Observe como o título na guia do navegador agora exibe **Política de Privacidade – Aplicativo de filme**, em vez de **Política de Privacidade – Filme MVC**:</span><span class="sxs-lookup"><span data-stu-id="1c393-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Guia Privacidade](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="1c393-166">Selecione o link **Página Inicial** e observe que o texto do título e de âncora também exibem **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="1c393-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="1c393-167">Conseguimos fazer a alteração uma vez no modelo de layout e fazer com que todas as páginas no site refletissem o novo texto do link e o novo título.</span><span class="sxs-lookup"><span data-stu-id="1c393-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="1c393-168">Examine o arquivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1c393-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="1c393-169">O arquivo *Views/_ViewStart.cshtml* mostra o arquivo *Views/Shared/_Layout.cshtml* em cada exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="1c393-170">A propriedade `Layout` pode ser usada para definir outra exibição de layout ou defina-a como `null` para que nenhum arquivo de layout seja usado.</span><span class="sxs-lookup"><span data-stu-id="1c393-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="1c393-171">Altere o título e o elemento `<h2>` do arquivo de exibição *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1c393-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="1c393-172">O título e o elemento `<h2>` são ligeiramente diferentes para que possa ver qual parte do código altera a exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="1c393-173">`ViewData["Title"] = "Movie List";` no código acima define a propriedade `Title` do dicionário `ViewData` como “Lista de Filmes”.</span><span class="sxs-lookup"><span data-stu-id="1c393-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="1c393-174">A propriedade `Title` é usada no elemento HTML `<title>` na página de layout:</span><span class="sxs-lookup"><span data-stu-id="1c393-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="1c393-175">Salve as alterações e navegue para `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="1c393-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="1c393-176">Observe que o título do navegador, o cabeçalho primário e os títulos secundários foram alterados.</span><span class="sxs-lookup"><span data-stu-id="1c393-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="1c393-177">(Se as alterações não forem exibidas no navegador, talvez o conteúdo armazenado em cache esteja sendo exibido.</span><span class="sxs-lookup"><span data-stu-id="1c393-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="1c393-178">Pressione CTRL + F5 em seu navegador para forçar a resposta do servidor a ser carregado.) O título do navegador é criado com `ViewData["Title"]` que definimos no modelo de exibição *index. cshtml* e o "aplicativo de filme" adicional adicionado ao arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="1c393-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="1c393-179">O conteúdo do modelo de exibição *Index.cshtml* é mesclado com o modelo de exibição *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="1c393-180">Uma única resposta HTML é enviada ao navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="1c393-181">Os modelos de layout facilitam a realização de alterações que se aplicam a todas as páginas de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1c393-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="1c393-182">Para saber mais, consulte [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="1c393-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Exibição de Lista de Filmes](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="1c393-184">Apesar disso, nossos poucos “dados” (nesse caso, a mensagem “Olá de nosso modelo de exibição!”)</span><span class="sxs-lookup"><span data-stu-id="1c393-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="1c393-185">são embutidos em código.</span><span class="sxs-lookup"><span data-stu-id="1c393-185">message) is hard-coded, though.</span></span> <span data-ttu-id="1c393-186">O aplicativo MVC tem um “V” (exibição) e você tem um “C” (controlador), mas ainda nenhum “M” (modelo).</span><span class="sxs-lookup"><span data-stu-id="1c393-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="1c393-187">Passando dados do controlador para a exibição</span><span class="sxs-lookup"><span data-stu-id="1c393-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="1c393-188">As ações do controlador são invocadas em resposta a uma solicitação de URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="1c393-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="1c393-189">Uma classe de controlador é o local em que o código é escrito e que manipula as solicitações recebidas do navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="1c393-190">O controlador recupera dados de uma fonte de dados e decide qual tipo de resposta será enviada novamente para o navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="1c393-191">Modelos de exibição podem ser usados em um controlador para gerar e formatar uma resposta HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="1c393-192">Os controladores são responsáveis por fornecer os dados necessários para que um modelo de exibição renderize uma resposta.</span><span class="sxs-lookup"><span data-stu-id="1c393-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="1c393-193">Uma melhor prática: modelos de exibição **não** devem executar a lógica de negócios nem interagir diretamente com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1c393-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="1c393-194">Em vez disso, um modelo de exibição deve funcionar somente com os dados fornecidos pelo controlador.</span><span class="sxs-lookup"><span data-stu-id="1c393-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="1c393-195">Manter essa “separação de preocupações” ajuda a manter o código limpo, testável e com capacidade de manutenção.</span><span class="sxs-lookup"><span data-stu-id="1c393-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="1c393-196">Atualmente, o método `Welcome` na classe `HelloWorldController` usa um parâmetro `name` e um `ID` e, em seguida, gera os valores diretamente no navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="1c393-197">Em vez de fazer com que o controlador renderize a resposta como uma cadeia de caracteres, altere o controlador para que ele use um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="1c393-198">O modelo de exibição gera uma resposta dinâmica, o que significa que é necessário passar bits de dados apropriados do controlador para a exibição para gerar a resposta.</span><span class="sxs-lookup"><span data-stu-id="1c393-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="1c393-199">Faça isso fazendo com que o controlador coloque os dados dinâmicos (parâmetros) que o modelo de exibição precisa em um dicionário `ViewData` que pode ser acessado em seguida pelo modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="1c393-200">Em *HelloWorldController.cs*, altere o método `Welcome` para adicionar um valor `Message` e `NumTimes` ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="1c393-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="1c393-201">O dicionário `ViewData` é um objeto dinâmico, o que significa que qualquer tipo pode ser usado. O objeto `ViewData` não tem nenhuma propriedade definida até que você insira algo nele.</span><span class="sxs-lookup"><span data-stu-id="1c393-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="1c393-202">O sistema de [model binding](xref:mvc/models/model-binding) MVC mapeia automaticamente os parâmetros nomeados (`name` e `numTimes`) da cadeia de consulta na barra de endereços para os parâmetros no método.</span><span class="sxs-lookup"><span data-stu-id="1c393-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="1c393-203">O arquivo *HelloWorldController.cs* completo tem esta aparência:</span><span class="sxs-lookup"><span data-stu-id="1c393-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="1c393-204">O objeto de dicionário `ViewData` contém dados que serão passados para a exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="1c393-205">Crie um modelo de exibição Boas-vindas chamado *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="1c393-206">Você criará um loop no modelo de exibição *Welcome.cshtml* que exibe “Olá” `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="1c393-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="1c393-207">Substitua o conteúdo de *Views/HelloWorld/Welcome.cshtml* pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="1c393-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="1c393-208">Salve as alterações e navegue para a seguinte URL:</span><span class="sxs-lookup"><span data-stu-id="1c393-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="1c393-209">Os dados são obtidos da URL e passados para o controlador usando o [associador de modelo MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1c393-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="1c393-210">O controlador empacota os dados em um dicionário `ViewData` e passa esse objeto para a exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="1c393-211">Em seguida, a exibição renderiza os dados como HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-211">The view then renders the data as HTML to the browser.</span></span>

![Exibição de privacidade que mostra um rótulo Boas-vindas e a frase Olá, Ricardo mostrada quatro vezes](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="1c393-213">No exemplo acima, o dicionário `ViewData` foi usado para passar dados do controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="1c393-214">Mais adiante no tutorial, um modelo de exibição será usado para passar dados de um controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="1c393-215">A abordagem de modelo de exibição para passar dados é geralmente a preferida em relação à abordagem do dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="1c393-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="1c393-216">Confira mais informações em [Quando usar ViewBag, ViewData ou TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="1c393-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="1c393-217">No próximo tutorial, será criado um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="1c393-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1c393-218">[Anterior](adding-controller.md)
> [Próximo](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="1c393-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1c393-219">Nesta seção, você modifica a classe `HelloWorldController` para que ela use os arquivos de exibição do [Razor](xref:mvc/views/razor) para encapsular corretamente o processo de geração de respostas HTML para um cliente.</span><span class="sxs-lookup"><span data-stu-id="1c393-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="1c393-220">Crie um arquivo de modelo de exibição usando o Razor.</span><span class="sxs-lookup"><span data-stu-id="1c393-220">You create a view template file using Razor.</span></span> <span data-ttu-id="1c393-221">Os modelos de exibição baseados no Razor têm uma extensão de arquivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="1c393-222">Eles fornecem uma maneira elegante de criar a saída HTML com o C#.</span><span class="sxs-lookup"><span data-stu-id="1c393-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="1c393-223">Atualmente, o método `Index` retorna uma cadeia de caracteres com uma mensagem que é embutida em código na classe do controlador.</span><span class="sxs-lookup"><span data-stu-id="1c393-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="1c393-224">Na classe `HelloWorldController`, substitua o método `Index` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1c393-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="1c393-225">O código anterior chama o método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> do controlador.</span><span class="sxs-lookup"><span data-stu-id="1c393-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="1c393-226">Ele usa um modelo de exibição para gerar uma resposta HTML.</span><span class="sxs-lookup"><span data-stu-id="1c393-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="1c393-227">Métodos do controlador (também conhecidos como *métodos de ação*), como o método `Index` acima, geralmente retornam um <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou uma classe derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), não um tipo como `string`.</span><span class="sxs-lookup"><span data-stu-id="1c393-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="1c393-228">Adicionar uma exibição</span><span class="sxs-lookup"><span data-stu-id="1c393-228">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1c393-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c393-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c393-230">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1c393-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="1c393-231">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="1c393-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="1c393-232">Na caixa de diálogo **Adicionar Novo Item – MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="1c393-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="1c393-233">Na caixa de pesquisa no canto superior direito, insira *exibição*</span><span class="sxs-lookup"><span data-stu-id="1c393-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="1c393-234">Selecione **Exibição Razor**</span><span class="sxs-lookup"><span data-stu-id="1c393-234">Select **Razor View**</span></span>

  * <span data-ttu-id="1c393-235">Mantenha o valor da caixa **Nome**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="1c393-236">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="1c393-236">Select **Add**</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1c393-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c393-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c393-239">Adicione uma exibição `Index` ao `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="1c393-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="1c393-240">Adicione uma nova pasta chamada *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1c393-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="1c393-241">Adicione um novo arquivo à pasta *Views/HelloWorld* chamada *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1c393-242">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1c393-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1c393-243">Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1c393-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="1c393-244">Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="1c393-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="1c393-245">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="1c393-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="1c393-246">Selecione **Web** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="1c393-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="1c393-247">Selecione **Arquivo HTML vazio** no painel central.</span><span class="sxs-lookup"><span data-stu-id="1c393-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="1c393-248">Digite *Index.cshtml* na caixa **Nome**.</span><span class="sxs-lookup"><span data-stu-id="1c393-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="1c393-249">Selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="1c393-249">Select **New**.</span></span>

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="1c393-251">Substitua o conteúdo do arquivo de exibição *Views/HelloWorld/Index.cshtml* do Razor pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="1c393-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="1c393-252">Navegue até `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="1c393-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="1c393-253">O método `Index` no `HelloWorldController` não fez muita coisa: ele executou a instrução `return View();`, que especificou que o método deve usar um arquivo de modelo de exibição para renderizar uma resposta para o navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="1c393-254">Como um nome de arquivo de modo de exibição não foi especificado, o MVC é padronizado para usar o arquivo de exibição padrão.</span><span class="sxs-lookup"><span data-stu-id="1c393-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="1c393-255">O arquivo de exibição padrão tem o mesmo nome que o método (`Index`), portanto, é usado */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="1c393-256">A imagem abaixo mostra a cadeia de caracteres “Olá de nosso modelo de exibição!”</span><span class="sxs-lookup"><span data-stu-id="1c393-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="1c393-257">embutida em código na exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-257">hard-coded in the view.</span></span>

![Janela do navegador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="1c393-259">Alterar exibições e páginas de layout</span><span class="sxs-lookup"><span data-stu-id="1c393-259">Change views and layout pages</span></span>

<span data-ttu-id="1c393-260">Selecione os links de menu (**MvcMovie**, **Página Inicial** e **Privacidade**).</span><span class="sxs-lookup"><span data-stu-id="1c393-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="1c393-261">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="1c393-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="1c393-262">O layout de menu é implementado no arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="1c393-263">Abra o arquivo *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1c393-264">Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site.</span><span class="sxs-lookup"><span data-stu-id="1c393-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="1c393-265">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="1c393-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="1c393-266">`RenderBody` é um espaço reservado em que todas as páginas específicas à exibição criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="1c393-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="1c393-267">Por exemplo, se você selecionar o link **Privacidade**, a exibição **Views/Home/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="1c393-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="1c393-268">Alterar o título, o rodapé e o link de menu no arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="1c393-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="1c393-269">Nos elementos de título e de rodapé, altere `MvcMovie` para `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="1c393-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="1c393-270">Altere o elemento de âncora `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` para `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="1c393-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="1c393-271">A seguinte marcação mostra as alterações realçadas:</span><span class="sxs-lookup"><span data-stu-id="1c393-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="1c393-272">Na marcação anterior, o [atributo auxiliar da marca de ancoragem](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-area` foi omitido porque este aplicativo não está usando [áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="1c393-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="1c393-273">**Observação**: o controlador de `Movies` não foi implementado.</span><span class="sxs-lookup"><span data-stu-id="1c393-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="1c393-274">Neste ponto, o link `Movie App` não está funcionando.</span><span class="sxs-lookup"><span data-stu-id="1c393-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="1c393-275">Salve suas alterações e selecione o link **Privacidade**.</span><span class="sxs-lookup"><span data-stu-id="1c393-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="1c393-276">Observe como o título na guia do navegador agora exibe **Política de Privacidade – Aplicativo de filme**, em vez de **Política de Privacidade – Filme MVC**:</span><span class="sxs-lookup"><span data-stu-id="1c393-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Guia Privacidade](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="1c393-278">Selecione o link **Página Inicial** e observe que o texto do título e de âncora também exibem **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="1c393-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="1c393-279">Conseguimos fazer a alteração uma vez no modelo de layout e fazer com que todas as páginas no site refletissem o novo texto do link e o novo título.</span><span class="sxs-lookup"><span data-stu-id="1c393-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="1c393-280">Examine o arquivo *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1c393-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="1c393-281">O arquivo *Views/_ViewStart.cshtml* mostra o arquivo *Views/Shared/_Layout.cshtml* em cada exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="1c393-282">A propriedade `Layout` pode ser usada para definir outra exibição de layout ou defina-a como `null` para que nenhum arquivo de layout seja usado.</span><span class="sxs-lookup"><span data-stu-id="1c393-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="1c393-283">Altere o título e o elemento `<h2>` do arquivo de exibição *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1c393-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="1c393-284">O título e o elemento `<h2>` são ligeiramente diferentes para que possa ver qual parte do código altera a exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="1c393-285">`ViewData["Title"] = "Movie List";` no código acima define a propriedade `Title` do dicionário `ViewData` como “Lista de Filmes”.</span><span class="sxs-lookup"><span data-stu-id="1c393-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="1c393-286">A propriedade `Title` é usada no elemento HTML `<title>` na página de layout:</span><span class="sxs-lookup"><span data-stu-id="1c393-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="1c393-287">Salve as alterações e navegue para `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="1c393-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="1c393-288">Observe que o título do navegador, o cabeçalho primário e os títulos secundários foram alterados.</span><span class="sxs-lookup"><span data-stu-id="1c393-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="1c393-289">(Se as alterações não forem exibidas no navegador, talvez o conteúdo armazenado em cache esteja sendo exibido.</span><span class="sxs-lookup"><span data-stu-id="1c393-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="1c393-290">Pressione CTRL + F5 em seu navegador para forçar a resposta do servidor a ser carregado.) O título do navegador é criado com `ViewData["Title"]` que definimos no modelo de exibição *index. cshtml* e o "aplicativo de filme" adicional adicionado ao arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="1c393-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="1c393-291">Observe também como o conteúdo no modelo de exibição *Index.cshtml* foi mesclado com o modelo de exibição *Views/Shared/_Layout.cshtml* e uma única resposta HTML foi enviada para o navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="1c393-292">Os modelos de layout facilitam realmente a realização de alterações que se aplicam a todas as páginas do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1c393-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="1c393-293">Para saber mais, consulte [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="1c393-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Exibição de Lista de Filmes](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="1c393-295">Apesar disso, nossos poucos “dados” (nesse caso, a mensagem “Olá de nosso modelo de exibição!”)</span><span class="sxs-lookup"><span data-stu-id="1c393-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="1c393-296">são embutidos em código.</span><span class="sxs-lookup"><span data-stu-id="1c393-296">message) is hard-coded, though.</span></span> <span data-ttu-id="1c393-297">O aplicativo MVC tem um “V” (exibição) e você tem um “C” (controlador), mas ainda nenhum “M” (modelo).</span><span class="sxs-lookup"><span data-stu-id="1c393-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="1c393-298">Passando dados do controlador para a exibição</span><span class="sxs-lookup"><span data-stu-id="1c393-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="1c393-299">As ações do controlador são invocadas em resposta a uma solicitação de URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="1c393-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="1c393-300">Uma classe de controlador é o local em que o código é escrito e que manipula as solicitações recebidas do navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="1c393-301">O controlador recupera dados de uma fonte de dados e decide qual tipo de resposta será enviada novamente para o navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="1c393-302">Modelos de exibição podem ser usados em um controlador para gerar e formatar uma resposta HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="1c393-303">Os controladores são responsáveis por fornecer os dados necessários para que um modelo de exibição renderize uma resposta.</span><span class="sxs-lookup"><span data-stu-id="1c393-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="1c393-304">Uma melhor prática: modelos de exibição **não** devem executar a lógica de negócios nem interagir diretamente com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1c393-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="1c393-305">Em vez disso, um modelo de exibição deve funcionar somente com os dados fornecidos pelo controlador.</span><span class="sxs-lookup"><span data-stu-id="1c393-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="1c393-306">Manter essa “separação de preocupações” ajuda a manter o código limpo, testável e com capacidade de manutenção.</span><span class="sxs-lookup"><span data-stu-id="1c393-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="1c393-307">Atualmente, o método `Welcome` na classe `HelloWorldController` usa um parâmetro `name` e um `ID` e, em seguida, gera os valores diretamente no navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="1c393-308">Em vez de fazer com que o controlador renderize a resposta como uma cadeia de caracteres, altere o controlador para que ele use um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="1c393-309">O modelo de exibição gera uma resposta dinâmica, o que significa que é necessário passar bits de dados apropriados do controlador para a exibição para gerar a resposta.</span><span class="sxs-lookup"><span data-stu-id="1c393-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="1c393-310">Faça isso fazendo com que o controlador coloque os dados dinâmicos (parâmetros) que o modelo de exibição precisa em um dicionário `ViewData` que pode ser acessado em seguida pelo modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="1c393-311">Em *HelloWorldController.cs*, altere o método `Welcome` para adicionar um valor `Message` e `NumTimes` ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="1c393-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="1c393-312">O dicionário `ViewData` é um objeto dinâmico, o que significa que qualquer tipo pode ser usado. O objeto `ViewData` não tem nenhuma propriedade definida até que você insira algo nele.</span><span class="sxs-lookup"><span data-stu-id="1c393-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="1c393-313">O sistema de [model binding](xref:mvc/models/model-binding) MVC mapeia automaticamente os parâmetros nomeados (`name` e `numTimes`) da cadeia de consulta na barra de endereços para os parâmetros no método.</span><span class="sxs-lookup"><span data-stu-id="1c393-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="1c393-314">O arquivo *HelloWorldController.cs* completo tem esta aparência:</span><span class="sxs-lookup"><span data-stu-id="1c393-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="1c393-315">O objeto de dicionário `ViewData` contém dados que serão passados para a exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="1c393-316">Crie um modelo de exibição Boas-vindas chamado *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1c393-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="1c393-317">Você criará um loop no modelo de exibição *Welcome.cshtml* que exibe “Olá” `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="1c393-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="1c393-318">Substitua o conteúdo de *Views/HelloWorld/Welcome.cshtml* pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="1c393-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="1c393-319">Salve as alterações e navegue para a seguinte URL:</span><span class="sxs-lookup"><span data-stu-id="1c393-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="1c393-320">Os dados são obtidos da URL e passados para o controlador usando o [associador de modelo MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1c393-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="1c393-321">O controlador empacota os dados em um dicionário `ViewData` e passa esse objeto para a exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="1c393-322">Em seguida, a exibição renderiza os dados como HTML para o navegador.</span><span class="sxs-lookup"><span data-stu-id="1c393-322">The view then renders the data as HTML to the browser.</span></span>

![Exibição de privacidade que mostra um rótulo Boas-vindas e a frase Olá, Ricardo mostrada quatro vezes](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="1c393-324">No exemplo acima, o dicionário `ViewData` foi usado para passar dados do controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="1c393-325">Mais adiante no tutorial, um modelo de exibição será usado para passar dados de um controlador para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="1c393-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="1c393-326">A abordagem de modelo de exibição para passar dados é geralmente a preferida em relação à abordagem do dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="1c393-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="1c393-327">Confira mais informações em [Quando usar ViewBag, ViewData ou TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="1c393-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="1c393-328">No próximo tutorial, será criado um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="1c393-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1c393-329">[Anterior](adding-controller.md)
> [Próximo](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="1c393-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
