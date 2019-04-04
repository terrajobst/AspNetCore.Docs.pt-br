---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Obtenha uma introdução ao ASP.NET Core, uma estrutura de software livre, plataforma cruzada e alto desempenho para a criação de aplicativos modernos conectados à Internet e baseados em nuvem.
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: index
ms.openlocfilehash: baf8557b29c30f6dbeb6a4ff5d50c83d07160a51
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320232"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="64029-103">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64029-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="64029-104">Por [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="64029-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="64029-105">O ASP.NET Core é uma estrutura de [software livre](https://github.com/aspnet/home), de multiplaforma e alto desempenho para a criação de aplicativos modernos conectados à Internet e baseados em nuvem.</span><span class="sxs-lookup"><span data-stu-id="64029-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="64029-106">Com o ASP.NET Core, você pode:</span><span class="sxs-lookup"><span data-stu-id="64029-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="64029-107">Compilar aplicativos e serviços Web, aplicativos [IoT](https://www.microsoft.com/internet-of-things/) e back-ends móveis.</span><span class="sxs-lookup"><span data-stu-id="64029-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="64029-108">Usar suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="64029-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="64029-109">Implantar na nuvem ou local.</span><span class="sxs-lookup"><span data-stu-id="64029-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="64029-110">Executar no [.NET Core ou no .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="64029-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-to-use-aspnet-core"></a><span data-ttu-id="64029-111">Por que usar o ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="64029-111">Why to use ASP.NET Core</span></span>

<span data-ttu-id="64029-112">Milhões de desenvolvedores usaram (e continuam usando) o [ASP.NET 4.x](/aspnet/overview) para criar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="64029-112">Millions of developers have used (and continue to use) [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="64029-113">O ASP.NET Core é uma reformulação do ASP.NET 4.x, com alterações de arquitetura que resultam em uma estrutura mais enxuta e modular.</span><span class="sxs-lookup"><span data-stu-id="64029-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="64029-114">Compilar APIs Web e uma interface do usuário da Web usando o ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="64029-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="64029-115">O ASP.NET Core MVC fornece recursos que ajudam você a compilar [APIs Web](xref:tutorials/first-web-api) e [aplicativos Web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="64029-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="64029-116">O [padrão MVC (Model-View-Controller)](xref:mvc/overview) ajuda a tornar as APIs Web e os aplicativos Web testáveis.</span><span class="sxs-lookup"><span data-stu-id="64029-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="64029-117">O [Razor Pages](xref:razor-pages/index) é um modelo de programação baseado em página que torna mais fácil e produtiva a criação da interface do usuário da Web.</span><span class="sxs-lookup"><span data-stu-id="64029-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="64029-118">A [marcação Razor](xref:mvc/views/razor) fornece uma sintaxe produtiva para [Páginas Razor](xref:razor-pages/index) e as [Exibições do MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="64029-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="64029-119">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="64029-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="64029-120">O suporte interno para [vários formatos de dados e negociação de conteúdo](xref:web-api/advanced/formatting) permite que as APIs Web alcancem uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.</span><span class="sxs-lookup"><span data-stu-id="64029-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="64029-121">O [model binding](xref:mvc/models/model-binding) mapeia automaticamente os dados de solicitações HTTP para os parâmetros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="64029-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="64029-122">A [Validação de Modelos](xref:mvc/models/validation) executa automaticamente a validação no lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="64029-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="64029-123">Desenvolvimento do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="64029-123">Client-side development</span></span>

<span data-ttu-id="64029-124">O ASP.NET Core integra-se perfeitamente a estruturas e bibliotecas conhecidas do lado do cliente, incluindo [Componentes Razor](xref:razor-components/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="64029-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Razor Components](xref:razor-components/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="64029-125">Para obter mais informações, consulte [Componentes Razor](xref:razor-components/index) e os tópicos relacionados em *Desenvolvimento do lado do cliente*.</span><span class="sxs-lookup"><span data-stu-id="64029-125">For more information, see [Razor Components](xref:razor-components/index) and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="64029-126">ASP.NET Core direcionado para o .NET Framework</span><span class="sxs-lookup"><span data-stu-id="64029-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="64029-127">O ASP.NET Core 2.x pode ser direcionado para o .NET Core ou ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="64029-127">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="64029-128">Os aplicativos do ASP.NET Core direcionados ao .NET Framework não são multiplataforma,&mdash; são executados somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="64029-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="64029-129">Em geral, o ASP.NET Core 2.x é composto de bibliotecas do [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="64029-129">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="64029-130">Aplicativos criados com o .NET Standard 2.0 são executados em qualquer lugar com suporte para ele.</span><span class="sxs-lookup"><span data-stu-id="64029-130">Apps written with .NET Standard 2.0 run anywhere that .NET Standard 2.0 is supported.</span></span>

<span data-ttu-id="64029-131">O ASP.NET Core 2.x dá suporte para as versões do .NET Framework compatíveis com o .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="64029-131">ASP.NET Core 2.x is supported on .NET Framework versions compatible with .NET Standard 2.0:</span></span>

* <span data-ttu-id="64029-132">O .NET Framework 4.7.1 e versões posteriores são fortemente recomendados.</span><span class="sxs-lookup"><span data-stu-id="64029-132">.NET Framework 4.7.1 and later is strongly recommended.</span></span>
* <span data-ttu-id="64029-133">.NET Framework 4.6.1 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="64029-133">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="64029-134">O ASP.NET Core 3.0 e posterior somente executará no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="64029-134">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="64029-135">Para obter mais detalhes sobre essa alteração, confira [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Uma primeira análise das alterações no ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="64029-135">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="64029-136">Há várias vantagens em direcionar para o .NET Core, e essas vantagens aumentam com cada versão.</span><span class="sxs-lookup"><span data-stu-id="64029-136">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="64029-137">Algumas vantagens do .NET Core em relação ao .NET Framework incluem:</span><span class="sxs-lookup"><span data-stu-id="64029-137">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="64029-138">Multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="64029-138">Cross-platform.</span></span> <span data-ttu-id="64029-139">É executado no Windows, no Linux e no macOS.</span><span class="sxs-lookup"><span data-stu-id="64029-139">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="64029-140">Desempenho aprimorado</span><span class="sxs-lookup"><span data-stu-id="64029-140">Improved performance</span></span>
* <span data-ttu-id="64029-141">Controle de versão lado a lado</span><span class="sxs-lookup"><span data-stu-id="64029-141">Side-by-side versioning</span></span>
* <span data-ttu-id="64029-142">Novas APIs</span><span class="sxs-lookup"><span data-stu-id="64029-142">New APIs</span></span>
* <span data-ttu-id="64029-143">Código Aberto</span><span class="sxs-lookup"><span data-stu-id="64029-143">Open source</span></span>

<span data-ttu-id="64029-144">Estamos trabalhando duro para diminuir a diferença de API entre o .NET Framework e o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="64029-144">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="64029-145">O [Pacote de Compatibilidade do Windows](/dotnet/core/porting/windows-compat-pack) disponibilizou milhares de APIs exclusivas do Windows no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="64029-145">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="64029-146">Essas APIs não estavam disponíveis no .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="64029-146">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="64029-147">Caminho de aprendizado recomendado</span><span class="sxs-lookup"><span data-stu-id="64029-147">Recommended learning path</span></span>

<span data-ttu-id="64029-148">Recomendamos a seguinte sequência de tutoriais e artigos para obter uma introdução ao desenvolvimento de aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="64029-148">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="64029-149">Siga um tutorial para o tipo de aplicativo que você deseja desenvolver ou manter:</span><span class="sxs-lookup"><span data-stu-id="64029-149">Follow a tutorial for the type of app you want to develop or maintain:</span></span>

   |<span data-ttu-id="64029-150">Tipo de aplicativo</span><span class="sxs-lookup"><span data-stu-id="64029-150">App type</span></span>  |<span data-ttu-id="64029-151">Cenário</span><span class="sxs-lookup"><span data-stu-id="64029-151">Scenario</span></span>  |<span data-ttu-id="64029-152">Tutorial</span><span class="sxs-lookup"><span data-stu-id="64029-152">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="64029-153">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="64029-153">Web app</span></span>       | <span data-ttu-id="64029-154">Para novo desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="64029-154">For new development</span></span>        |[<span data-ttu-id="64029-155">Introdução a Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="64029-155">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="64029-156">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="64029-156">Web app</span></span>       | <span data-ttu-id="64029-157">Para manter um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="64029-157">For maintaining an MVC app</span></span> |[<span data-ttu-id="64029-158">Introdução ao MVC</span><span class="sxs-lookup"><span data-stu-id="64029-158">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="64029-159">API Web</span><span class="sxs-lookup"><span data-stu-id="64029-159">Web API</span></span>       |                            |<span data-ttu-id="64029-160">[Criar uma API Web](xref:tutorials/first-web-api)\*</span><span class="sxs-lookup"><span data-stu-id="64029-160">[Create a web API](xref:tutorials/first-web-api)\*</span></span>  |
   |<span data-ttu-id="64029-161">Aplicativo em tempo real</span><span class="sxs-lookup"><span data-stu-id="64029-161">Real-time app</span></span> |                            |[<span data-ttu-id="64029-162">Introdução ao SignalR</span><span class="sxs-lookup"><span data-stu-id="64029-162">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="64029-163">Siga um tutorial que mostra como fazer o acesso a dados básicos:</span><span class="sxs-lookup"><span data-stu-id="64029-163">Follow a tutorial that shows how to do basic data access:</span></span>

   |<span data-ttu-id="64029-164">Cenário</span><span class="sxs-lookup"><span data-stu-id="64029-164">Scenario</span></span>  |<span data-ttu-id="64029-165">Tutorial</span><span class="sxs-lookup"><span data-stu-id="64029-165">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="64029-166">Para novo desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="64029-166">For new development</span></span>        |[<span data-ttu-id="64029-167">Razor Pages com o Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="64029-167">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="64029-168">Para manter um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="64029-168">For maintaining an MVC app</span></span> |[<span data-ttu-id="64029-169">MVC com o Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="64029-169">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

1. <span data-ttu-id="64029-170">Leia uma visão geral dos recursos do ASP.NET Core que se aplicam a todos os tipos de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="64029-170">Read an overview of ASP.NET Core features that apply to all app types:</span></span>

   * [<span data-ttu-id="64029-171">Conceitos básicos</span><span class="sxs-lookup"><span data-stu-id="64029-171">Fundamentals</span></span>](xref:fundamentals/index)

1. <span data-ttu-id="64029-172">Pesquise no Sumário outros tópicos de interesse.</span><span class="sxs-lookup"><span data-stu-id="64029-172">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="64029-173">\* Há um novo [tutorial de API Web que você segue totalmente no navegador](https://docs.microsoft.com/learn/modules/build-web-api-net-core), sem necessidade de instalação local do IDE.</span><span class="sxs-lookup"><span data-stu-id="64029-173">\* There is a new [web API tutorial that you follow entirely in the browser](https://docs.microsoft.com/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span>  <span data-ttu-id="64029-174">O código é executado em um [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) e [curl](https://curl.haxx.se/) é usado para teste.</span><span class="sxs-lookup"><span data-stu-id="64029-174">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="64029-175">Como baixar uma amostra</span><span class="sxs-lookup"><span data-stu-id="64029-175">How to download a sample</span></span>

<span data-ttu-id="64029-176">Muitos dos artigos e tutoriais incluem links para exemplos de código.</span><span class="sxs-lookup"><span data-stu-id="64029-176">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="64029-177">[Baixe o arquivo zip do repositório ASP.NET](https://codeload.github.com/aspnet/Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="64029-177">[Download the ASP.NET repository zip file](https://codeload.github.com/aspnet/Docs/zip/master).</span></span>
1. <span data-ttu-id="64029-178">Descompacte o arquivo *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="64029-178">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="64029-179">Use a URL no link de exemplo para ajudá-lo a navegar até o diretório de exemplo.</span><span class="sxs-lookup"><span data-stu-id="64029-179">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="64029-180">Diretivas do pré-processador no código de exemplo</span><span class="sxs-lookup"><span data-stu-id="64029-180">Preprocessor directives in sample code</span></span>

<span data-ttu-id="64029-181">Para demonstrar vários cenários, os aplicativos de exemplo usam as instruções C# `#define` e `#if-#else/#elif-#endif` para compilar e executar diferentes seções de código de exemplo de forma seletiva.</span><span class="sxs-lookup"><span data-stu-id="64029-181">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` C# statements to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="64029-182">Para esses exemplos que usam essa abordagem, defina a instrução `#define` na parte superior dos arquivos C# para o símbolo associado ao cenário que deseja executar.</span><span class="sxs-lookup"><span data-stu-id="64029-182">For those samples that make use of this approach, set the `#define` statement at the top of the C# files to the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="64029-183">Alguns exemplos podem exigir que você defina o símbolo na parte superior de vários arquivos para executar um cenário.</span><span class="sxs-lookup"><span data-stu-id="64029-183">Some samples require setting the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="64029-184">Por exemplo, a seguinte lista de símbolo `#define` indica que quatro cenários estão disponíveis (um cenário por símbolo).</span><span class="sxs-lookup"><span data-stu-id="64029-184">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="64029-185">A configuração da amostra atual executa o cenário `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="64029-185">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="64029-186">Para alterar a amostra que executará o cenário `ExpandDefault`, defina o símbolo `ExpandDefault` e deixe os símbolos restantes comentados de fora:</span><span class="sxs-lookup"><span data-stu-id="64029-186">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="64029-187">Para obter mais informações sobre como usar [diretivas de pré-processador C#](/dotnet/csharp/language-reference/preprocessor-directives/) para compilar seletivamente as seções de código, consulte [#define (Referência C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) e [#if (Referência C#) ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="64029-187">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="64029-188">Regiões no código de exemplo</span><span class="sxs-lookup"><span data-stu-id="64029-188">Regions in sample code</span></span>

<span data-ttu-id="64029-189">Alguns aplicativos de exemplo contém seções de código cercadas pelas instruções C# [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) e [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion).</span><span class="sxs-lookup"><span data-stu-id="64029-189">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# statements.</span></span> <span data-ttu-id="64029-190">O sistema de build de documentação injeta essas regiões nos tópicos renderizados da documentação.</span><span class="sxs-lookup"><span data-stu-id="64029-190">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="64029-191">Os nomes das regiões geralmente contêm a palavra "snippet".</span><span class="sxs-lookup"><span data-stu-id="64029-191">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="64029-192">O exemplo a seguir mostra uma região chamada `snippet_FilterInCode`:</span><span class="sxs-lookup"><span data-stu-id="64029-192">The following example shows a region named `snippet_FilterInCode`:</span></span>

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

<span data-ttu-id="64029-193">O snippet de código C# precedente é referenciado no arquivo de markdown do tópico com a seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="64029-193">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_FilterInCode)]
```

<span data-ttu-id="64029-194">Você pode ignorar (ou remover) com segurança as instruções `#region` e `#endregion` que envolvem o código.</span><span class="sxs-lookup"><span data-stu-id="64029-194">You may safely ignore (or remove) the `#region` and `#endregion` statements that surround the code.</span></span> <span data-ttu-id="64029-195">Não altere o código dentro dessas instruções se você planeja executar os cenários de exemplo descritos no tópico.</span><span class="sxs-lookup"><span data-stu-id="64029-195">Don't alter the code within these statements if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="64029-196">Fique à vontade para alterar o código ao experimentar com outros cenários.</span><span class="sxs-lookup"><span data-stu-id="64029-196">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="64029-197">Para obter mais informações, veja [Contribuir para a documentação do ASP.NET: snippets de código](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="64029-197">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="64029-198">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="64029-198">Next steps</span></span>

<span data-ttu-id="64029-199">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="64029-199">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="64029-200">Conceitos básicos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64029-200">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="64029-201">O [Community Standup semanal do ASP.NET](https://live.asp.net/) aborda o progresso e os planos da equipe.</span><span class="sxs-lookup"><span data-stu-id="64029-201">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="64029-202">Ele apresenta o novo software de terceiros e blogs.</span><span class="sxs-lookup"><span data-stu-id="64029-202">It features new blogs and third-party software.</span></span>
