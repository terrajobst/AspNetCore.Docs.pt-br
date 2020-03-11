---
title: Introdução a Páginas do Razor no ASP.NET Core
author: Rick-Anderson
description: Saiba como as Páginas Razor no ASP.NET Core tornam a codificação de cenários centrados em página mais fácil e mais produtiva do que com o uso de MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
uid: razor-pages/index
ms.openlocfilehash: 42ffb0d4d2e49663dd53ffeee5d9fa2a931ee5b7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667576"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="15396-103">Introdução a Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="15396-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="15396-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="15396-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="15396-105">Razor Pages pode tornar os cenários voltados para a página de codificação mais fáceis e produtivos do que usar controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="15396-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="15396-106">Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="15396-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="15396-107">Este documento proporciona uma introdução a páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="15396-108">Este não é um tutorial passo a passo.</span><span class="sxs-lookup"><span data-stu-id="15396-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="15396-109">Se você achar que algumas das seções são muito avançadas, consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="15396-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="15396-110">Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="15396-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="15396-111">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="15396-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="15396-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="15396-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="15396-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="15396-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="15396-114">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="15396-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="15396-115">Criar um projeto do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="15396-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="15396-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="15396-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="15396-117">Confira a [Introdução ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="15396-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="15396-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="15396-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="15396-119">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="15396-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="15396-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="15396-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="15396-121">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="15396-121">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="15396-122">Abra o arquivo *.csproj* gerado do Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="15396-122">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="15396-123">Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="15396-123">Razor Pages</span></span>

<span data-ttu-id="15396-124">O Páginas do Razor está habilitado em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="15396-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="15396-125">Considere uma página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="15396-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="15396-126">O código anterior se assemelha muito a um [arquivo de exibição do Razor](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="15396-126">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="15396-127">O que o torna diferente é a diretiva [`@page`](xref:mvc/views/razor#page) .</span><span class="sxs-lookup"><span data-stu-id="15396-127">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="15396-128">`@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador.</span><span class="sxs-lookup"><span data-stu-id="15396-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="15396-129">`@page` deve ser a primeira diretiva do Razor em uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="15396-130">`@page` afeta o comportamento de outras construções do [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="15396-130">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="15396-131">Razor Pages nomes de arquivo têm um sufixo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="15396-131">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="15396-132">Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir.</span><span class="sxs-lookup"><span data-stu-id="15396-132">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="15396-133">O arquivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-133">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="15396-134">O modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="15396-134">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="15396-135">Por convenção, o arquivo de classe `PageModel` tem o mesmo nome que o arquivo na Página do Razor com *.cs* acrescentado.</span><span class="sxs-lookup"><span data-stu-id="15396-135">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="15396-136">Por exemplo, a Página do Razor anterior é *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="15396-136">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="15396-137">O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="15396-137">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="15396-138">As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="15396-138">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="15396-139">A tabela a seguir mostra um caminho de Página do Razor e a URL correspondente:</span><span class="sxs-lookup"><span data-stu-id="15396-139">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="15396-140">Caminho e nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="15396-140">File name and path</span></span>               | <span data-ttu-id="15396-141">URL correspondente</span><span class="sxs-lookup"><span data-stu-id="15396-141">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="15396-142">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-142">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="15396-143">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="15396-143">`/` or `/Index`</span></span> |
| <span data-ttu-id="15396-144">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-144">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="15396-145">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-145">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="15396-146">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-146">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="15396-147">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="15396-147">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="15396-148">Observações:</span><span class="sxs-lookup"><span data-stu-id="15396-148">Notes:</span></span>

* <span data-ttu-id="15396-149">O runtime procura arquivos de Páginas do Razor na pasta *Pages* por padrão.</span><span class="sxs-lookup"><span data-stu-id="15396-149">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="15396-150">`Index` é a página padrão quando uma URL não inclui uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-150">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="15396-151">Escrever um formulário básico</span><span class="sxs-lookup"><span data-stu-id="15396-151">Write a basic form</span></span>

<span data-ttu-id="15396-152">Páginas do Razor foi projetado para facilitar a implementação de padrões comuns usados com navegadores da Web ao criar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="15396-152">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="15396-153">[Model binding](xref:mvc/models/model-binding), [auxiliares de marcas](xref:mvc/views/tag-helpers/intro) e auxiliares HTML *funcionam todos apenas* com as propriedades definidas em uma classe de Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-153">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="15396-154">Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="15396-154">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="15396-155">Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="15396-155">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="15396-156">O modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="15396-156">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="15396-157">O contexto do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="15396-157">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="15396-158">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="15396-159">O modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="15396-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="15396-160">Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.</span><span class="sxs-lookup"><span data-stu-id="15396-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="15396-161">A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação.</span><span class="sxs-lookup"><span data-stu-id="15396-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="15396-162">Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página.</span><span class="sxs-lookup"><span data-stu-id="15396-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="15396-163">Essa separação permite:</span><span class="sxs-lookup"><span data-stu-id="15396-163">This separation allows:</span></span>

* <span data-ttu-id="15396-164">Gerenciamento de dependências de página por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="15396-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="15396-165">Testes de unidade</span><span class="sxs-lookup"><span data-stu-id="15396-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="15396-166">A página tem um *método de manipulador*de `OnPostAsync`, que é executado em `POST` solicitações (quando um usuário posta o formulário).</span><span class="sxs-lookup"><span data-stu-id="15396-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="15396-167">Métodos de manipulador para qualquer verbo HTTP podem ser adicionados.</span><span class="sxs-lookup"><span data-stu-id="15396-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="15396-168">Os manipuladores mais comuns são:</span><span class="sxs-lookup"><span data-stu-id="15396-168">The most common handlers are:</span></span>

* <span data-ttu-id="15396-169">`OnGet` para inicializar o estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="15396-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="15396-170">No código anterior, o método `OnGet` exibe a página Razor *CREATEMODEL. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="15396-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="15396-171">`OnPost` para manipular envios de formulário.</span><span class="sxs-lookup"><span data-stu-id="15396-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="15396-172">O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas.</span><span class="sxs-lookup"><span data-stu-id="15396-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="15396-173">O código anterior é comum para as Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="15396-174">Se você estiver familiarizado com os aplicativos ASP.NET usando os controladores e exibições:</span><span class="sxs-lookup"><span data-stu-id="15396-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="15396-175">O código de `OnPostAsync` no exemplo anterior é semelhante ao código de controlador típico.</span><span class="sxs-lookup"><span data-stu-id="15396-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="15396-176">A maioria dos primitivos MVC, como [Associação de modelo](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation)e resultados de ação, funcionam da mesma forma com controladores e Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="15396-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="15396-177">O método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="15396-177">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="15396-178">O fluxo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="15396-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="15396-179">Verifique se há erros de validação.</span><span class="sxs-lookup"><span data-stu-id="15396-179">Check for validation errors.</span></span>

* <span data-ttu-id="15396-180">Se não houver nenhum erro, salve os dados e redirecione.</span><span class="sxs-lookup"><span data-stu-id="15396-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="15396-181">Se houver erros, mostre a página novamente com as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="15396-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="15396-182">Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.</span><span class="sxs-lookup"><span data-stu-id="15396-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="15396-183">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="15396-184">O HTML renderizado de *pages/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="15396-185">No código anterior, postando o formulário:</span><span class="sxs-lookup"><span data-stu-id="15396-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="15396-186">Com dados válidos:</span><span class="sxs-lookup"><span data-stu-id="15396-186">With valid data:</span></span>

  * <span data-ttu-id="15396-187">O método do manipulador de `OnPostAsync` chama o método auxiliar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>.</span><span class="sxs-lookup"><span data-stu-id="15396-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="15396-188">`RedirectToPage` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="15396-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="15396-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="15396-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="15396-190">É um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="15396-190">Is an action result.</span></span>
    * <span data-ttu-id="15396-191">É semelhante a `RedirectToAction` ou `RedirectToRoute` (usado em controladores e exibições).</span><span class="sxs-lookup"><span data-stu-id="15396-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="15396-192">É personalizado para páginas.</span><span class="sxs-lookup"><span data-stu-id="15396-192">Is customized for pages.</span></span> <span data-ttu-id="15396-193">Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="15396-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="15396-194">`RedirectToPage` é descrito em detalhes na seção [Geração de URLs para páginas](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="15396-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="15396-195">Com erros de validação que são passados para o servidor:</span><span class="sxs-lookup"><span data-stu-id="15396-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="15396-196">O método do manipulador de `OnPostAsync` chama o método auxiliar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>.</span><span class="sxs-lookup"><span data-stu-id="15396-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="15396-197">`Page` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="15396-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="15396-198">Retornar `Page` é semelhante a como as ações em controladores retornam `View`.</span><span class="sxs-lookup"><span data-stu-id="15396-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="15396-199">`PageResult` é o tipo de retorno padrão para um método de manipulador.</span><span class="sxs-lookup"><span data-stu-id="15396-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="15396-200">Um método de manipulador que retorna `void` renderiza a página.</span><span class="sxs-lookup"><span data-stu-id="15396-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="15396-201">No exemplo anterior, a postagem do formulário sem nenhum resultado em [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) retorna false.</span><span class="sxs-lookup"><span data-stu-id="15396-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="15396-202">Neste exemplo, nenhum erro de validação é exibido no cliente.</span><span class="sxs-lookup"><span data-stu-id="15396-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="15396-203">A entrega do erro de validação será abordada posteriormente neste documento.</span><span class="sxs-lookup"><span data-stu-id="15396-203">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="15396-204">Com erros de validação detectados pela validação no lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="15396-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="15396-205">Os dados **não** são postados no servidor.</span><span class="sxs-lookup"><span data-stu-id="15396-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="15396-206">A validação no lado do cliente é explicada posteriormente neste documento.</span><span class="sxs-lookup"><span data-stu-id="15396-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="15396-207">A propriedade `Customer` usa o atributo [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) para aceitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="15396-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="15396-208">`[BindProperty]` **não** deve ser usado em modelos que contêm propriedades que não devem ser alteradas pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="15396-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="15396-209">Para obter mais informações, consulte [superpostando](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="15396-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="15396-210">O Razor Pages, por padrão, associa propriedades somente com verbos não `GET`.</span><span class="sxs-lookup"><span data-stu-id="15396-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="15396-211">A associação a Propriedades remove a necessidade de escrever código para converter dados HTTP no tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="15396-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="15396-212">A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.</span><span class="sxs-lookup"><span data-stu-id="15396-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="15396-213">Examinando o arquivo de exibição *páginas/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="15396-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="15396-214">No código anterior, o [auxiliar de marca de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` associa o elemento HTML `<input>` à expressão do modelo `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="15396-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="15396-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) torna os auxiliares de marca disponíveis.</span><span class="sxs-lookup"><span data-stu-id="15396-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="15396-216">Home page</span><span class="sxs-lookup"><span data-stu-id="15396-216">The home page</span></span>

<span data-ttu-id="15396-217">*Index. cshtml* é o Home Page:</span><span class="sxs-lookup"><span data-stu-id="15396-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="15396-218">A classe `PageModel` (*Index.cshtml.cs*) associada:</span><span class="sxs-lookup"><span data-stu-id="15396-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="15396-219">O arquivo *index. cshtml* contém a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="15396-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="15396-220">O [auxiliar de marca de ancoragem](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a /a>` usou o atributo `asp-route-{value}` para gerar um link para a página de edição.</span><span class="sxs-lookup"><span data-stu-id="15396-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="15396-221">O link contém dados de rota com a ID de contato.</span><span class="sxs-lookup"><span data-stu-id="15396-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="15396-222">Por exemplo, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="15396-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="15396-223">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="15396-224">O arquivo *index. cshtml* contém marcação para criar um botão de exclusão para cada contato de cliente:</span><span class="sxs-lookup"><span data-stu-id="15396-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="15396-225">O HTML renderizado:</span><span class="sxs-lookup"><span data-stu-id="15396-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="15396-226">Quando o botão de exclusão é renderizado em HTML, seu [formsaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) inclui parâmetros para:</span><span class="sxs-lookup"><span data-stu-id="15396-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="15396-227">A ID de contato do cliente, especificada pelo atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="15396-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="15396-228">O `handler`, especificado pelo atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="15396-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="15396-229">Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="15396-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="15396-230">Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="15396-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="15396-231">Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`.</span><span class="sxs-lookup"><span data-stu-id="15396-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="15396-232">Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado.</span><span class="sxs-lookup"><span data-stu-id="15396-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="15396-233">O método `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="15396-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="15396-234">Obtém o `id` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="15396-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="15396-235">Consulta o banco de dados para o contato de cliente com `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="15396-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="15396-236">Se o contato do cliente for encontrado, ele é removido e o banco de dados é atualizado.</span><span class="sxs-lookup"><span data-stu-id="15396-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="15396-237">Chama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> para redirecionar para a página de índice de raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="15396-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="15396-238">O arquivo Edit. cshtml</span><span class="sxs-lookup"><span data-stu-id="15396-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="15396-239">A primeira linha contém a diretiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="15396-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="15396-240">A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`.</span><span class="sxs-lookup"><span data-stu-id="15396-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="15396-241">Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o runtime retorna um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="15396-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="15396-242">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="15396-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="15396-243">O arquivo *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="15396-243">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="15396-244">Validação</span><span class="sxs-lookup"><span data-stu-id="15396-244">Validation</span></span>

<span data-ttu-id="15396-245">Regras de validação:</span><span class="sxs-lookup"><span data-stu-id="15396-245">Validation rules:</span></span>

* <span data-ttu-id="15396-246">São especificadas declarativamente na classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="15396-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="15396-247">São impostos em todos os lugares no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="15396-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="15396-248">O namespace <xref:System.ComponentModel.DataAnnotations> fornece um conjunto de atributos de validação internos que são aplicados declarativamente a uma classe ou propriedade.</span><span class="sxs-lookup"><span data-stu-id="15396-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="15396-249">Annotations também contém atributos de formatação como [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) que ajudam na formatação e não fornecem nenhuma validação.</span><span class="sxs-lookup"><span data-stu-id="15396-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="15396-250">Considere o modelo de `Customer`:</span><span class="sxs-lookup"><span data-stu-id="15396-250">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="15396-251">Usando o seguinte arquivo de exibição *Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="15396-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="15396-252">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="15396-252">The preceding code:</span></span>

* <span data-ttu-id="15396-253">Inclui scripts de validação jQuery e jQuery.</span><span class="sxs-lookup"><span data-stu-id="15396-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="15396-254">Usa os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) `<div />` e `<span />` para habilitar:</span><span class="sxs-lookup"><span data-stu-id="15396-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="15396-255">Validação no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="15396-255">Client-side validation.</span></span>
  * <span data-ttu-id="15396-256">Renderização de erro de validação.</span><span class="sxs-lookup"><span data-stu-id="15396-256">Validation error rendering.</span></span>

* <span data-ttu-id="15396-257">Gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="15396-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="15396-258">A postagem do valor de criar formulário sem um nome exibe a mensagem de erro "o campo nome é obrigatório".</span><span class="sxs-lookup"><span data-stu-id="15396-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="15396-259">no formulário.</span><span class="sxs-lookup"><span data-stu-id="15396-259">on the form.</span></span> <span data-ttu-id="15396-260">Se o JavaScript estiver habilitado no cliente, o navegador exibirá o erro sem postar no servidor.</span><span class="sxs-lookup"><span data-stu-id="15396-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="15396-261">O atributo `[StringLength(10)]` gera `data-val-length-max="10"` no HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="15396-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="15396-262">`data-val-length-max` impede que os navegadores insiram mais do que o comprimento máximo especificado.</span><span class="sxs-lookup"><span data-stu-id="15396-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="15396-263">Se uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) for usada para editar e reproduzir a postagem:</span><span class="sxs-lookup"><span data-stu-id="15396-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="15396-264">Com o nome maior que 10.</span><span class="sxs-lookup"><span data-stu-id="15396-264">With the name longer than 10.</span></span>
* <span data-ttu-id="15396-265">A mensagem de erro "o nome do campo deve ser uma cadeia de caracteres com um comprimento máximo de 10".</span><span class="sxs-lookup"><span data-stu-id="15396-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="15396-266">é retornado.</span><span class="sxs-lookup"><span data-stu-id="15396-266">is returned.</span></span>

<span data-ttu-id="15396-267">Considere o seguinte modelo de `Movie`:</span><span class="sxs-lookup"><span data-stu-id="15396-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="15396-268">Os atributos de validação especificam o comportamento a ser aplicado nas propriedades de modelo às quais eles são aplicados:</span><span class="sxs-lookup"><span data-stu-id="15396-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="15396-269">Os atributos `Required` e `MinimumLength` indicam que uma propriedade deve ter um valor, mas nada impede que um usuário insira espaço em branco para atender a essa validação.</span><span class="sxs-lookup"><span data-stu-id="15396-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="15396-270">O atributo `RegularExpression` é usado para limitar quais caracteres podem ser inseridos.</span><span class="sxs-lookup"><span data-stu-id="15396-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="15396-271">No código anterior, "Gênero":</span><span class="sxs-lookup"><span data-stu-id="15396-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="15396-272">Deve usar apenas letras.</span><span class="sxs-lookup"><span data-stu-id="15396-272">Must only use letters.</span></span>
  * <span data-ttu-id="15396-273">A primeira letra deve ser maiúscula.</span><span class="sxs-lookup"><span data-stu-id="15396-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="15396-274">Espaços em branco, números e caracteres especiais não são permitidos.</span><span class="sxs-lookup"><span data-stu-id="15396-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="15396-275">A "Classificação" `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="15396-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="15396-276">Exige que o primeiro caractere seja uma letra maiúscula.</span><span class="sxs-lookup"><span data-stu-id="15396-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="15396-277">Permite caracteres especiais e números em espaços subsequentes.</span><span class="sxs-lookup"><span data-stu-id="15396-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="15396-278">"PG-13" é válido para uma classificação, mas é um erro em "Gênero".</span><span class="sxs-lookup"><span data-stu-id="15396-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="15396-279">O atributo `Range` restringe um valor a um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="15396-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="15396-280">O atributo `StringLength` define o comprimento máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu comprimento mínimo.</span><span class="sxs-lookup"><span data-stu-id="15396-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="15396-281">Os tipos de valor (como `decimal`, `int`, `float`, `DateTime`) são inerentemente necessários e não precisam do atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="15396-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="15396-282">A página criar para o modelo de `Movie` mostra exibe erros com valores inválidos:</span><span class="sxs-lookup"><span data-stu-id="15396-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="15396-284">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="15396-284">For more information, see:</span></span>

* [<span data-ttu-id="15396-285">Adicionar validação ao aplicativo de filme</span><span class="sxs-lookup"><span data-stu-id="15396-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="15396-286">[Validação de modelo no ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="15396-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="15396-287">Manipular solicitações HEAD com um fallback de manipulador OnGet</span><span class="sxs-lookup"><span data-stu-id="15396-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="15396-288">`HEAD` solicitações permitem recuperar os cabeçalhos de um recurso específico.</span><span class="sxs-lookup"><span data-stu-id="15396-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="15396-289">Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="15396-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="15396-290">Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="15396-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="15396-291">Razor Pages volta a chamar o manipulador de `OnGet` se nenhum manipulador de `OnHead` for definido.</span><span class="sxs-lookup"><span data-stu-id="15396-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="15396-292">XSRF/CSRF e Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="15396-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="15396-293">Razor Pages são protegidos pela [validação de antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="15396-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="15396-294">O [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injeta tokens de antifalsificação em elementos de formulário HTML.</span><span class="sxs-lookup"><span data-stu-id="15396-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="15396-295">Usando Layouts, parciais, modelos e auxiliares de marcas com Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="15396-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="15396-296">As Páginas funcionam com todos os recursos do mecanismo de exibição do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="15396-297">Layouts, parciais, modelos, auxiliares de marca, *_ViewStart. cshtml*e *_ViewImports. cshtml* funcionam da mesma maneira que fazem para exibições convencionais do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="15396-298">Organizaremos essa página aproveitando alguns desses recursos.</span><span class="sxs-lookup"><span data-stu-id="15396-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="15396-299">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="15396-300">O [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="15396-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="15396-301">Controla o layout de cada página (a menos que a página opte por não usar o layout).</span><span class="sxs-lookup"><span data-stu-id="15396-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="15396-302">Importa estruturas HTML como JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="15396-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="15396-303">O conteúdo da página Razor é renderizado onde `@RenderBody()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="15396-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="15396-304">Para obter mais informações, consulte [página de layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="15396-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="15396-305">A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="15396-306">O layout está na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="15396-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="15396-307">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="15396-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="15396-308">Um layout na pasta *Pages/Shared* pode ser usado em qualquer página do Razor na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="15396-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="15396-309">O arquivo de layout deve entrar na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="15396-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="15396-310">Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="15396-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="15396-311">*Views/Shared* é um padrão de exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="15396-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="15396-312">As Páginas do Razor devem confiar na hierarquia de pasta e não nas convenções de caminho.</span><span class="sxs-lookup"><span data-stu-id="15396-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="15396-313">A pesquisa de modo de exibição de uma Página do Razor inclui a pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="15396-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="15396-314">Os layouts, modelos e parciais usados com controladores MVC e exibições convencionais do Razor *funcionam apenas*.</span><span class="sxs-lookup"><span data-stu-id="15396-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="15396-315">Adicione um arquivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="15396-316">`@namespace` é explicado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="15396-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="15396-317">A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="15396-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="15396-318">A diretiva `@namespace` definida em uma página:</span><span class="sxs-lookup"><span data-stu-id="15396-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="15396-319">A diretiva `@namespace` define o namespace da página.</span><span class="sxs-lookup"><span data-stu-id="15396-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="15396-320">A diretiva `@model` não precisa incluir o namespace.</span><span class="sxs-lookup"><span data-stu-id="15396-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="15396-321">Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="15396-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="15396-322">O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.</span><span class="sxs-lookup"><span data-stu-id="15396-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="15396-323">Por exemplo, a classe `PageModel`*Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:</span><span class="sxs-lookup"><span data-stu-id="15396-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="15396-324">O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="15396-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="15396-325">O namespace gerado para o Razor Pages *Pages/Customers/Edit.cshtml* é o mesmo que a classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="15396-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="15396-326">`@namespace` *também funciona com exibições convencionais do Razor.*</span><span class="sxs-lookup"><span data-stu-id="15396-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="15396-327">Considere o arquivo de exibição *páginas/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="15396-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="15396-328">O arquivo de exibição *páginas/Create. cshtml* atualizado com *_ViewImports. cshtml* e o arquivo de layout anterior:</span><span class="sxs-lookup"><span data-stu-id="15396-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="15396-329">No código anterior, o *_ViewImports. cshtml* importou os auxiliares de namespace e de marca.</span><span class="sxs-lookup"><span data-stu-id="15396-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="15396-330">O arquivo de layout importou os arquivos JavaScript.</span><span class="sxs-lookup"><span data-stu-id="15396-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="15396-331">O [projeto inicial de Páginas do Razor](#rpvs17) contém o *Pages/_ValidationScriptsPartial.cshtml*, que conecta a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="15396-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="15396-332">Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="15396-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="15396-333">Geração de URL para Páginas</span><span class="sxs-lookup"><span data-stu-id="15396-333">URL generation for Pages</span></span>

<span data-ttu-id="15396-334">A página `Create`, exibida anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="15396-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="15396-335">O aplicativo tem a estrutura de arquivos/pastas a seguir:</span><span class="sxs-lookup"><span data-stu-id="15396-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="15396-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="15396-336">*/Pages*</span></span>

  * <span data-ttu-id="15396-337">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="15396-338">*Privacidade. cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="15396-339">*/Clientes*</span><span class="sxs-lookup"><span data-stu-id="15396-339">*/Customers*</span></span>

    * <span data-ttu-id="15396-340">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="15396-341">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="15396-342">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-342">*Index.cshtml*</span></span>

<span data-ttu-id="15396-343">As páginas *pages/Customers/Create. cshtml* e *pages/Customers/Edit. cshtml* redirecionam para *pages/Customers/index. cshtml* após o êxito.</span><span class="sxs-lookup"><span data-stu-id="15396-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="15396-344">A cadeia de caracteres `./Index` é um nome de página relativo usado para acessar a página anterior.</span><span class="sxs-lookup"><span data-stu-id="15396-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="15396-345">Ele é usado para gerar URLs para a página *pages/Customers/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="15396-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="15396-346">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="15396-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="15396-347">O nome de página absoluto `/Index` é usado para gerar URLs para a página *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="15396-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="15396-348">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="15396-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="15396-349">O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="15396-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="15396-350">Os exemplos de geração de URL anteriores oferecem opções avançadas e recursos funcionais por meio da codificação de uma URL.</span><span class="sxs-lookup"><span data-stu-id="15396-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="15396-351">A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.</span><span class="sxs-lookup"><span data-stu-id="15396-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="15396-352">A Geração de URL para páginas dá suporte a nomes relativos.</span><span class="sxs-lookup"><span data-stu-id="15396-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="15396-353">A tabela a seguir mostra qual página de índice é selecionada usando parâmetros de `RedirectToPage` diferentes em *pages/Customers/Create. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="15396-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="15396-354">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="15396-354">RedirectToPage(x)</span></span>| <span data-ttu-id="15396-355">{1&gt;Página&lt;1}</span><span class="sxs-lookup"><span data-stu-id="15396-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="15396-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="15396-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="15396-357">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="15396-357">*Pages/Index*</span></span> |
| <span data-ttu-id="15396-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="15396-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="15396-359">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="15396-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="15396-360">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="15396-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="15396-361">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="15396-361">*Pages/Index*</span></span> |
| <span data-ttu-id="15396-362">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="15396-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="15396-363">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="15396-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="15396-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`e `RedirectToPage("../Index")` são *nomes relativos*.</span><span class="sxs-lookup"><span data-stu-id="15396-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="15396-365">O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.</span><span class="sxs-lookup"><span data-stu-id="15396-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="15396-366">Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa.</span><span class="sxs-lookup"><span data-stu-id="15396-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="15396-367">Quando nomes relativos são usados para vincular entre páginas em uma pasta:</span><span class="sxs-lookup"><span data-stu-id="15396-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="15396-368">Renomear uma pasta não interrompe os links relativos.</span><span class="sxs-lookup"><span data-stu-id="15396-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="15396-369">Os links não são desfeitos porque não incluem o nome da pasta.</span><span class="sxs-lookup"><span data-stu-id="15396-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="15396-370">Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:</span><span class="sxs-lookup"><span data-stu-id="15396-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="15396-371">Para obter mais informações, consulte <xref:mvc/controllers/areas> e <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="15396-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="15396-372">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="15396-372">ViewData attribute</span></span>

<span data-ttu-id="15396-373">Os dados podem ser passados para uma página com <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span><span class="sxs-lookup"><span data-stu-id="15396-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="15396-374">As propriedades com o atributo `[ViewData]` têm seus valores armazenados e carregados do <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span><span class="sxs-lookup"><span data-stu-id="15396-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="15396-375">No exemplo a seguir, o `AboutModel` aplica o atributo `[ViewData]` à propriedade `Title`:</span><span class="sxs-lookup"><span data-stu-id="15396-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="15396-376">Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="15396-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="15396-377">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="15396-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="15396-378">TempData</span><span class="sxs-lookup"><span data-stu-id="15396-378">TempData</span></span>

<span data-ttu-id="15396-379">ASP.NET Core expõe a <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span><span class="sxs-lookup"><span data-stu-id="15396-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="15396-380">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="15396-380">This property stores data until it's read.</span></span> <span data-ttu-id="15396-381">Os métodos <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> e <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="15396-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="15396-382">`TempData` é útil para o redirecionamento, quando os dados são necessários para mais de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="15396-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="15396-383">Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="15396-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="15396-384">A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="15396-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="15396-385">O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.</span><span class="sxs-lookup"><span data-stu-id="15396-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="15396-386">Para obter mais informações, consulte [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="15396-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="15396-387">Vários manipuladores por página</span><span class="sxs-lookup"><span data-stu-id="15396-387">Multiple handlers per page</span></span>

<span data-ttu-id="15396-388">A página a seguir gera marcação para dois manipuladores usando o auxiliar de marcação `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="15396-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="15396-389">O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente.</span><span class="sxs-lookup"><span data-stu-id="15396-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="15396-390">O atributo `asp-page-handler` é um complemento para `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="15396-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="15396-391">`asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="15396-392">`asp-page` não foi especificado porque a amostra está vinculando à página atual.</span><span class="sxs-lookup"><span data-stu-id="15396-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="15396-393">O modelo de página:</span><span class="sxs-lookup"><span data-stu-id="15396-393">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="15396-394">O código anterior usa *métodos de manipulador nomeados*.</span><span class="sxs-lookup"><span data-stu-id="15396-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="15396-395">Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver).</span><span class="sxs-lookup"><span data-stu-id="15396-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="15396-396">No exemplo anterior, os métodos de página são OnPost**JoinList**Async e OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="15396-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="15396-397">Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="15396-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="15396-398">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="15396-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="15396-399">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="15396-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="15396-400">Rotas personalizadas</span><span class="sxs-lookup"><span data-stu-id="15396-400">Custom routes</span></span>

<span data-ttu-id="15396-401">Use a diretiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="15396-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="15396-402">Especifique uma rota personalizada para uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-402">Specify a custom route to a page.</span></span> <span data-ttu-id="15396-403">Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="15396-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="15396-404">Acrescente segmentos à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-404">Append segments to a page's default route.</span></span> <span data-ttu-id="15396-405">Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="15396-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="15396-406">Acrescente parâmetros à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="15396-407">Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="15396-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="15396-408">Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho.</span><span class="sxs-lookup"><span data-stu-id="15396-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="15396-409">Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="15396-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="15396-410">Se você não gostar da cadeia de caracteres de consulta `?handler=JoinList` na URL, altere a rota para colocar o nome do manipulador na parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="15396-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="15396-411">A rota pode ser personalizada adicionando um modelo de rota entre aspas duplas após a diretiva de `@page`.</span><span class="sxs-lookup"><span data-stu-id="15396-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="15396-412">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="15396-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="15396-413">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="15396-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="15396-414">O `?` após `handler` significa que o parâmetro de rota é opcional.</span><span class="sxs-lookup"><span data-stu-id="15396-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="15396-415">Configuração e configurações avançadas</span><span class="sxs-lookup"><span data-stu-id="15396-415">Advanced configuration and settings</span></span>

<span data-ttu-id="15396-416">A configuração e as configurações nas seções a seguir não são exigidas pela maioria dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="15396-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="15396-417">Para configurar opções avançadas, use o método de extensão <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span><span class="sxs-lookup"><span data-stu-id="15396-417">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="15396-418">Use a <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> para definir o diretório raiz para páginas ou adicionar convenções de modelo de aplicativo para páginas.</span><span class="sxs-lookup"><span data-stu-id="15396-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="15396-419">Para obter mais informações sobre convenções, consulte [Razor Pages convenções de autorização](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="15396-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="15396-420">Para pré-compilar exibições, consulte [compilação de exibição do Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="15396-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="15396-421">Especificar que as Páginas Razor estão na raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="15396-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="15396-422">Por padrão, as Páginas Razor estão na raiz do diretório */Pages*.</span><span class="sxs-lookup"><span data-stu-id="15396-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="15396-423">Adicione <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> para especificar que seus Razor Pages estão na [raiz do conteúdo](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="15396-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="15396-424">Especificar que as Páginas Razor estão em um diretório raiz personalizado</span><span class="sxs-lookup"><span data-stu-id="15396-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="15396-425">Adicione <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> para especificar que Razor Pages estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):</span><span class="sxs-lookup"><span data-stu-id="15396-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="15396-426">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="15396-426">Additional resources</span></span>

* <span data-ttu-id="15396-427">Consulte Introdução [ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução</span><span class="sxs-lookup"><span data-stu-id="15396-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="15396-428">Baixar ou exibir código de exemplo</span><span class="sxs-lookup"><span data-stu-id="15396-428">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="15396-429">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="15396-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="15396-430">Páginas do Razor é um novo aspecto do ASP.NET Core MVC que torna a codificação de cenários focados em página mais fácil e produtiva.</span><span class="sxs-lookup"><span data-stu-id="15396-430">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="15396-431">Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="15396-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="15396-432">Este documento proporciona uma introdução a páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="15396-433">Este não é um tutorial passo a passo.</span><span class="sxs-lookup"><span data-stu-id="15396-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="15396-434">Se você achar que algumas das seções são muito avançadas, consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="15396-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="15396-435">Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="15396-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="15396-436">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="15396-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="15396-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="15396-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="15396-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="15396-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="15396-439">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="15396-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="15396-440">Criar um projeto do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="15396-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="15396-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="15396-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="15396-442">Confira a [Introdução ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="15396-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="15396-443">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="15396-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="15396-444">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="15396-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="15396-445">Abra o arquivo *.csproj* gerado do Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="15396-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="15396-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="15396-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="15396-447">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="15396-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="15396-448">Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="15396-448">Razor Pages</span></span>

<span data-ttu-id="15396-449">O Páginas do Razor está habilitado em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="15396-449">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="15396-450">Considere uma página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="15396-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="15396-451">O código anterior se assemelha muito a um [arquivo de exibição do Razor](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="15396-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="15396-452">O que o torna diferentes é a diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="15396-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="15396-453">`@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador.</span><span class="sxs-lookup"><span data-stu-id="15396-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="15396-454">`@page` deve ser a primeira diretiva do Razor em uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="15396-455">`@page` afeta o comportamento de outros constructos do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="15396-456">Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir.</span><span class="sxs-lookup"><span data-stu-id="15396-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="15396-457">O arquivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="15396-458">O modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="15396-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="15396-459">Por convenção, o arquivo de classe `PageModel` tem o mesmo nome que o arquivo na Página do Razor com *.cs* acrescentado.</span><span class="sxs-lookup"><span data-stu-id="15396-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="15396-460">Por exemplo, a Página do Razor anterior é *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="15396-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="15396-461">O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="15396-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="15396-462">As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="15396-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="15396-463">A tabela a seguir mostra um caminho de Página do Razor e a URL correspondente:</span><span class="sxs-lookup"><span data-stu-id="15396-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="15396-464">Caminho e nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="15396-464">File name and path</span></span>               | <span data-ttu-id="15396-465">URL correspondente</span><span class="sxs-lookup"><span data-stu-id="15396-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="15396-466">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="15396-467">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="15396-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="15396-468">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="15396-469">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="15396-470">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="15396-471">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="15396-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="15396-472">Observações:</span><span class="sxs-lookup"><span data-stu-id="15396-472">Notes:</span></span>

* <span data-ttu-id="15396-473">O runtime procura arquivos de Páginas do Razor na pasta *Pages* por padrão.</span><span class="sxs-lookup"><span data-stu-id="15396-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="15396-474">`Index` é a página padrão quando uma URL não inclui uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="15396-475">Escrever um formulário básico</span><span class="sxs-lookup"><span data-stu-id="15396-475">Write a basic form</span></span>

<span data-ttu-id="15396-476">Páginas do Razor foi projetado para facilitar a implementação de padrões comuns usados com navegadores da Web ao criar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="15396-476">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="15396-477">[Model binding](xref:mvc/models/model-binding), [auxiliares de marcas](xref:mvc/views/tag-helpers/intro) e auxiliares HTML *funcionam todos apenas* com as propriedades definidas em uma classe de Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="15396-478">Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="15396-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="15396-479">Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="15396-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="15396-480">O modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="15396-480">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="15396-481">O contexto do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="15396-481">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="15396-482">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="15396-483">O modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="15396-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="15396-484">Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.</span><span class="sxs-lookup"><span data-stu-id="15396-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="15396-485">A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação.</span><span class="sxs-lookup"><span data-stu-id="15396-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="15396-486">Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página.</span><span class="sxs-lookup"><span data-stu-id="15396-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="15396-487">Essa separação permite:</span><span class="sxs-lookup"><span data-stu-id="15396-487">This separation allows:</span></span>

* <span data-ttu-id="15396-488">Gerenciamento de dependências de página por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="15396-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="15396-489">[Teste de unidade](xref:test/razor-pages-tests) das páginas.</span><span class="sxs-lookup"><span data-stu-id="15396-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="15396-490">A página tem um *método de manipulador*de `OnPostAsync`, que é executado em `POST` solicitações (quando um usuário posta o formulário).</span><span class="sxs-lookup"><span data-stu-id="15396-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="15396-491">Você pode adicionar métodos de manipulador para qualquer verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="15396-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="15396-492">Os manipuladores mais comuns são:</span><span class="sxs-lookup"><span data-stu-id="15396-492">The most common handlers are:</span></span>

* <span data-ttu-id="15396-493">`OnGet` para inicializar o estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="15396-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="15396-494">Amostra de [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="15396-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="15396-495">`OnPost` para manipular envios de formulário.</span><span class="sxs-lookup"><span data-stu-id="15396-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="15396-496">O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas.</span><span class="sxs-lookup"><span data-stu-id="15396-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="15396-497">O código anterior é comum para as Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="15396-498">Se você estiver familiarizado com os aplicativos ASP.NET usando os controladores e exibições:</span><span class="sxs-lookup"><span data-stu-id="15396-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="15396-499">O código de `OnPostAsync` no exemplo anterior é semelhante ao código de controlador típico.</span><span class="sxs-lookup"><span data-stu-id="15396-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="15396-500">A maioria dos primitivos MVC, como [Associação de modelo](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation), [validação](xref:mvc/models/validation)e resultados de ação, é compartilhada.</span><span class="sxs-lookup"><span data-stu-id="15396-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="15396-501">O método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="15396-501">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="15396-502">O fluxo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="15396-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="15396-503">Verifique se há erros de validação.</span><span class="sxs-lookup"><span data-stu-id="15396-503">Check for validation errors.</span></span>

* <span data-ttu-id="15396-504">Se não houver nenhum erro, salve os dados e redirecione.</span><span class="sxs-lookup"><span data-stu-id="15396-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="15396-505">Se houver erros, mostre a página novamente com as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="15396-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="15396-506">A validação do lado do cliente é idêntica para aplicativos ASP.NET Core MVC tradicionais.</span><span class="sxs-lookup"><span data-stu-id="15396-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="15396-507">Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.</span><span class="sxs-lookup"><span data-stu-id="15396-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="15396-508">Quando os dados são inseridos com êxito, o método de manipulador `OnPostAsync` chama o método auxiliar `RedirectToPage` para retornar uma instância de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="15396-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="15396-509">`RedirectToPage` é um novo resultado de ação, semelhante a `RedirectToAction` ou `RedirectToRoute`, mas personalizado para páginas.</span><span class="sxs-lookup"><span data-stu-id="15396-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="15396-510">Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="15396-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="15396-511">`RedirectToPage` é descrito em detalhes na seção [Geração de URLs para páginas](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="15396-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="15396-512">Quando o formulário enviado tem erros de validação (que são passados para o servidor), o método de manipulador `OnPostAsync` chama o método auxiliar `Page`.</span><span class="sxs-lookup"><span data-stu-id="15396-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="15396-513">`Page` retorna uma instância de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="15396-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="15396-514">Retornar `Page` é semelhante a como as ações em controladores retornam `View`.</span><span class="sxs-lookup"><span data-stu-id="15396-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="15396-515">`PageResult` é o tipo de retorno padrão para um método de manipulador.</span><span class="sxs-lookup"><span data-stu-id="15396-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="15396-516">Um método de manipulador que retorna `void` renderiza a página.</span><span class="sxs-lookup"><span data-stu-id="15396-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="15396-517">A propriedade `Customer` usa o atributo `[BindProperty]` para aceitar o model binding.</span><span class="sxs-lookup"><span data-stu-id="15396-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="15396-518">O Razor Pages, por padrão, associa propriedades somente com verbos não `GET`.</span><span class="sxs-lookup"><span data-stu-id="15396-518">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="15396-519">A associação de propriedades pode reduzir a quantidade de código que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="15396-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="15396-520">A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.</span><span class="sxs-lookup"><span data-stu-id="15396-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="15396-521">A home page (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="15396-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="15396-522">A classe `PageModel` (*Index.cshtml.cs*) associada:</span><span class="sxs-lookup"><span data-stu-id="15396-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="15396-523">O arquivo *cshtml* contém a marcação a seguir para criar um link de edição para cada contato:</span><span class="sxs-lookup"><span data-stu-id="15396-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="15396-524">O [auxiliar de marca de ancoragem](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` usou o atributo `asp-route-{value}` para gerar um link para a página de edição.</span><span class="sxs-lookup"><span data-stu-id="15396-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="15396-525">O link contém dados de rota com a ID de contato.</span><span class="sxs-lookup"><span data-stu-id="15396-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="15396-526">Por exemplo, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="15396-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="15396-527">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="15396-528">Os auxiliares de marca são habilitados pelo `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="15396-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="15396-529">O arquivo *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="15396-530">A primeira linha contém a diretiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="15396-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="15396-531">A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`.</span><span class="sxs-lookup"><span data-stu-id="15396-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="15396-532">Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o runtime retorna um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="15396-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="15396-533">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="15396-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="15396-534">O arquivo *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="15396-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="15396-535">O arquivo *Index.cshtml* também contém a marcação para criar um botão de exclusão para cada contato de cliente:</span><span class="sxs-lookup"><span data-stu-id="15396-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="15396-536">Quando o botão de exclusão é renderizado em HTML, seu `formaction` inclui parâmetros para:</span><span class="sxs-lookup"><span data-stu-id="15396-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="15396-537">A ID de contato do cliente especificada pelo atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="15396-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="15396-538">O `handler` especificado pelo atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="15396-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="15396-539">Este é um exemplo de um botão de exclusão renderizado com uma ID de contato do cliente de `1`:</span><span class="sxs-lookup"><span data-stu-id="15396-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="15396-540">Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="15396-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="15396-541">Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="15396-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="15396-542">Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`.</span><span class="sxs-lookup"><span data-stu-id="15396-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="15396-543">Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado.</span><span class="sxs-lookup"><span data-stu-id="15396-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="15396-544">O código a seguir mostra o manipulador de `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="15396-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="15396-545">O método `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="15396-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="15396-546">Aceita o `id` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="15396-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="15396-547">Se a diretiva de página *index. cshtml* continha a restrição de roteamento `"{id:int?}"`, `id` seriam provenientes de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="15396-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="15396-548">Os dados de rota para `id` são especificados no URI, como `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="15396-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="15396-549">Consulta o banco de dados para o contato de cliente com `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="15396-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="15396-550">Se o contato do cliente for encontrado, eles serão removidos da lista de contatos do cliente.</span><span class="sxs-lookup"><span data-stu-id="15396-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="15396-551">O banco de dados é atualizado.</span><span class="sxs-lookup"><span data-stu-id="15396-551">The database is updated.</span></span>
* <span data-ttu-id="15396-552">Chama `RedirectToPage` para redirecionar para a página de índice de raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="15396-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="15396-553">Marque as propriedades da página conforme necessário</span><span class="sxs-lookup"><span data-stu-id="15396-553">Mark page properties as required</span></span>

<span data-ttu-id="15396-554">As propriedades em uma `PageModel` podem ser marcadas com o atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) :</span><span class="sxs-lookup"><span data-stu-id="15396-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="15396-555">Para obter mais informações, confira [Validação de modelo](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="15396-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="15396-556">Manipular solicitações HEAD com um fallback de manipulador OnGet</span><span class="sxs-lookup"><span data-stu-id="15396-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="15396-557">As solicitações `HEAD` permitem recuperar os cabeçalhos de um recurso específico.</span><span class="sxs-lookup"><span data-stu-id="15396-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="15396-558">Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="15396-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="15396-559">Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`:</span><span class="sxs-lookup"><span data-stu-id="15396-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="15396-560">No ASP.NET Core 2.1 ou superior, o Razor Pages volta a chamar o manipulador `OnGet` quando nenhum manipulador `OnHead` está definido.</span><span class="sxs-lookup"><span data-stu-id="15396-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="15396-561">Esse comportamento é habilitado pela chamada para [SetCompatibilityVersion](xref:mvc/compatibility-version) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="15396-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="15396-562">Os modelos padrão geram a chamada `SetCompatibilityVersion` no ASP.NET Core 2.1 e 2.2.</span><span class="sxs-lookup"><span data-stu-id="15396-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="15396-563">`SetCompatibilityVersion` define de forma eficiente a opção de Páginas Razor `AllowMappingHeadRequestsToGetHandler` como `true`.</span><span class="sxs-lookup"><span data-stu-id="15396-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="15396-564">Em vez de aceitar todos os comportamentos com `SetCompatibilityVersion`, você pode aceitar explicitamente participar de comportamentos *específicos*.</span><span class="sxs-lookup"><span data-stu-id="15396-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="15396-565">O código a seguir aceita permitir que solicitações `HEAD` sejam mapeadas para o manipulador `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="15396-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="15396-566">XSRF/CSRF e Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="15396-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="15396-567">Você não precisa escrever nenhum código para [validação antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="15396-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="15396-568">Validação e geração de token antifalsificação são automaticamente incluídas nas Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="15396-569">Usando Layouts, parciais, modelos e auxiliares de marcas com Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="15396-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="15396-570">As Páginas funcionam com todos os recursos do mecanismo de exibição do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="15396-571">Layouts, parciais, modelos, auxiliares de marcas, *_ViewStart.cshtml* e *_ViewImports.cshtml* funcionam da mesma forma que funcionam exibições convencionais do Razor.</span><span class="sxs-lookup"><span data-stu-id="15396-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="15396-572">Organizaremos essa página aproveitando alguns desses recursos.</span><span class="sxs-lookup"><span data-stu-id="15396-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="15396-573">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="15396-574">O [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="15396-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="15396-575">Controla o layout de cada página (a menos que a página opte por não usar o layout).</span><span class="sxs-lookup"><span data-stu-id="15396-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="15396-576">Importa estruturas HTML como JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="15396-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="15396-577">Veja [página de layout](xref:mvc/views/layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="15396-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="15396-578">A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="15396-579">O layout está na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="15396-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="15396-580">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="15396-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="15396-581">Um layout na pasta *Pages/Shared* pode ser usado em qualquer página do Razor na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="15396-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="15396-582">O arquivo de layout deve entrar na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="15396-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="15396-583">Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="15396-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="15396-584">*Views/Shared* é um padrão de exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="15396-584">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="15396-585">As Páginas do Razor devem confiar na hierarquia de pasta e não nas convenções de caminho.</span><span class="sxs-lookup"><span data-stu-id="15396-585">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="15396-586">A pesquisa de modo de exibição de uma Página do Razor inclui a pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="15396-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="15396-587">Os layouts, modelos e parciais que você está usando com controladores MVC e exibições do Razor convencionais *apenas funcionam*.</span><span class="sxs-lookup"><span data-stu-id="15396-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="15396-588">Adicione um arquivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="15396-589">`@namespace` é explicado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="15396-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="15396-590">A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="15396-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="15396-591">Quando a diretiva `@namespace` é usada explicitamente em uma página:</span><span class="sxs-lookup"><span data-stu-id="15396-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="15396-592">A diretiva define o namespace da página.</span><span class="sxs-lookup"><span data-stu-id="15396-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="15396-593">A diretiva `@model` não precisa incluir o namespace.</span><span class="sxs-lookup"><span data-stu-id="15396-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="15396-594">Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="15396-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="15396-595">O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.</span><span class="sxs-lookup"><span data-stu-id="15396-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="15396-596">Por exemplo, a classe `PageModel`*Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:</span><span class="sxs-lookup"><span data-stu-id="15396-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="15396-597">O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="15396-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="15396-598">O namespace gerado para o Razor Pages *Pages/Customers/Edit.cshtml* é o mesmo que a classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="15396-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="15396-599">`@namespace` *também funciona com exibições convencionais do Razor.*</span><span class="sxs-lookup"><span data-stu-id="15396-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="15396-600">O arquivo de exibição *Pages/Create.cshtml* original:</span><span class="sxs-lookup"><span data-stu-id="15396-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="15396-601">O arquivo de exibição *Pages/Create.cshtml* atualizado:</span><span class="sxs-lookup"><span data-stu-id="15396-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="15396-602">O [projeto inicial de Páginas do Razor](#rpvs17) contém o *Pages/_ValidationScriptsPartial.cshtml*, que conecta a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="15396-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="15396-603">Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="15396-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="15396-604">Geração de URL para Páginas</span><span class="sxs-lookup"><span data-stu-id="15396-604">URL generation for Pages</span></span>

<span data-ttu-id="15396-605">A página `Create`, exibida anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="15396-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="15396-606">O aplicativo tem a estrutura de arquivos/pastas a seguir:</span><span class="sxs-lookup"><span data-stu-id="15396-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="15396-607">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="15396-607">*/Pages*</span></span>

  * <span data-ttu-id="15396-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="15396-609">*/Clientes*</span><span class="sxs-lookup"><span data-stu-id="15396-609">*/Customers*</span></span>

    * <span data-ttu-id="15396-610">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="15396-611">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="15396-612">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="15396-612">*Index.cshtml*</span></span>

<span data-ttu-id="15396-613">As páginas *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* redirecionam para o *Pages/Index.cshtml* após êxito.</span><span class="sxs-lookup"><span data-stu-id="15396-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="15396-614">A cadeia de caracteres `/Index` faz parte do URI para acessar a página anterior.</span><span class="sxs-lookup"><span data-stu-id="15396-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="15396-615">A cadeia de caracteres `/Index` pode ser usada para gerar URIs para a página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="15396-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="15396-616">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="15396-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="15396-617">O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="15396-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="15396-618">Os exemplos anteriores de geração de URL oferecem opções avançadas e recursos funcionais para codificar uma URL.</span><span class="sxs-lookup"><span data-stu-id="15396-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="15396-619">A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.</span><span class="sxs-lookup"><span data-stu-id="15396-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="15396-620">A Geração de URL para páginas dá suporte a nomes relativos.</span><span class="sxs-lookup"><span data-stu-id="15396-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="15396-621">A tabela a seguir mostra qual página de Índice é selecionada com diferentes parâmetros `RedirectToPage` de *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="15396-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="15396-622">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="15396-622">RedirectToPage(x)</span></span>| <span data-ttu-id="15396-623">{1&gt;Página&lt;1}</span><span class="sxs-lookup"><span data-stu-id="15396-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="15396-624">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="15396-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="15396-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="15396-625">*Pages/Index*</span></span> |
| <span data-ttu-id="15396-626">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="15396-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="15396-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="15396-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="15396-628">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="15396-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="15396-629">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="15396-629">*Pages/Index*</span></span> |
| <span data-ttu-id="15396-630">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="15396-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="15396-631">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="15396-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="15396-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` são *nomes relativos*.</span><span class="sxs-lookup"><span data-stu-id="15396-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="15396-633">O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.</span><span class="sxs-lookup"><span data-stu-id="15396-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="15396-634">Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa.</span><span class="sxs-lookup"><span data-stu-id="15396-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="15396-635">Se você usar nomes relativos para vincular entre páginas em uma pasta, você poderá renomear essa pasta.</span><span class="sxs-lookup"><span data-stu-id="15396-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="15396-636">Todos os links ainda funcionarão (porque eles não incluirão o nome da pasta).</span><span class="sxs-lookup"><span data-stu-id="15396-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="15396-637">Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:</span><span class="sxs-lookup"><span data-stu-id="15396-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="15396-638">Para obter mais informações, consulte <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="15396-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="15396-639">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="15396-639">ViewData attribute</span></span>

<span data-ttu-id="15396-640">Os dados podem ser passados para uma página com [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="15396-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="15396-641">As propriedades em controladores ou modelos de página do Razor com o atributo `[ViewData]` têm seus valores armazenados e carregados do [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="15396-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="15396-642">No exemplo a seguir, o `AboutModel` contém uma propriedade `Title` marcada com `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="15396-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="15396-643">A propriedade `Title` está definida como o título da página Sobre:</span><span class="sxs-lookup"><span data-stu-id="15396-643">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="15396-644">Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="15396-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="15396-645">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="15396-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="15396-646">TempData</span><span class="sxs-lookup"><span data-stu-id="15396-646">TempData</span></span>

<span data-ttu-id="15396-647">O ASP.NET Core expõe a propriedade [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) em um [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="15396-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="15396-648">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="15396-648">This property stores data until it's read.</span></span> <span data-ttu-id="15396-649">Os métodos `Keep` e `Peek` podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="15396-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="15396-650">`TempData` é útil para redirecionamento nos casos em que os dados são necessários para mais de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="15396-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="15396-651">Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="15396-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="15396-652">A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="15396-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="15396-653">O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.</span><span class="sxs-lookup"><span data-stu-id="15396-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="15396-654">Para obter mais informações, confira [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="15396-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="15396-655">Vários manipuladores por página</span><span class="sxs-lookup"><span data-stu-id="15396-655">Multiple handlers per page</span></span>

<span data-ttu-id="15396-656">A página a seguir gera marcação para dois manipuladores usando o auxiliar de marcação `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="15396-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="15396-657">O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente.</span><span class="sxs-lookup"><span data-stu-id="15396-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="15396-658">O atributo `asp-page-handler` é um complemento para `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="15396-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="15396-659">`asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="15396-660">`asp-page` não foi especificado porque a amostra está vinculando à página atual.</span><span class="sxs-lookup"><span data-stu-id="15396-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="15396-661">O modelo de página:</span><span class="sxs-lookup"><span data-stu-id="15396-661">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="15396-662">O código anterior usa *métodos de manipulador nomeados*.</span><span class="sxs-lookup"><span data-stu-id="15396-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="15396-663">Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver).</span><span class="sxs-lookup"><span data-stu-id="15396-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="15396-664">No exemplo anterior, os métodos de página são OnPost**JoinList**Async e OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="15396-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="15396-665">Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="15396-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="15396-666">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="15396-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="15396-667">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="15396-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="15396-668">Rotas personalizadas</span><span class="sxs-lookup"><span data-stu-id="15396-668">Custom routes</span></span>

<span data-ttu-id="15396-669">Use a diretiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="15396-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="15396-670">Especifique uma rota personalizada para uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-670">Specify a custom route to a page.</span></span> <span data-ttu-id="15396-671">Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="15396-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="15396-672">Acrescente segmentos à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-672">Append segments to a page's default route.</span></span> <span data-ttu-id="15396-673">Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="15396-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="15396-674">Acrescente parâmetros à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="15396-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="15396-675">Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="15396-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="15396-676">Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho.</span><span class="sxs-lookup"><span data-stu-id="15396-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="15396-677">Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="15396-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="15396-678">Se você não gostar da cadeia de caracteres de consulta `?handler=JoinList` na URL, altere a rota para colocar o nome do manipulador na parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="15396-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="15396-679">A rota pode ser personalizada adicionando um modelo de rota entre aspas duplas após a diretiva de `@page`.</span><span class="sxs-lookup"><span data-stu-id="15396-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="15396-680">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="15396-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="15396-681">O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="15396-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="15396-682">O `?` após `handler` significa que o parâmetro de rota é opcional.</span><span class="sxs-lookup"><span data-stu-id="15396-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="15396-683">Configuração e definições</span><span class="sxs-lookup"><span data-stu-id="15396-683">Configuration and settings</span></span>

<span data-ttu-id="15396-684">Para configurar opções avançadas, use o método de extensão `AddRazorPagesOptions` no construtor de MVC:</span><span class="sxs-lookup"><span data-stu-id="15396-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="15396-685">No momento, você pode usar o `RazorPagesOptions` para definir o diretório raiz para páginas ou adicionar as convenções de modelo de aplicativo para páginas.</span><span class="sxs-lookup"><span data-stu-id="15396-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="15396-686">Permitiremos mais extensibilidade dessa maneira no futuro.</span><span class="sxs-lookup"><span data-stu-id="15396-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="15396-687">Para pré-compilar exibições, consulte [Compilação de exibição do Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="15396-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="15396-688">[Baixar ou exibir código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="15396-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="15396-689">Consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução.</span><span class="sxs-lookup"><span data-stu-id="15396-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="15396-690">Especificar que as Páginas Razor estão na raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="15396-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="15396-691">Por padrão, as Páginas Razor estão na raiz do diretório */Pages*.</span><span class="sxs-lookup"><span data-stu-id="15396-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="15396-692">Adicione [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que seus Razor Pages estão na [raiz do conteúdo](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="15396-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="15396-693">Especificar que as Páginas Razor estão em um diretório raiz personalizado</span><span class="sxs-lookup"><span data-stu-id="15396-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="15396-694">Adicione [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) em [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que as Páginas Razor estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):</span><span class="sxs-lookup"><span data-stu-id="15396-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="15396-695">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="15396-695">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
