---
title: Introdução a Páginas do Razor no ASP.NET Core
author: Rick-Anderson
description: Saiba como as Páginas Razor no ASP.NET Core tornam a codificação de cenários centrados em página mais fácil e mais produtiva do que com o uso de MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 04/06/2019
uid: razor-pages/index
ms.openlocfilehash: 93796fa1edfa316790794d3775342147ea28ae2e
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692546"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="39ab7-103">Introdução a Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39ab7-103">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="39ab7-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="39ab7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="39ab7-105">Páginas do Razor é um novo aspecto do ASP.NET Core MVC que torna a codificação de cenários focados em página mais fácil e produtiva.</span><span class="sxs-lookup"><span data-stu-id="39ab7-105">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="39ab7-106">Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="39ab7-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="39ab7-107">Este documento proporciona uma introdução a páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="39ab7-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="39ab7-108">Este não é um tutorial passo a passo.</span><span class="sxs-lookup"><span data-stu-id="39ab7-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="39ab7-109">Se você achar que algumas das seções são muito avançadas, consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="39ab7-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="39ab7-110">Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="39ab7-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="39ab7-111">Criar um projeto do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="39ab7-111">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="39ab7-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39ab7-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39ab7-113">Confira a [Introdução ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="39ab7-113">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="39ab7-114">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="39ab7-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="39ab7-115">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-115">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="39ab7-116">Da linha de comando, execute `dotnet new razor`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-116">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

<span data-ttu-id="39ab7-117">Abra o arquivo *.csproj* gerado do Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="39ab7-117">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="39ab7-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39ab7-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="39ab7-119">Da linha de comando, execute `dotnet new webapp`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-119">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="39ab7-120">Da linha de comando, execute `dotnet new razor`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-120">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

---

## <a name="razor-pages"></a><span data-ttu-id="39ab7-121">Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="39ab7-121">Razor Pages</span></span>

<span data-ttu-id="39ab7-122">O Páginas do Razor está habilitado em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-122">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="39ab7-123">Considere uma página básica: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="39ab7-123">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="39ab7-124">O código anterior se assemelha muito a um [arquivo de exibição do Razor](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="39ab7-124">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="39ab7-125">O que o torna diferentes é a diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-125">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="39ab7-126">`@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador.</span><span class="sxs-lookup"><span data-stu-id="39ab7-126">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="39ab7-127">`@page` deve ser a primeira diretiva do Razor em uma página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-127">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="39ab7-128">`@page` afeta o comportamento de outros constructos do Razor.</span><span class="sxs-lookup"><span data-stu-id="39ab7-128">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="39ab7-129">Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir.</span><span class="sxs-lookup"><span data-stu-id="39ab7-129">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="39ab7-130">O arquivo *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-130">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="39ab7-131">O modelo de página *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-131">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="39ab7-132">Por convenção, o arquivo de classe `PageModel` tem o mesmo nome que o arquivo na Página do Razor com *.cs* acrescentado.</span><span class="sxs-lookup"><span data-stu-id="39ab7-132">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="39ab7-133">Por exemplo, a Página do Razor anterior é *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-133">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="39ab7-134">O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-134">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="39ab7-135">As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="39ab7-135">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="39ab7-136">A tabela a seguir mostra um caminho de Página do Razor e a URL correspondente:</span><span class="sxs-lookup"><span data-stu-id="39ab7-136">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="39ab7-137">Caminho e nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="39ab7-137">File name and path</span></span>               | <span data-ttu-id="39ab7-138">URL correspondente</span><span class="sxs-lookup"><span data-stu-id="39ab7-138">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="39ab7-139">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="39ab7-139">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="39ab7-140">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="39ab7-140">`/` or `/Index`</span></span> |
| <span data-ttu-id="39ab7-141">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="39ab7-141">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="39ab7-142">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="39ab7-142">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="39ab7-143">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="39ab7-143">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="39ab7-144">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="39ab7-144">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="39ab7-145">Notas:</span><span class="sxs-lookup"><span data-stu-id="39ab7-145">Notes:</span></span>

* <span data-ttu-id="39ab7-146">O tempo de execução procura arquivos de Páginas do Razor na pasta *Pages* por padrão.</span><span class="sxs-lookup"><span data-stu-id="39ab7-146">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="39ab7-147">`Index` é a página padrão quando uma URL não inclui uma página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-147">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="39ab7-148">Escrever um formulário básico</span><span class="sxs-lookup"><span data-stu-id="39ab7-148">Write a basic form</span></span>

<span data-ttu-id="39ab7-149">Páginas do Razor foi projetado para facilitar a implementação de padrões comuns usados com navegadores da Web ao criar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39ab7-149">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="39ab7-150">[Model binding](xref:mvc/models/model-binding), [auxiliares de marcas](xref:mvc/views/tag-helpers/intro) e auxiliares HTML *funcionam todos apenas* com as propriedades definidas em uma classe de Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="39ab7-150">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="39ab7-151">Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:</span><span class="sxs-lookup"><span data-stu-id="39ab7-151">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="39ab7-152">Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="39ab7-152">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="39ab7-153">O modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="39ab7-153">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="39ab7-154">O contexto do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="39ab7-154">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="39ab7-155">O arquivo de exibição *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-155">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="39ab7-156">O modelo de página *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-156">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="39ab7-157">Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-157">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="39ab7-158">A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação.</span><span class="sxs-lookup"><span data-stu-id="39ab7-158">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="39ab7-159">Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-159">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="39ab7-160">Esta separação permite gerenciar as dependências da página por meio de [injeção de dependência](xref:fundamentals/dependency-injection) e realizar um [teste de unidade](xref:test/razor-pages-tests) nas páginas.</span><span class="sxs-lookup"><span data-stu-id="39ab7-160">This separation allows you to manage page dependencies through [dependency injection](xref:fundamentals/dependency-injection) and to [unit test](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="39ab7-161">A página tem um *método de manipulador* `OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário).</span><span class="sxs-lookup"><span data-stu-id="39ab7-161">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="39ab7-162">Você pode adicionar métodos de manipulador para qualquer verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="39ab7-162">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="39ab7-163">Os manipuladores mais comuns são:</span><span class="sxs-lookup"><span data-stu-id="39ab7-163">The most common handlers are:</span></span>

* <span data-ttu-id="39ab7-164">`OnGet` para inicializar o estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-164">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="39ab7-165">Amostra de [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="39ab7-165">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="39ab7-166">`OnPost` para manipular envios de formulário.</span><span class="sxs-lookup"><span data-stu-id="39ab7-166">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="39ab7-167">O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas.</span><span class="sxs-lookup"><span data-stu-id="39ab7-167">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="39ab7-168">O código `OnPostAsync` no exemplo anterior tem aparência semelhante ao que você normalmente escreve em um controlador.</span><span class="sxs-lookup"><span data-stu-id="39ab7-168">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="39ab7-169">O código anterior é comum para as Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="39ab7-169">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="39ab7-170">A maioria dos primitivos MVC como [model binding](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation) e resultados da ação são compartilhados.</span><span class="sxs-lookup"><span data-stu-id="39ab7-170">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="39ab7-171">O método `OnPostAsync` anterior:</span><span class="sxs-lookup"><span data-stu-id="39ab7-171">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="39ab7-172">O fluxo básico de `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="39ab7-172">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="39ab7-173">Verifique se há erros de validação.</span><span class="sxs-lookup"><span data-stu-id="39ab7-173">Check for validation errors.</span></span>

* <span data-ttu-id="39ab7-174">Se não houver nenhum erro, salve os dados e redirecione.</span><span class="sxs-lookup"><span data-stu-id="39ab7-174">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="39ab7-175">Se houver erros, mostre a página novamente com as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="39ab7-175">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="39ab7-176">A validação do lado do cliente é idêntica para aplicativos ASP.NET Core MVC tradicionais.</span><span class="sxs-lookup"><span data-stu-id="39ab7-176">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="39ab7-177">Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.</span><span class="sxs-lookup"><span data-stu-id="39ab7-177">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="39ab7-178">Quando os dados são inseridos com êxito, o método de manipulador `OnPostAsync` chama o método auxiliar `RedirectToPage` para retornar uma instância de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-178">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="39ab7-179">`RedirectToPage` é um novo resultado de ação, semelhante a `RedirectToAction` ou `RedirectToRoute`, mas personalizado para páginas.</span><span class="sxs-lookup"><span data-stu-id="39ab7-179">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="39ab7-180">Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="39ab7-180">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="39ab7-181">`RedirectToPage` é descrito em detalhes na seção [Geração de URLs para páginas](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="39ab7-181">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="39ab7-182">Quando o formulário enviado tem erros de validação (que são passados para o servidor), o método de manipulador `OnPostAsync` chama o método auxiliar `Page`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-182">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="39ab7-183">`Page` retorna uma instância de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-183">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="39ab7-184">Retornar `Page` é semelhante a como as ações em controladores retornam `View`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-184">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="39ab7-185">`PageResult` é o tipo</span><span class="sxs-lookup"><span data-stu-id="39ab7-185">`PageResult` is the default</span></span> <!-- Review  --> <span data-ttu-id="39ab7-186">de retorno padrão para um método de manipulação.</span><span class="sxs-lookup"><span data-stu-id="39ab7-186">return type for a handler method.</span></span> <span data-ttu-id="39ab7-187">Um método de manipulador que retorna `void` renderiza a página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-187">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="39ab7-188">A propriedade `Customer` usa o atributo `[BindProperty]` para aceitar o model binding.</span><span class="sxs-lookup"><span data-stu-id="39ab7-188">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="39ab7-189">Páginas do Razor, por padrão, associam as propriedades somente com verbos não GET.</span><span class="sxs-lookup"><span data-stu-id="39ab7-189">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="39ab7-190">A associação de propriedades pode reduzir a quantidade de código que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="39ab7-190">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="39ab7-191">A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.</span><span class="sxs-lookup"><span data-stu-id="39ab7-191">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="39ab7-192">A home page (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="39ab7-192">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="39ab7-193">A classe `PageModel` (*Index.cshtml.cs*) associada:</span><span class="sxs-lookup"><span data-stu-id="39ab7-193">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="39ab7-194">O arquivo *cshtml* contém a marcação a seguir para criar um link de edição para cada contato:</span><span class="sxs-lookup"><span data-stu-id="39ab7-194">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="39ab7-195">O [auxiliar de marcas de âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) usou o atributo `asp-route-{value}` para gerar um link para a página Edit.</span><span class="sxs-lookup"><span data-stu-id="39ab7-195">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="39ab7-196">O link contém dados de rota com a ID de contato.</span><span class="sxs-lookup"><span data-stu-id="39ab7-196">The link contains route data with the contact ID.</span></span> <span data-ttu-id="39ab7-197">Por exemplo, `http://localhost:5000/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-197">For example, `http://localhost:5000/Edit/1`.</span></span> <span data-ttu-id="39ab7-198">Use o atributo `asp-area` para especificar uma área.</span><span class="sxs-lookup"><span data-stu-id="39ab7-198">Use the `asp-area` attribute to specify an area.</span></span> <span data-ttu-id="39ab7-199">Para obter mais informações, consulte <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="39ab7-199">For more information, see <xref:mvc/controllers/areas>.</span></span>

<span data-ttu-id="39ab7-200">O arquivo *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-200">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="39ab7-201">A primeira linha contém a diretiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-201">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="39ab7-202">A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-202">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="39ab7-203">Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o tempo de execução retorna um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="39ab7-203">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="39ab7-204">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="39ab7-204">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="39ab7-205">O arquivo *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-205">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="39ab7-206">O arquivo *Index.cshtml* também contém a marcação para criar um botão de exclusão para cada contato de cliente:</span><span class="sxs-lookup"><span data-stu-id="39ab7-206">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="39ab7-207">Quando o botão de exclusão é renderizado em HTML, seu `formaction` inclui parâmetros para:</span><span class="sxs-lookup"><span data-stu-id="39ab7-207">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="39ab7-208">A ID de contato do cliente especificada pelo atributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-208">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="39ab7-209">O `handler` especificado pelo atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-209">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="39ab7-210">Este é um exemplo de um botão de exclusão renderizado com uma ID de contato do cliente de `1`:</span><span class="sxs-lookup"><span data-stu-id="39ab7-210">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="39ab7-211">Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor.</span><span class="sxs-lookup"><span data-stu-id="39ab7-211">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="39ab7-212">Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-212">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="39ab7-213">Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-213">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="39ab7-214">Se o `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador de página com o nome `OnPostRemoveAsync` será selecionado.</span><span class="sxs-lookup"><span data-stu-id="39ab7-214">If the `asp-page-handler` is set to a different value, such as `remove`, a page handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="39ab7-215">O método `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="39ab7-215">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="39ab7-216">Aceita o `id` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="39ab7-216">Accepts the `id` from the query string.</span></span>
* <span data-ttu-id="39ab7-217">Consulta o banco de dados para o contato de cliente com `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-217">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="39ab7-218">Se o contato do cliente for encontrado, eles serão removidos da lista de contatos do cliente.</span><span class="sxs-lookup"><span data-stu-id="39ab7-218">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="39ab7-219">O banco de dados é atualizado.</span><span class="sxs-lookup"><span data-stu-id="39ab7-219">The database is updated.</span></span>
* <span data-ttu-id="39ab7-220">Chama `RedirectToPage` para redirecionar para a página de índice de raiz (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="39ab7-220">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="39ab7-221">Marque as propriedades da página conforme necessário</span><span class="sxs-lookup"><span data-stu-id="39ab7-221">Mark page properties as required</span></span>

<span data-ttu-id="39ab7-222">As propriedades em um `PageModel` podem ser decoradas com o atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):</span><span class="sxs-lookup"><span data-stu-id="39ab7-222">Properties on a `PageModel` can be decorated with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="39ab7-223">Para obter mais informações, confira [Validação de modelo](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="39ab7-223">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="manage-head-requests-with-the-onget-handler"></a><span data-ttu-id="39ab7-224">Gerenciar solicitações HEAD com o manipulador OnGet</span><span class="sxs-lookup"><span data-stu-id="39ab7-224">Manage HEAD requests with the OnGet handler</span></span>

<span data-ttu-id="39ab7-225">As solicitações HEAD permitem recuperar os cabeçalhos de um recurso específico.</span><span class="sxs-lookup"><span data-stu-id="39ab7-225">HEAD requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="39ab7-226">Ao contrário das solicitações GET, as solicitações HEAD não retornam um corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="39ab7-226">Unlike GET requests, HEAD requests don't return a response body.</span></span>

<span data-ttu-id="39ab7-227">Geralmente, um manipulador HEAD é criado e chamado para solicitações HEAD:</span><span class="sxs-lookup"><span data-stu-id="39ab7-227">Ordinarily, a HEAD handler is created and called for HEAD requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="39ab7-228">Caso nenhum manipulador HEAD (`OnHead`) seja definido, as Páginas Razor voltam a chamar o manipulador de página GET (`OnGet`) no ASP.NET Core 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="39ab7-228">If no HEAD handler (`OnHead`) is defined, Razor Pages falls back to calling the GET page handler (`OnGet`) in ASP.NET Core 2.1 or later.</span></span> <span data-ttu-id="39ab7-229">No ASP.NET Core 2.1 e 2.2, esse comportamento ocorre com o [SetCompatibilityVersion](xref:mvc/compatibility-version) em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="39ab7-229">In ASP.NET Core 2.1 and 2.2, this behavior occurs with the [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.Configure`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="39ab7-230">Os modelos padrão geram a chamada `SetCompatibilityVersion` no ASP.NET Core 2.1 e 2.2.</span><span class="sxs-lookup"><span data-stu-id="39ab7-230">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span>

<span data-ttu-id="39ab7-231">`SetCompatibilityVersion` define de forma eficiente a opção de Páginas Razor `AllowMappingHeadRequestsToGetHandler` como `true`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-231">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="39ab7-232">Em vez de aceitar todos os 2.1 comportamentos com `SetCompatibilityVersion`, você pode explicitamente participar de comportamentos específicos.</span><span class="sxs-lookup"><span data-stu-id="39ab7-232">Rather than opting into all 2.1 behaviors with `SetCompatibilityVersion`, you can explicitly opt-in to specific behaviors.</span></span> <span data-ttu-id="39ab7-233">O código a seguir aceita as solicitações de mapeamento HEAD para o manipulador GET.</span><span class="sxs-lookup"><span data-stu-id="39ab7-233">The following code opts into the mapping HEAD requests to the GET handler.</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

::: moniker-end

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="39ab7-234">XSRF/CSRF e Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="39ab7-234">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="39ab7-235">Você não precisa escrever nenhum código para [validação antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="39ab7-235">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="39ab7-236">Validação e geração de token antifalsificação são automaticamente incluídas nas Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="39ab7-236">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="39ab7-237">Usando Layouts, parciais, modelos e auxiliares de marcas com Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="39ab7-237">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="39ab7-238">As Páginas funcionam com todos os recursos do mecanismo de exibição do Razor.</span><span class="sxs-lookup"><span data-stu-id="39ab7-238">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="39ab7-239">Layouts, parciais, modelos, auxiliares de marcas, *_ViewStart.cshtml* e *_ViewImports.cshtml* funcionam da mesma forma que funcionam exibições convencionais do Razor.</span><span class="sxs-lookup"><span data-stu-id="39ab7-239">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="39ab7-240">Organizaremos essa página aproveitando alguns desses recursos.</span><span class="sxs-lookup"><span data-stu-id="39ab7-240">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="39ab7-241">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-241">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="39ab7-242">Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-242">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

::: moniker-end

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="39ab7-243">O [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="39ab7-243">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="39ab7-244">Controla o layout de cada página (a menos que a página opte por não usar o layout).</span><span class="sxs-lookup"><span data-stu-id="39ab7-244">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="39ab7-245">Importa estruturas HTML como JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="39ab7-245">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="39ab7-246">Veja [página de layout](xref:mvc/views/layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="39ab7-246">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="39ab7-247">A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-247">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="39ab7-248">O layout está na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-248">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="39ab7-249">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="39ab7-249">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="39ab7-250">Um layout na pasta *Pages/Shared* pode ser usado em qualquer página do Razor na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-250">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="39ab7-251">O arquivo de layout deve entrar na pasta *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-251">The layout file should go in the *Pages/Shared* folder.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="39ab7-252">O layout está na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-252">The layout is in the *Pages* folder.</span></span> <span data-ttu-id="39ab7-253">As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual.</span><span class="sxs-lookup"><span data-stu-id="39ab7-253">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="39ab7-254">Um layout na pasta *Pages* pode ser usado em qualquer Página do Razor na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-254">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

::: moniker-end

<span data-ttu-id="39ab7-255">Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-255">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="39ab7-256">*Views/Shared* é um padrão de exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="39ab7-256">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="39ab7-257">As Páginas do Razor devem confiar na hierarquia de pasta e não nas convenções de caminho.</span><span class="sxs-lookup"><span data-stu-id="39ab7-257">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="39ab7-258">A pesquisa de modo de exibição de uma Página do Razor inclui a pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-258">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="39ab7-259">Os layouts, modelos e parciais que você está usando com controladores MVC e exibições do Razor convencionais *apenas funcionam*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-259">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="39ab7-260">Adicione um arquivo *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-260">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="39ab7-261">`@namespace` é explicado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="39ab7-261">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="39ab7-262">A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-262">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="39ab7-263">Quando a diretiva `@namespace` é usada explicitamente em uma página:</span><span class="sxs-lookup"><span data-stu-id="39ab7-263">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="39ab7-264">A diretiva define o namespace da página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-264">The directive sets the namespace for the page.</span></span> <span data-ttu-id="39ab7-265">A diretiva `@model` não precisa incluir o namespace.</span><span class="sxs-lookup"><span data-stu-id="39ab7-265">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="39ab7-266">Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-266">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="39ab7-267">O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-267">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="39ab7-268">Por exemplo, a classe `PageModel` *Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:</span><span class="sxs-lookup"><span data-stu-id="39ab7-268">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="39ab7-269">O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:</span><span class="sxs-lookup"><span data-stu-id="39ab7-269">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="39ab7-270">O namespace gerado para o Razor Pages *Pages/Customers/Edit.cshtml* é o mesmo que a classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-270">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="39ab7-271">`@namespace` *também funciona com exibições do Razor convencionais.*</span><span class="sxs-lookup"><span data-stu-id="39ab7-271">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="39ab7-272">O arquivo de exibição *Pages/Create.cshtml* original:</span><span class="sxs-lookup"><span data-stu-id="39ab7-272">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="39ab7-273">O arquivo de exibição *Pages/Create.cshtml* atualizado:</span><span class="sxs-lookup"><span data-stu-id="39ab7-273">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="39ab7-274">O [projeto inicial de Páginas do Razor](#rpvs17) contém o *Pages/_ValidationScriptsPartial.cshtml*, que conecta a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="39ab7-274">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="39ab7-275">Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="39ab7-275">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="39ab7-276">Geração de URL para Páginas</span><span class="sxs-lookup"><span data-stu-id="39ab7-276">URL generation for Pages</span></span>

<span data-ttu-id="39ab7-277">A página `Create`, exibida anteriormente, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="39ab7-277">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="39ab7-278">O aplicativo tem a estrutura de arquivos/pastas a seguir:</span><span class="sxs-lookup"><span data-stu-id="39ab7-278">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="39ab7-279">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="39ab7-279">*/Pages*</span></span>

  * <span data-ttu-id="39ab7-280">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="39ab7-280">*Index.cshtml*</span></span>
  * <span data-ttu-id="39ab7-281">*/Clientes*</span><span class="sxs-lookup"><span data-stu-id="39ab7-281">*/Customers*</span></span>

    * <span data-ttu-id="39ab7-282">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="39ab7-282">*Create.cshtml*</span></span>
    * <span data-ttu-id="39ab7-283">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="39ab7-283">*Edit.cshtml*</span></span>
    * <span data-ttu-id="39ab7-284">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="39ab7-284">*Index.cshtml*</span></span>

<span data-ttu-id="39ab7-285">As páginas *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* redirecionam para o *Pages/Index.cshtml* após êxito.</span><span class="sxs-lookup"><span data-stu-id="39ab7-285">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="39ab7-286">A cadeia de caracteres `/Index` faz parte do URI para acessar a página anterior.</span><span class="sxs-lookup"><span data-stu-id="39ab7-286">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="39ab7-287">A cadeia de caracteres `/Index` pode ser usada para gerar URIs para a página *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-287">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="39ab7-288">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="39ab7-288">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="39ab7-289">O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="39ab7-289">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="39ab7-290">Os exemplos anteriores de geração de URL oferecem opções avançadas e recursos funcionais para codificar uma URL.</span><span class="sxs-lookup"><span data-stu-id="39ab7-290">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="39ab7-291">A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.</span><span class="sxs-lookup"><span data-stu-id="39ab7-291">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="39ab7-292">A Geração de URL para páginas dá suporte a nomes relativos.</span><span class="sxs-lookup"><span data-stu-id="39ab7-292">URL generation for pages supports relative names.</span></span> <span data-ttu-id="39ab7-293">A tabela a seguir mostra qual página de Índice é selecionada com diferentes parâmetros `RedirectToPage` de *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="39ab7-293">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="39ab7-294">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="39ab7-294">RedirectToPage(x)</span></span>| <span data-ttu-id="39ab7-295">Página</span><span class="sxs-lookup"><span data-stu-id="39ab7-295">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="39ab7-296">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="39ab7-296">RedirectToPage("/Index")</span></span> | <span data-ttu-id="39ab7-297">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="39ab7-297">*Pages/Index*</span></span> |
| <span data-ttu-id="39ab7-298">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="39ab7-298">RedirectToPage("./Index");</span></span> | <span data-ttu-id="39ab7-299">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="39ab7-299">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="39ab7-300">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="39ab7-300">RedirectToPage("../Index")</span></span> | <span data-ttu-id="39ab7-301">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="39ab7-301">*Pages/Index*</span></span> |
| <span data-ttu-id="39ab7-302">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="39ab7-302">RedirectToPage("Index")</span></span>  | <span data-ttu-id="39ab7-303">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="39ab7-303">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="39ab7-304">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` são *nomes relativos*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-304">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="39ab7-305">O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.</span><span class="sxs-lookup"><span data-stu-id="39ab7-305">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="39ab7-306">Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa.</span><span class="sxs-lookup"><span data-stu-id="39ab7-306">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="39ab7-307">Se você usar nomes relativos para vincular entre páginas em uma pasta, você poderá renomear essa pasta.</span><span class="sxs-lookup"><span data-stu-id="39ab7-307">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="39ab7-308">Todos os links ainda funcionarão (porque eles não incluirão o nome da pasta).</span><span class="sxs-lookup"><span data-stu-id="39ab7-308">All the links still work (because they didn't include the folder name).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="39ab7-309">Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:</span><span class="sxs-lookup"><span data-stu-id="39ab7-309">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="39ab7-310">Para obter mais informações, consulte <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="39ab7-310">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="39ab7-311">Atributo ViewData</span><span class="sxs-lookup"><span data-stu-id="39ab7-311">ViewData attribute</span></span>

<span data-ttu-id="39ab7-312">Os dados podem ser passados para uma página com [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="39ab7-312">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="39ab7-313">As propriedades nos controladores ou nos modelos da Página Razor decoradas com `[ViewData]` têm seus valores armazenados e carregados em [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="39ab7-313">Properties on controllers or Razor Page models decorated with `[ViewData]` have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="39ab7-314">No exemplo a seguir, o `AboutModel` contém uma propriedade `Title` decorada com `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-314">In the following example, the `AboutModel` contains a `Title` property decorated with `[ViewData]`.</span></span> <span data-ttu-id="39ab7-315">A propriedade `Title` está definida como o título da página Sobre:</span><span class="sxs-lookup"><span data-stu-id="39ab7-315">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="39ab7-316">Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="39ab7-316">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="39ab7-317">No layout, o título é lido a partir do dicionário ViewData:</span><span class="sxs-lookup"><span data-stu-id="39ab7-317">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

## <a name="tempdata"></a><span data-ttu-id="39ab7-318">TempData</span><span class="sxs-lookup"><span data-stu-id="39ab7-318">TempData</span></span>

<span data-ttu-id="39ab7-319">O ASP.NET Core expõe a propriedade [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) em um [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="39ab7-319">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="39ab7-320">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="39ab7-320">This property stores data until it's read.</span></span> <span data-ttu-id="39ab7-321">Os métodos `Keep` e `Peek` podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="39ab7-321">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="39ab7-322">`TempData` é útil para redirecionamento nos casos em que os dados são necessários para mais de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="39ab7-322">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="39ab7-323">O atributo `[TempData]` é novo no ASP.NET Core 2.0 e tem suporte em controladores e páginas.</span><span class="sxs-lookup"><span data-stu-id="39ab7-323">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="39ab7-324">Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="39ab7-324">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="39ab7-325">A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-325">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="39ab7-326">O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-326">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="39ab7-327">Para obter mais informações, confira [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="39ab7-327">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="39ab7-328">Vários manipuladores por página</span><span class="sxs-lookup"><span data-stu-id="39ab7-328">Multiple handlers per page</span></span>

<span data-ttu-id="39ab7-329">A página a seguir gera marcação para dois manipuladores de página usando o auxiliar de marcas `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="39ab7-329">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="39ab7-330">O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente.</span><span class="sxs-lookup"><span data-stu-id="39ab7-330">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="39ab7-331">O atributo `asp-page-handler` é um complemento para `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-331">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="39ab7-332">`asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-332">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="39ab7-333">`asp-page` não foi especificado porque a amostra está vinculando à página atual.</span><span class="sxs-lookup"><span data-stu-id="39ab7-333">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="39ab7-334">O modelo de página:</span><span class="sxs-lookup"><span data-stu-id="39ab7-334">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="39ab7-335">O código anterior usa *métodos de manipulador nomeados*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-335">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="39ab7-336">Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver).</span><span class="sxs-lookup"><span data-stu-id="39ab7-336">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="39ab7-337">No exemplo anterior, os métodos de página são OnPost**JoinList**Async e OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="39ab7-337">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="39ab7-338">Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-338">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="39ab7-339">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-339">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="39ab7-340">O caminho da URL que envia a `OnPostJoinListUCAsync` é `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-340">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="39ab7-341">Rotas personalizadas</span><span class="sxs-lookup"><span data-stu-id="39ab7-341">Custom routes</span></span>

<span data-ttu-id="39ab7-342">Use a diretiva `@page` para:</span><span class="sxs-lookup"><span data-stu-id="39ab7-342">Use the `@page` directive to:</span></span>

* <span data-ttu-id="39ab7-343">Especifique uma rota personalizada para uma página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-343">Specify a custom route to a page.</span></span> <span data-ttu-id="39ab7-344">Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-344">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="39ab7-345">Acrescente segmentos à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-345">Append segments to a page's default route.</span></span> <span data-ttu-id="39ab7-346">Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-346">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="39ab7-347">Acrescente parâmetros à rota padrão de uma página.</span><span class="sxs-lookup"><span data-stu-id="39ab7-347">Append parameters to a page's default route.</span></span> <span data-ttu-id="39ab7-348">Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-348">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="39ab7-349">Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho.</span><span class="sxs-lookup"><span data-stu-id="39ab7-349">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="39ab7-350">Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-350">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="39ab7-351">Você pode alterar a cadeia de caracteres de consulta `?handler=JoinList` na URL para um segmento de rota `/JoinList` ao especificar o modelo de rota `@page "{handler?}"`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-351">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="39ab7-352">Se você não deseja a cadeia de consulta `?handler=JoinList` na URL, você pode alterar a rota para colocar o nome do manipulador na parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="39ab7-352">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="39ab7-353">Você pode personalizar a rota adicionando um modelo de rota entre aspas duplas após a diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-353">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="39ab7-354">Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `http://localhost:5000/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-354">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="39ab7-355">O caminho da URL que envia a `OnPostJoinListUCAsync` é `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="39ab7-355">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="39ab7-356">O `?` após `handler` significa que o parâmetro de rota é opcional.</span><span class="sxs-lookup"><span data-stu-id="39ab7-356">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="39ab7-357">Configuração e definições</span><span class="sxs-lookup"><span data-stu-id="39ab7-357">Configuration and settings</span></span>

<span data-ttu-id="39ab7-358">Para configurar opções avançadas, use o método de extensão `AddRazorPagesOptions` no construtor de MVC:</span><span class="sxs-lookup"><span data-stu-id="39ab7-358">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="39ab7-359">No momento, você pode usar o `RazorPagesOptions` para definir o diretório raiz para páginas ou adicionar as convenções de modelo de aplicativo para páginas.</span><span class="sxs-lookup"><span data-stu-id="39ab7-359">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="39ab7-360">Permitiremos mais extensibilidade dessa maneira no futuro.</span><span class="sxs-lookup"><span data-stu-id="39ab7-360">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="39ab7-361">Para pré-compilar exibições, consulte [Compilação de exibição do Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="39ab7-361">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="39ab7-362">[Baixar ou exibir código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="39ab7-362">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="39ab7-363">Consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução.</span><span class="sxs-lookup"><span data-stu-id="39ab7-363">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="39ab7-364">Especificar que as Páginas Razor estão na raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="39ab7-364">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="39ab7-365">Por padrão, as Páginas Razor estão na raiz do diretório */Pages*.</span><span class="sxs-lookup"><span data-stu-id="39ab7-365">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="39ab7-366">Adicione [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) em [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que as Páginas Razor estão na raiz do conteúdo ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="39ab7-366">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the content root ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="39ab7-367">Especificar que as Páginas Razor estão em um diretório raiz personalizado</span><span class="sxs-lookup"><span data-stu-id="39ab7-367">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="39ab7-368">Adicione [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) em [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que as Páginas Razor estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):</span><span class="sxs-lookup"><span data-stu-id="39ab7-368">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="39ab7-369">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="39ab7-369">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
