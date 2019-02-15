---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Obtenha uma introdução ao ASP.NET Core, uma estrutura de software livre, plataforma cruzada e alto desempenho para a criação de aplicativos modernos conectados à Internet e baseados em nuvem.
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: index
ms.openlocfilehash: c3f07814bfab19a0f070e0b48b0d2ef6cfc1594e
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248154"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="3fd87-103">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fd87-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="3fd87-104">Por [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="3fd87-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="3fd87-105">O ASP.NET Core é uma estrutura de [software livre](https://github.com/aspnet/home), de multiplaforma e alto desempenho para a criação de aplicativos modernos conectados à Internet e baseados em nuvem.</span><span class="sxs-lookup"><span data-stu-id="3fd87-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="3fd87-106">Com o ASP.NET Core, você pode:</span><span class="sxs-lookup"><span data-stu-id="3fd87-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="3fd87-107">Compilar aplicativos e serviços Web, aplicativos [IoT](https://www.microsoft.com/internet-of-things/) e back-ends móveis.</span><span class="sxs-lookup"><span data-stu-id="3fd87-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="3fd87-108">Usar suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="3fd87-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="3fd87-109">Implantar na nuvem ou local.</span><span class="sxs-lookup"><span data-stu-id="3fd87-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="3fd87-110">Executar no [.NET Core ou no .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="3fd87-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-use-aspnet-core"></a><span data-ttu-id="3fd87-111">Por que usar o ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="3fd87-111">Why use ASP.NET Core?</span></span>

<span data-ttu-id="3fd87-112">Milhões de desenvolvedores usaram (e continuam usando) o [ASP.NET 4.x](/aspnet/overview) para criar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="3fd87-112">Millions of developers have used (and continue to use) [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="3fd87-113">O ASP.NET Core é uma reformulação do ASP.NET 4.x, com alterações de arquitetura que resultam em uma estrutura mais enxuta e modular.</span><span class="sxs-lookup"><span data-stu-id="3fd87-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="3fd87-114">Compilar APIs Web e uma interface do usuário da Web usando o ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="3fd87-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="3fd87-115">O ASP.NET Core MVC fornece recursos que ajudam você a compilar [APIs Web](xref:tutorials/first-web-api) e [aplicativos Web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="3fd87-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="3fd87-116">O [padrão MVC (Model-View-Controller)](xref:mvc/overview) ajuda a tornar as APIs Web e os aplicativos Web testáveis.</span><span class="sxs-lookup"><span data-stu-id="3fd87-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="3fd87-117">O [Razor Pages](xref:razor-pages/index) é um modelo de programação baseado em página que torna mais fácil e produtiva a criação da interface do usuário da Web.</span><span class="sxs-lookup"><span data-stu-id="3fd87-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="3fd87-118">A [marcação Razor](xref:mvc/views/razor) fornece uma sintaxe produtiva para [Páginas Razor](xref:razor-pages/index) e as [Exibições do MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="3fd87-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="3fd87-119">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="3fd87-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="3fd87-120">O suporte interno para [vários formatos de dados e negociação de conteúdo](xref:web-api/advanced/formatting) permite que as APIs Web alcancem uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.</span><span class="sxs-lookup"><span data-stu-id="3fd87-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="3fd87-121">O [model binding](xref:mvc/models/model-binding) mapeia automaticamente os dados de solicitações HTTP para os parâmetros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="3fd87-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="3fd87-122">A [Validação de Modelos](xref:mvc/models/validation) executa automaticamente a validação no lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="3fd87-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="3fd87-123">Desenvolvimento do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="3fd87-123">Client-side development</span></span>

<span data-ttu-id="3fd87-124">O ASP.NET Core integra-se perfeitamente a estruturas e bibliotecas conhecidas do lado do cliente, incluindo [Componentes Razor](xref:razor-components/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="3fd87-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Razor Components](xref:razor-components/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="3fd87-125">Para obter mais informações, consulte [Componentes Razor](xref:razor-components/index) e os tópicos relacionados em *Desenvolvimento do lado do cliente*.</span><span class="sxs-lookup"><span data-stu-id="3fd87-125">For more information, see [Razor Components](xref:razor-components/index) and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="3fd87-126">ASP.NET Core direcionado para o .NET Framework</span><span class="sxs-lookup"><span data-stu-id="3fd87-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="3fd87-127">O ASP.NET Core 2.x pode ser direcionado para o .NET Core ou ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="3fd87-127">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="3fd87-128">Os aplicativos do ASP.NET Core direcionados ao .NET Framework não são multiplataforma,&mdash; são executados somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="3fd87-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="3fd87-129">Em geral, o ASP.NET Core 2.x é composto de bibliotecas do [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="3fd87-129">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="3fd87-130">Aplicativos criados com o .NET Standard 2.0 são executados em qualquer lugar com suporte para ele.</span><span class="sxs-lookup"><span data-stu-id="3fd87-130">Apps written with .NET Standard 2.0 run anywhere that .NET Standard 2.0 is supported.</span></span>

<span data-ttu-id="3fd87-131">O ASP.NET Core 2.x dá suporte para as versões do .NET Framework compatíveis com o .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="3fd87-131">ASP.NET Core 2.x is supported on .NET Framework versions compatible with .NET Standard 2.0:</span></span>

* <span data-ttu-id="3fd87-132">O .NET Framework 4.7.1 e versões posteriores são fortemente recomendados.</span><span class="sxs-lookup"><span data-stu-id="3fd87-132">.NET Framework 4.7.1 and later is strongly recommended.</span></span>
* <span data-ttu-id="3fd87-133">.NET Framework 4.6.1 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="3fd87-133">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="3fd87-134">O ASP.NET Core 3.0 e posterior somente executará no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3fd87-134">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="3fd87-135">Para obter mais detalhes sobre essa alteração, confira [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Uma primeira análise das alterações no ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="3fd87-135">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="3fd87-136">Há várias vantagens em direcionar para o .NET Core, e essas vantagens aumentam com cada versão.</span><span class="sxs-lookup"><span data-stu-id="3fd87-136">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="3fd87-137">Algumas vantagens do .NET Core em relação ao .NET Framework incluem:</span><span class="sxs-lookup"><span data-stu-id="3fd87-137">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="3fd87-138">Multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="3fd87-138">Cross-platform.</span></span> <span data-ttu-id="3fd87-139">É executado no Windows, no Linux e no macOS.</span><span class="sxs-lookup"><span data-stu-id="3fd87-139">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="3fd87-140">Desempenho aprimorado</span><span class="sxs-lookup"><span data-stu-id="3fd87-140">Improved performance</span></span>
* <span data-ttu-id="3fd87-141">Controle de versão lado a lado</span><span class="sxs-lookup"><span data-stu-id="3fd87-141">Side-by-side versioning</span></span>
* <span data-ttu-id="3fd87-142">Novas APIs</span><span class="sxs-lookup"><span data-stu-id="3fd87-142">New APIs</span></span>
* <span data-ttu-id="3fd87-143">Código Aberto</span><span class="sxs-lookup"><span data-stu-id="3fd87-143">Open source</span></span>

<span data-ttu-id="3fd87-144">Estamos trabalhando duro para diminuir a diferença de API entre o .NET Framework e o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3fd87-144">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="3fd87-145">O [Pacote de Compatibilidade do Windows](/dotnet/core/porting/windows-compat-pack) disponibilizou milhares de APIs exclusivas do Windows no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3fd87-145">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="3fd87-146">Essas APIs não estavam disponíveis no .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="3fd87-146">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="3fd87-147">Como baixar uma amostra</span><span class="sxs-lookup"><span data-stu-id="3fd87-147">How to download a sample</span></span>

<span data-ttu-id="3fd87-148">Muitos dos artigos e tutoriais incluem links para exemplos de código.</span><span class="sxs-lookup"><span data-stu-id="3fd87-148">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="3fd87-149">[Baixe o arquivo zip do repositório ASP.NET](https://codeload.github.com/aspnet/Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="3fd87-149">[Download the ASP.NET repository zip file](https://codeload.github.com/aspnet/Docs/zip/master).</span></span>
1. <span data-ttu-id="3fd87-150">Descompacte o arquivo *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="3fd87-150">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="3fd87-151">Use a URL no link de exemplo para ajudá-lo a navegar até o diretório de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3fd87-151">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="3fd87-152">Diretivas do pré-processador no código de exemplo</span><span class="sxs-lookup"><span data-stu-id="3fd87-152">Preprocessor directives in sample code</span></span>

<span data-ttu-id="3fd87-153">Para demonstrar vários cenários, os aplicativos de exemplo usam as instruções C# `#define` e `#if-#else/#elif-#endif` para compilar e executar diferentes seções de código de exemplo de forma seletiva.</span><span class="sxs-lookup"><span data-stu-id="3fd87-153">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` C# statements to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="3fd87-154">Para esses exemplos que usam essa abordagem, defina a instrução `#define` na parte superior dos arquivos C# para o símbolo associado ao cenário que deseja executar.</span><span class="sxs-lookup"><span data-stu-id="3fd87-154">For those samples that make use of this approach, set the `#define` statement at the top of the C# files to the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="3fd87-155">Alguns exemplos podem exigir que você defina o símbolo na parte superior de vários arquivos para executar um cenário.</span><span class="sxs-lookup"><span data-stu-id="3fd87-155">Some samples require setting the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="3fd87-156">Por exemplo, a seguinte lista de símbolo `#define` indica que quatro cenários estão disponíveis (um cenário por símbolo).</span><span class="sxs-lookup"><span data-stu-id="3fd87-156">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="3fd87-157">A configuração da amostra atual executa o cenário `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="3fd87-157">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="3fd87-158">Para alterar a amostra que executará o cenário `ExpandDefault`, defina o símbolo `ExpandDefault` e deixe os símbolos restantes comentados de fora:</span><span class="sxs-lookup"><span data-stu-id="3fd87-158">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="3fd87-159">Para obter mais informações sobre como usar [diretivas de pré-processador C#](/dotnet/csharp/language-reference/preprocessor-directives/) para compilar seletivamente as seções de código, consulte [#define (Referência C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) e [#if (Referência C#) ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="3fd87-159">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="3fd87-160">Regiões no código de exemplo</span><span class="sxs-lookup"><span data-stu-id="3fd87-160">Regions in sample code</span></span>

<span data-ttu-id="3fd87-161">Alguns aplicativos de exemplo contém seções de código cercadas pelas instruções C# [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) e [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion).</span><span class="sxs-lookup"><span data-stu-id="3fd87-161">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# statements.</span></span> <span data-ttu-id="3fd87-162">O sistema de build de documentação injeta essas regiões nos tópicos renderizados da documentação.</span><span class="sxs-lookup"><span data-stu-id="3fd87-162">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="3fd87-163">Os nomes das regiões geralmente contêm a palavra "snippet".</span><span class="sxs-lookup"><span data-stu-id="3fd87-163">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="3fd87-164">O exemplo a seguir mostra uma região chamada `snippet_FilterInCode`:</span><span class="sxs-lookup"><span data-stu-id="3fd87-164">The following example shows a region named `snippet_FilterInCode`:</span></span>

```csharp
#region snippet_FilterInCode
WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
        logging.AddFilter("System", LogLevel.Debug)
            .AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Trace))
            .Build();
#endregion
```

<span data-ttu-id="3fd87-165">O snippet de código C# precedente é referenciado no arquivo de markdown do tópico com a seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="3fd87-165">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_FilterInCode)]
```

<span data-ttu-id="3fd87-166">Você pode ignorar (ou remover) com segurança as instruções `#region` e `#endregion` que envolvem o código.</span><span class="sxs-lookup"><span data-stu-id="3fd87-166">You may safely ignore (or remove) the `#region` and `#endregion` statements that surround the code.</span></span> <span data-ttu-id="3fd87-167">Não altere o código dentro dessas instruções se você planeja executar os cenários de exemplo descritos no tópico.</span><span class="sxs-lookup"><span data-stu-id="3fd87-167">Don't alter the code within these statements if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="3fd87-168">Fique à vontade para alterar o código ao experimentar com outros cenários.</span><span class="sxs-lookup"><span data-stu-id="3fd87-168">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="3fd87-169">Para obter mais informações, veja [Contribuir para a documentação do ASP.NET: snippets de código](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="3fd87-169">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="3fd87-170">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="3fd87-170">Next steps</span></span>

<span data-ttu-id="3fd87-171">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="3fd87-171">For more information, see the following resources:</span></span>

* [<span data-ttu-id="3fd87-172">Introdução a Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="3fd87-172">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="3fd87-173">Conceitos básicos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fd87-173">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="3fd87-174">O [Community Standup semanal do ASP.NET](https://live.asp.net/) aborda o progresso e os planos da equipe.</span><span class="sxs-lookup"><span data-stu-id="3fd87-174">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="3fd87-175">Ele apresenta o novo software de terceiros e blogs.</span><span class="sxs-lookup"><span data-stu-id="3fd87-175">It features new blogs and third-party software.</span></span>
