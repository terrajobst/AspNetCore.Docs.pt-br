---
title: Exibições no ASP.NET Core MVC
author: ardalis
description: Saiba como as exibições tratam da apresentação de dados do aplicativo e da interação com o usuário no ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/overview
ms.openlocfilehash: f636908ee36d0af6e92875876240cb8712dd2ccc
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881032"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="8a668-103">Exibições no ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8a668-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="8a668-104">Por [Steve Smith](https://ardalis.com/) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8a668-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8a668-105">Este documento explica as exibições usadas em aplicativos do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8a668-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="8a668-106">Para obter informações sobre páginas do Razor, consulte [Introdução a Páginas do Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="8a668-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="8a668-107">No padrão MVC (Modelo-Exibição-Controlador), a *exibição* cuida da apresentação de dados do aplicativo e da interação com o usuário.</span><span class="sxs-lookup"><span data-stu-id="8a668-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="8a668-108">Uma exibição é um modelo HTML com [marcação Razor](xref:mvc/views/razor) inserida.</span><span class="sxs-lookup"><span data-stu-id="8a668-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="8a668-109">A marcação Razor é um código que interage com a marcação HTML para produzir uma página da Web que é enviada ao cliente.</span><span class="sxs-lookup"><span data-stu-id="8a668-109">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="8a668-110">No ASP.NET Core MVC, as exibições são arquivos *.cshtml* que usam a [linguagem de programação C#](/dotnet/csharp/) na marcação Razor.</span><span class="sxs-lookup"><span data-stu-id="8a668-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="8a668-111">Geralmente, arquivos de exibição são agrupados em pastas nomeadas para cada um dos [controladores](xref:mvc/controllers/actions) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a668-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="8a668-112">As pastas são armazenadas em uma pasta chamada *Views* na raiz do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8a668-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![A pasta Views no Gerenciador de Soluções do Visual Studio é aberta com a pasta Inicial aberta para mostrar os arquivos About.cshtml, Contact.cshtml e Index.cshtml](overview/_static/views_solution_explorer.png)

<span data-ttu-id="8a668-114">O controlador *Home* é representado por uma pasta *Home* dentro da pasta *Views*.</span><span class="sxs-lookup"><span data-stu-id="8a668-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="8a668-115">A pasta *Home* contém as exibições das páginas da Web *About*, *Contact* e *Index* (home page).</span><span class="sxs-lookup"><span data-stu-id="8a668-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="8a668-116">Quando um usuário solicita uma dessas três páginas da Web, ações do controlador *Home* determinam qual das três exibições é usada para compilar e retornar uma página da Web para o usuário.</span><span class="sxs-lookup"><span data-stu-id="8a668-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="8a668-117">Use [layouts](xref:mvc/views/layout) para fornecer seções de páginas da Web consistentes e reduzir repetições de código.</span><span class="sxs-lookup"><span data-stu-id="8a668-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="8a668-118">Layouts geralmente contêm o cabeçalho, elementos de navegação e menu e o rodapé.</span><span class="sxs-lookup"><span data-stu-id="8a668-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="8a668-119">O cabeçalho e o rodapé geralmente contêm marcações repetitivas para muitos elementos de metadados, bem como links para ativos de script e estilo.</span><span class="sxs-lookup"><span data-stu-id="8a668-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="8a668-120">Layouts ajudam a evitar essa marcação repetitiva em suas exibições.</span><span class="sxs-lookup"><span data-stu-id="8a668-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="8a668-121">[Exibições parciais](xref:mvc/views/partial) reduzem a duplicação de código gerenciando as partes reutilizáveis das exibições.</span><span class="sxs-lookup"><span data-stu-id="8a668-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="8a668-122">Por exemplo, uma exibição parcial é útil para uma biografia do autor que aparece em várias exibições em um site de blog.</span><span class="sxs-lookup"><span data-stu-id="8a668-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="8a668-123">Uma biografia do autor é um conteúdo de exibição comum e não requer que um código seja executado para produzi-lo para a página da Web.</span><span class="sxs-lookup"><span data-stu-id="8a668-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="8a668-124">O conteúdo da biografia do autor é disponibilizado para a exibição usando somente o model binding, de modo que usar uma exibição parcial para esse tipo de conteúdo é ideal.</span><span class="sxs-lookup"><span data-stu-id="8a668-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="8a668-125">[Componentes de exibição](xref:mvc/views/view-components) são semelhantes a exibições parciais no sentido em que permitem reduzir códigos repetitivos, mas são adequados para conteúdos de exibição que requerem que um código seja executado no servidor para renderizar a página da Web.</span><span class="sxs-lookup"><span data-stu-id="8a668-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="8a668-126">Componentes de exibição são úteis quando o conteúdo renderizado requer uma interação com o banco de dados, como para o carrinho de compras de um site.</span><span class="sxs-lookup"><span data-stu-id="8a668-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="8a668-127">Os componentes de exibição não ficam limitados ao model binding para produzir a saída da página da Web.</span><span class="sxs-lookup"><span data-stu-id="8a668-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="8a668-128">Benefícios do uso de exibições</span><span class="sxs-lookup"><span data-stu-id="8a668-128">Benefits of using views</span></span>

<span data-ttu-id="8a668-129">As exibições ajudam a estabelecer uma [separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) dentro de um aplicativo MVC, separando a marcação da interface do usuário de outras partes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a668-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="8a668-130">Seguir um design de SoC faz com que seu aplicativo seja modular, o que fornece vários benefícios:</span><span class="sxs-lookup"><span data-stu-id="8a668-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="8a668-131">A manutenção do aplicativo é mais fácil, porque ele é melhor organizado.</span><span class="sxs-lookup"><span data-stu-id="8a668-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="8a668-132">Geralmente, as exibições são agrupadas segundo os recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a668-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="8a668-133">Isso facilita encontrar exibições relacionadas ao trabalhar em um recurso.</span><span class="sxs-lookup"><span data-stu-id="8a668-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="8a668-134">As partes do aplicativo ficam acopladas de forma flexível.</span><span class="sxs-lookup"><span data-stu-id="8a668-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="8a668-135">Você pode compilar e atualizar as exibições do aplicativo separadamente da lógica de negócios e dos componentes de acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="8a668-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="8a668-136">É possível modificar os modos de exibição do aplicativo sem precisar necessariamente atualizar outras partes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a668-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="8a668-137">É mais fácil testar as partes da interface do usuário do aplicativo porque as exibições são unidades separadas.</span><span class="sxs-lookup"><span data-stu-id="8a668-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="8a668-138">Devido à melhor organização, é menos provável que você repita acidentalmente seções da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="8a668-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="8a668-139">Criando uma exibição</span><span class="sxs-lookup"><span data-stu-id="8a668-139">Creating a view</span></span>

<span data-ttu-id="8a668-140">Exibições que são específicas de um controlador são criadas na pasta *Views/ [NomeDoControlador]* .</span><span class="sxs-lookup"><span data-stu-id="8a668-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="8a668-141">Exibições que são compartilhadas entre controladores são colocadas na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="8a668-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="8a668-142">Para criar uma exibição, adicione um novo arquivo e dê a ele o mesmo nome que o da ação de seu controlador associado, com a extensão de arquivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8a668-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="8a668-143">Para criar uma exibição correspondente à ação *About* no controlador *Home*, crie um arquivo *About.cshtml* na pasta *Views/Home*:</span><span class="sxs-lookup"><span data-stu-id="8a668-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="8a668-144">A marcação *Razor* começa com o símbolo `@`.</span><span class="sxs-lookup"><span data-stu-id="8a668-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="8a668-145">Execute instruções em C# colocando o código C# dentro de [blocos de código Razor](xref:mvc/views/razor#razor-code-blocks) entre chaves (`{ ... }`).</span><span class="sxs-lookup"><span data-stu-id="8a668-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="8a668-146">Por exemplo, consulte a atribuição de "About" para `ViewData["Title"]` mostrado acima.</span><span class="sxs-lookup"><span data-stu-id="8a668-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="8a668-147">É possível exibir valores em HTML simplesmente referenciando o valor com o símbolo `@`.</span><span class="sxs-lookup"><span data-stu-id="8a668-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="8a668-148">Veja o conteúdo dos elementos `<h2>` e `<h3>` acima.</span><span class="sxs-lookup"><span data-stu-id="8a668-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="8a668-149">O conteúdo da exibição mostrado acima é apenas uma parte da página da Web inteira que é renderizada para o usuário.</span><span class="sxs-lookup"><span data-stu-id="8a668-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="8a668-150">O restante do layout da página e outros aspectos comuns da exibição são especificados em outros arquivos de exibição.</span><span class="sxs-lookup"><span data-stu-id="8a668-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="8a668-151">Para saber mais, consulte o [tópico sobre Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="8a668-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="8a668-152">Como controladores especificam exibições</span><span class="sxs-lookup"><span data-stu-id="8a668-152">How controllers specify views</span></span>

<span data-ttu-id="8a668-153">Normalmente, as exibições são retornadas de ações como um [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), que é um tipo de [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span><span class="sxs-lookup"><span data-stu-id="8a668-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="8a668-154">O método de ação pode criar e retornar um `ViewResult` diretamente, mas normalmente isso não é feito.</span><span class="sxs-lookup"><span data-stu-id="8a668-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="8a668-155">Como a maioria dos controladores herdam de [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), basta usar o método auxiliar `View` para retornar o `ViewResult`:</span><span class="sxs-lookup"><span data-stu-id="8a668-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="8a668-156">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="8a668-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="8a668-157">Quando essa ação é retornada, a exibição *About.cshtml* mostrada na seção anterior é renderizada como a seguinte página da Web:</span><span class="sxs-lookup"><span data-stu-id="8a668-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![Página About renderizada no navegador Microsoft Edge](overview/_static/about-page.png)

<span data-ttu-id="8a668-159">O método auxiliar `View` tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="8a668-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="8a668-160">Opcionalmente, você pode especificar:</span><span class="sxs-lookup"><span data-stu-id="8a668-160">You can optionally specify:</span></span>

* <span data-ttu-id="8a668-161">Uma exibição explícita a ser retornada:</span><span class="sxs-lookup"><span data-stu-id="8a668-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="8a668-162">Um [modelo](xref:mvc/models/model-binding) a ser passado para a exibição:</span><span class="sxs-lookup"><span data-stu-id="8a668-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="8a668-163">Um modo de exibição e um modelo:</span><span class="sxs-lookup"><span data-stu-id="8a668-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="8a668-164">Descoberta de exibição</span><span class="sxs-lookup"><span data-stu-id="8a668-164">View discovery</span></span>

<span data-ttu-id="8a668-165">Quando uma ação retorna uma exibição, um processo chamado *descoberta de exibição* ocorre.</span><span class="sxs-lookup"><span data-stu-id="8a668-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="8a668-166">Esse processo determina qual arquivo de exibição é usado com base no nome da exibição.</span><span class="sxs-lookup"><span data-stu-id="8a668-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="8a668-167">O comportamento padrão do método `View` (`return View();`) é retornar uma exibição com o mesmo nome que o método de ação do qual ela é chamada.</span><span class="sxs-lookup"><span data-stu-id="8a668-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="8a668-168">Por exemplo, o nome do método `ActionResult` de *About* do controlador é usado para pesquisar um arquivo de exibição chamado *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8a668-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="8a668-169">Primeiro, o tempo de execução pesquisa pela exibição na pasta *Views/[NomeDoControlador]* .</span><span class="sxs-lookup"><span data-stu-id="8a668-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="8a668-170">Se não encontrar uma exibição correspondente nela, ele procura pela exibição na pasta *Shared*.</span><span class="sxs-lookup"><span data-stu-id="8a668-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="8a668-171">Não importa se você retornar implicitamente o `ViewResult` com `return View();` ou se passar explicitamente o nome de exibição para o método `View` com `return View("<ViewName>");`.</span><span class="sxs-lookup"><span data-stu-id="8a668-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="8a668-172">Nos dois casos, a descoberta de exibição pesquisa por um arquivo de exibição correspondente nesta ordem:</span><span class="sxs-lookup"><span data-stu-id="8a668-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="8a668-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="8a668-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="8a668-174">*Views/Shared/\[NomeDaExibição].cshtml*</span><span class="sxs-lookup"><span data-stu-id="8a668-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="8a668-175">Um caminho de arquivo de exibição pode ser fornecido em vez de um nome de exibição.</span><span class="sxs-lookup"><span data-stu-id="8a668-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="8a668-176">Se um caminho absoluto que começa na raiz do aplicativo (ou é iniciado por "/" ou "~ /") estiver sendo usado, a extensão *.cshtml* deverá ser especificada:</span><span class="sxs-lookup"><span data-stu-id="8a668-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="8a668-177">Você também pode usar um caminho relativo para especificar exibições em diretórios diferentes sem a extensão *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8a668-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="8a668-178">Dentro do `HomeController`, você pode retornar a exibição *Index* de suas exibições *Manage* com um caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="8a668-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="8a668-179">De forma semelhante, você pode indicar o atual diretório específico do controlador com o prefixo ". /":</span><span class="sxs-lookup"><span data-stu-id="8a668-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="8a668-180">[Exibições parciais](xref:mvc/views/partial) e [componentes de exibição](xref:mvc/views/view-components) usam mecanismos de descoberta semelhantes (mas não idênticos).</span><span class="sxs-lookup"><span data-stu-id="8a668-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="8a668-181">É possível personalizar a convenção padrão de como as exibições ficam localizadas dentro do aplicativo usando um [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander) personalizado.</span><span class="sxs-lookup"><span data-stu-id="8a668-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="8a668-182">A descoberta de exibição depende da localização de arquivos de exibição pelo nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a668-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="8a668-183">Se o sistema de arquivos subjacente diferenciar maiúsculas de minúsculas, os nomes de exibição provavelmente diferenciarão maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="8a668-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="8a668-184">Para fins de compatibilidade de sistemas operacionais, padronize as maiúsculas e minúsculas dos nomes de controladores e de ações e dos nomes de arquivos e pastas de exibição.</span><span class="sxs-lookup"><span data-stu-id="8a668-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="8a668-185">Se encontrar um erro indicando que não é possível encontrar um arquivo de exibição ao trabalhar com um sistema de arquivos que diferencia maiúsculas de minúsculas, confirme que o uso de maiúsculas e minúsculas é correspondente entre o arquivo de exibição solicitado e o nome do arquivo de exibição real.</span><span class="sxs-lookup"><span data-stu-id="8a668-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="8a668-186">Siga a melhor prática de organizar a estrutura de arquivos de suas exibições de forma a refletir as relações entre controladores, ações e exibições para facilidade de manutenção e clareza.</span><span class="sxs-lookup"><span data-stu-id="8a668-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="8a668-187">Passando dados para exibições</span><span class="sxs-lookup"><span data-stu-id="8a668-187">Passing data to views</span></span>

<span data-ttu-id="8a668-188">Passe dados para exibições usando várias abordagens:</span><span class="sxs-lookup"><span data-stu-id="8a668-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="8a668-189">Dados fortemente tipados: viewmodel</span><span class="sxs-lookup"><span data-stu-id="8a668-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="8a668-190">Dados fracamente tipados</span><span class="sxs-lookup"><span data-stu-id="8a668-190">Weakly typed data</span></span>
  * <span data-ttu-id="8a668-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="8a668-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="8a668-192">Dados fortemente tipados (viewmodel)</span><span class="sxs-lookup"><span data-stu-id="8a668-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="8a668-193">A abordagem mais robusta é especificar um tipo de [modelo](xref:mvc/models/model-binding) na exibição.</span><span class="sxs-lookup"><span data-stu-id="8a668-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="8a668-194">Esse modelo é conhecido como *viewmodel*.</span><span class="sxs-lookup"><span data-stu-id="8a668-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="8a668-195">Você passa uma instância do tipo viewmodel para a exibição da ação.</span><span class="sxs-lookup"><span data-stu-id="8a668-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="8a668-196">Usar um viewmodel para passar dados para uma exibição permite que a exibição tire proveito da verificação de tipo *forte*.</span><span class="sxs-lookup"><span data-stu-id="8a668-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="8a668-197">*Tipagem forte* (ou *fortemente tipado*) significa que cada variável e constante têm um tipo definido explicitamente (por exemplo, `string`, `int` ou `DateTime`).</span><span class="sxs-lookup"><span data-stu-id="8a668-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="8a668-198">A validade dos tipos usados em uma exibição é verificada em tempo de compilação.</span><span class="sxs-lookup"><span data-stu-id="8a668-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="8a668-199">O [Visual Studio](https://visualstudio.microsoft.com) e o [Visual Studio Code](https://code.visualstudio.com/) listam membros de classe fortemente tipados usando um recurso chamado [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="8a668-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="8a668-200">Quando quiser ver as propriedades de um viewmodel, digite o nome da variável para o viewmodel, seguido por um ponto final (`.`).</span><span class="sxs-lookup"><span data-stu-id="8a668-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="8a668-201">Isso ajuda você a escrever código mais rapidamente e com menos erros.</span><span class="sxs-lookup"><span data-stu-id="8a668-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="8a668-202">Especifique um modelo usando a diretiva `@model`.</span><span class="sxs-lookup"><span data-stu-id="8a668-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="8a668-203">Use o modelo com `@Model`:</span><span class="sxs-lookup"><span data-stu-id="8a668-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="8a668-204">Para fornecer o modelo à exibição, o controlador o passa como um parâmetro:</span><span class="sxs-lookup"><span data-stu-id="8a668-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

<span data-ttu-id="8a668-205">Não há restrições quanto aos tipos de modelo que você pode fornecer a uma exibição.</span><span class="sxs-lookup"><span data-stu-id="8a668-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="8a668-206">Recomendamos o uso de viewmodels do tipo POCO (objeto CRL básico) com pouco ou nenhum comportamento (métodos) definido.</span><span class="sxs-lookup"><span data-stu-id="8a668-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="8a668-207">Geralmente, classes de viewmodel são armazenadas na pasta *Models* ou em uma pasta *ViewModels* separada na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a668-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="8a668-208">O viewmodel *Address* usado no exemplo acima é um viewmodel POCO armazenado em um arquivo chamado *Address.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a668-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

<span data-ttu-id="8a668-209">Nada impede que você use as mesmas classes para seus tipos de viewmodel e seus tipos de modelo de negócios.</span><span class="sxs-lookup"><span data-stu-id="8a668-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="8a668-210">No entanto, o uso de modelos separados permite que suas exibições variem independentemente das partes de lógica de negócios e de acesso a dados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a668-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="8a668-211">A separação de modelos e viewmodels também oferece benefícios de segurança quando os modelos usam [model binding](xref:mvc/models/model-binding) e [validação](xref:mvc/models/validation) para dados enviados ao aplicativo pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="8a668-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="8a668-212">Dados fracamente tipados (ViewData, atributo ViewData e ViewBag)</span><span class="sxs-lookup"><span data-stu-id="8a668-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="8a668-213">`ViewBag` *não está disponível nas Páginas do Razor.*</span><span class="sxs-lookup"><span data-stu-id="8a668-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="8a668-214">Além de exibições fortemente tipadas, as exibições têm acesso a uma coleção de dados *fracamente tipados* (também chamada de *tipagem flexível*).</span><span class="sxs-lookup"><span data-stu-id="8a668-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="8a668-215">Diferente dos tipos fortes, ter *tipos fracos* (ou *tipos flexíveis*) significa que você não declara explicitamente o tipo dos dados que está usando.</span><span class="sxs-lookup"><span data-stu-id="8a668-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="8a668-216">Você pode usar a coleção de dados fracamente tipados para transmitir pequenas quantidades de dados para dentro e para fora dos controladores e das exibições.</span><span class="sxs-lookup"><span data-stu-id="8a668-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="8a668-217">Passar dados entre...</span><span class="sxs-lookup"><span data-stu-id="8a668-217">Passing data between a ...</span></span>                        | <span data-ttu-id="8a668-218">Exemplo</span><span class="sxs-lookup"><span data-stu-id="8a668-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="8a668-219">Um controlador e uma exibição</span><span class="sxs-lookup"><span data-stu-id="8a668-219">Controller and a view</span></span>                             | <span data-ttu-id="8a668-220">Preencher uma lista suspensa com os dados.</span><span class="sxs-lookup"><span data-stu-id="8a668-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="8a668-221">Uma exibição e uma [exibição de layout](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="8a668-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="8a668-222">Definir o conteúdo do elemento **\<title>** na exibição de layout de um arquivo de exibição.</span><span class="sxs-lookup"><span data-stu-id="8a668-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="8a668-223">Uma [exibição parcial](xref:mvc/views/partial) e uma exibição</span><span class="sxs-lookup"><span data-stu-id="8a668-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="8a668-224">Um widget que exibe dados com base na página da Web que o usuário solicitou.</span><span class="sxs-lookup"><span data-stu-id="8a668-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="8a668-225">Essa coleção pode ser referenciada por meio das propriedades `ViewData` ou `ViewBag` em controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="8a668-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="8a668-226">A propriedade `ViewData` é um dicionário de objetos fracamente tipados.</span><span class="sxs-lookup"><span data-stu-id="8a668-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="8a668-227">A propriedade `ViewBag` é um wrapper em torno de `ViewData` que fornece propriedades dinâmicas à coleção de `ViewData` subjacente.</span><span class="sxs-lookup"><span data-stu-id="8a668-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="8a668-228">Observação: as pesquisas de chave não diferenciam maiúsculas de minúsculas para `ViewData` e `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="8a668-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="8a668-229">`ViewData` e `ViewBag` são resolvidos dinamicamente em runtime.</span><span class="sxs-lookup"><span data-stu-id="8a668-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="8a668-230">Uma vez que não oferecem verificação de tipo em tempo de compilação, geralmente ambos são mais propensos a erros do que quando um viewmodel é usado.</span><span class="sxs-lookup"><span data-stu-id="8a668-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="8a668-231">Por esse motivo, alguns desenvolvedores preferem nunca usar `ViewData` e `ViewBag` ou usá-los o mínimo possível.</span><span class="sxs-lookup"><span data-stu-id="8a668-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="8a668-232">**ViewData**</span><span class="sxs-lookup"><span data-stu-id="8a668-232">**ViewData**</span></span>

<span data-ttu-id="8a668-233">`ViewData` é um objeto [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) acessado por meio de chaves `string`.</span><span class="sxs-lookup"><span data-stu-id="8a668-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="8a668-234">Dados de cadeias de caracteres podem ser armazenados e usados diretamente, sem a necessidade de conversão, mas você precisa converter os valores de outros objetos `ViewData` em tipos específicos quando extraí-los.</span><span class="sxs-lookup"><span data-stu-id="8a668-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="8a668-235">Você pode usar `ViewData` para passar dados de controladores para exibições e dentro das exibições, incluindo [exibições parciais](xref:mvc/views/partial) e [layouts](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="8a668-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="8a668-236">A seguir, temos um exemplo que define valores para uma saudação e um endereço usando `ViewData` em uma ação:</span><span class="sxs-lookup"><span data-stu-id="8a668-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

<span data-ttu-id="8a668-237">Trabalhar com os dados em uma exibição:</span><span class="sxs-lookup"><span data-stu-id="8a668-237">Work with the data in a view:</span></span>

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8a668-238">**Atributo ViewData**</span><span class="sxs-lookup"><span data-stu-id="8a668-238">**ViewData attribute**</span></span>

<span data-ttu-id="8a668-239">Outra abordagem que usa o [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) é [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="8a668-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="8a668-240">As propriedades em controladores ou modelos de página Razor marcados com o atributo `[ViewData]` têm seus valores armazenados e carregados do dicionário.</span><span class="sxs-lookup"><span data-stu-id="8a668-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="8a668-241">No exemplo a seguir, o controlador inicial contém uma propriedade `Title` marcada com `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="8a668-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="8a668-242">O método `About` define o título para a exibição About:</span><span class="sxs-lookup"><span data-stu-id="8a668-242">The `About` method sets the title for the About view:</span></span>

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

<span data-ttu-id="8a668-243">Na exibição About, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="8a668-243">In the About view, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="8a668-244">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="8a668-244">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="8a668-245">**ViewBag**</span><span class="sxs-lookup"><span data-stu-id="8a668-245">**ViewBag**</span></span>

<span data-ttu-id="8a668-246">`ViewBag` *não está disponível nas Páginas do Razor.*</span><span class="sxs-lookup"><span data-stu-id="8a668-246">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="8a668-247">`ViewBag` é um objeto [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) que fornece acesso dinâmico aos objetos armazenados em `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="8a668-247">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="8a668-248">Pode ser mais conveniente trabalhar com `ViewBag`, pois ele não requer uma conversão.</span><span class="sxs-lookup"><span data-stu-id="8a668-248">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="8a668-249">O exemplo a seguir mostra como usar `ViewBag` com o mesmo resultado que o uso de `ViewData` acima:</span><span class="sxs-lookup"><span data-stu-id="8a668-249">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

<span data-ttu-id="8a668-250">**Usando ViewData e ViewBag simultaneamente**</span><span class="sxs-lookup"><span data-stu-id="8a668-250">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="8a668-251">`ViewBag` *não está disponível nas Páginas do Razor.*</span><span class="sxs-lookup"><span data-stu-id="8a668-251">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="8a668-252">Como `ViewData` e `ViewBag` fazem referência à mesma coleção `ViewData` subjacente, você pode usar `ViewData` e `ViewBag`, além de misturá-los e combiná-los ao ler e gravar valores.</span><span class="sxs-lookup"><span data-stu-id="8a668-252">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="8a668-253">Defina o título usando `ViewBag` e a descrição usando `ViewData` na parte superior de uma exibição *About.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8a668-253">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="8a668-254">Leia as propriedades, mas inverta o uso de `ViewData` e `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="8a668-254">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="8a668-255">No arquivo *_Layout.cshtml*, obtenha o título usando `ViewData` e a descrição usando `ViewBag`:</span><span class="sxs-lookup"><span data-stu-id="8a668-255">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="8a668-256">Lembre-se de que cadeias de caracteres não exigem uma conversão para `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="8a668-256">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="8a668-257">Você pode usar `@ViewData["Title"]` sem converter.</span><span class="sxs-lookup"><span data-stu-id="8a668-257">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="8a668-258">Usar `ViewData` e `ViewBag` ao mesmo tempo funciona, assim como misturar e combinar e leitura e a gravação das propriedades.</span><span class="sxs-lookup"><span data-stu-id="8a668-258">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="8a668-259">A seguinte marcação é renderizada:</span><span class="sxs-lookup"><span data-stu-id="8a668-259">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="8a668-260">**Resumo das diferenças entre ViewData e ViewBag**</span><span class="sxs-lookup"><span data-stu-id="8a668-260">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="8a668-261">`ViewBag` não está disponível em páginas Razor.</span><span class="sxs-lookup"><span data-stu-id="8a668-261">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="8a668-262">Deriva de [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), de forma que tem propriedades de dicionário que podem ser úteis, como `ContainsKey`, `Add`, `Remove` e `Clear`.</span><span class="sxs-lookup"><span data-stu-id="8a668-262">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="8a668-263">Chaves no dicionário são cadeias de caracteres, de forma que espaços em branco são permitidos.</span><span class="sxs-lookup"><span data-stu-id="8a668-263">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="8a668-264">Exemplo: `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="8a668-264">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="8a668-265">Qualquer tipo diferente de `string` deve ser convertido na exibição para usar `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="8a668-265">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="8a668-266">Deriva de [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), de forma que permite a criação de propriedades dinâmicas usando a notação de ponto (`@ViewBag.SomeKey = <value or object>`) e nenhuma conversão é necessária.</span><span class="sxs-lookup"><span data-stu-id="8a668-266">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="8a668-267">A sintaxe de `ViewBag` torna mais rápido adicioná-lo a controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="8a668-267">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="8a668-268">Mais simples de verificar quanto à presença de valores nulos.</span><span class="sxs-lookup"><span data-stu-id="8a668-268">Simpler to check for null values.</span></span> <span data-ttu-id="8a668-269">Exemplo: `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="8a668-269">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="8a668-270">**Quando usar ViewData ou ViewBag**</span><span class="sxs-lookup"><span data-stu-id="8a668-270">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="8a668-271">`ViewData` e `ViewBag` são abordagens igualmente válidas para passar pequenas quantidades de dados entre controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="8a668-271">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="8a668-272">A escolha de qual delas usar é baseada na preferência.</span><span class="sxs-lookup"><span data-stu-id="8a668-272">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="8a668-273">Você pode misturar e combinar objetos `ViewData` e `ViewBag`, mas é mais fácil ler e manter o código quando uma abordagem é usada de maneira consistente.</span><span class="sxs-lookup"><span data-stu-id="8a668-273">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="8a668-274">Ambas as abordagens são resolvidas dinamicamente em runtime e, portanto, são propensas a causar erros de runtime.</span><span class="sxs-lookup"><span data-stu-id="8a668-274">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="8a668-275">Algumas equipes de desenvolvimento as evitam.</span><span class="sxs-lookup"><span data-stu-id="8a668-275">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="8a668-276">Exibições dinâmicas</span><span class="sxs-lookup"><span data-stu-id="8a668-276">Dynamic views</span></span>

<span data-ttu-id="8a668-277">Exibições que não declaram um tipo de modelo usando `@model`, mas que têm uma instância de modelo passada a elas (por exemplo, `return View(Address);`) podem referenciar as propriedades da instância dinamicamente:</span><span class="sxs-lookup"><span data-stu-id="8a668-277">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="8a668-278">Esse recurso oferece flexibilidade, mas não oferece proteção de compilação ou IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="8a668-278">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="8a668-279">Se a propriedade não existir, a geração da página da Web falhará em runtime.</span><span class="sxs-lookup"><span data-stu-id="8a668-279">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="8a668-280">Mais recursos das exibições</span><span class="sxs-lookup"><span data-stu-id="8a668-280">More view features</span></span>

<span data-ttu-id="8a668-281">[Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) facilitam a adição do comportamento do lado do servidor às marcações HTML existentes.</span><span class="sxs-lookup"><span data-stu-id="8a668-281">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="8a668-282">O uso de Auxiliares de marca evita a necessidade de escrever código personalizado ou auxiliares em suas exibições.</span><span class="sxs-lookup"><span data-stu-id="8a668-282">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="8a668-283">Auxiliares de marca são aplicados como atributos a elementos HTML e são ignorados por editores que não podem processá-los.</span><span class="sxs-lookup"><span data-stu-id="8a668-283">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="8a668-284">Isso permite editar e renderizar a marcação da exibição em várias ferramentas.</span><span class="sxs-lookup"><span data-stu-id="8a668-284">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="8a668-285">É possível gerar uma marcação HTML personalizada com muitos auxiliares HTML internos.</span><span class="sxs-lookup"><span data-stu-id="8a668-285">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="8a668-286">Uma lógica de interface do usuário mais complexa pode ser tratada por [Componentes de exibição](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="8a668-286">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="8a668-287">Componentes de exibição fornecem o mesmo SoC oferecido por controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="8a668-287">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="8a668-288">Eles podem eliminar a necessidade de ações e exibições que lidam com os dados usados por elementos comuns da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="8a668-288">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="8a668-289">Como muitos outros aspectos do ASP.NET Core, as exibições dão suporte à [injeção de dependência](xref:fundamentals/dependency-injection), permitindo que serviços sejam [injetados em exibições](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8a668-289">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
