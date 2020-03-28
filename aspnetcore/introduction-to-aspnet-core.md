---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Obtenha uma introdução ao ASP.NET Core, uma estrutura de software livre, plataforma cruzada e alto desempenho para a criação de aplicativos modernos conectados à Internet e baseados em nuvem.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: fd7fa9dd70502f51222e457dd887ef668d377278
ms.sourcegitcommit: 4b166b49ec557a03f99f872dd069ca5e56faa524
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366655"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="a8e0e-103">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8e0e-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="a8e0e-104">Por [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="a8e0e-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="a8e0e-105">O ASP.NET Core é uma estrutura de [software livre](https://github.com/aspnet/home), de multiplaforma e alto desempenho para a criação de aplicativos modernos conectados à Internet e baseados em nuvem.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="a8e0e-106">Com o ASP.NET Core, você pode:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="a8e0e-107">Compilar aplicativos e serviços Web, aplicativos [IoT](https://www.microsoft.com/internet-of-things/) e back-ends móveis.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="a8e0e-108">Usar suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="a8e0e-109">Implantar na nuvem ou local.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="a8e0e-110">Executar no [.NET Core ou no .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="a8e0e-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="a8e0e-111">Por que escolher o ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="a8e0e-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="a8e0e-112">Milhões de desenvolvedores usam ou usaram o [ASP.NET 4. x](/aspnet/overview) para criar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="a8e0e-113">O ASP.NET Core é uma reformulação do ASP.NET 4.x, com alterações de arquitetura que resultam em uma estrutura mais enxuta e modular.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="a8e0e-114">Compilar APIs Web e uma interface do usuário da Web usando o ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a8e0e-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="a8e0e-115">O ASP.NET Core MVC fornece recursos que ajudam você a compilar [APIs Web](xref:tutorials/first-web-api) e [aplicativos Web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="a8e0e-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="a8e0e-116">O [padrão MVC (Model-View-Controller)](xref:mvc/overview) ajuda a tornar as APIs Web e os aplicativos Web testáveis.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="a8e0e-117">O [Razor Pages](xref:razor-pages/index) é um modelo de programação baseado em página que torna mais fácil e produtiva a criação da interface do usuário da Web.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="a8e0e-118">A [marcação Razor](xref:mvc/views/razor) fornece uma sintaxe produtiva para [Páginas Razor](xref:razor-pages/index) e as [Exibições do MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="a8e0e-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="a8e0e-119">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="a8e0e-120">O suporte interno para [vários formatos de dados e negociação de conteúdo](xref:web-api/advanced/formatting) permite que as APIs Web alcancem uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="a8e0e-121">O [model binding](xref:mvc/models/model-binding) mapeia automaticamente os dados de solicitações HTTP para os parâmetros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="a8e0e-122">A [Validação de Modelos](xref:mvc/models/validation) executa automaticamente a validação no lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="a8e0e-123">Desenvolvimento no lado do cliente</span><span class="sxs-lookup"><span data-stu-id="a8e0e-123">Client-side development</span></span>

<span data-ttu-id="a8e0e-124">O ASP.NET Core integra-se perfeitamente com estruturas conhecidas do lado do cliente e bibliotecas, incluindo [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="a8e0e-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="a8e0e-125">Para obter mais informações, confira <xref:blazor/index> e os tópicos relacionados em *Desenvolvimento do lado do cliente*.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="a8e0e-126">ASP.NET Core direcionado para o .NET Framework</span><span class="sxs-lookup"><span data-stu-id="a8e0e-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="a8e0e-127">O ASP.NET Core 2.x pode ser direcionado para o .NET Core ou ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-127">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="a8e0e-128">Os aplicativos do ASP.NET Core direcionados ao .NET Framework não são multiplataforma,&mdash; são executados somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="a8e0e-129">Em geral, o ASP.NET Core 2.x é composto de bibliotecas do [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="a8e0e-129">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="a8e0e-130">As bibliotecas gravadas com .NET Standard 2.0 podem ser executadas em qualquer [plataforma .NET que implemente o .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="a8e0e-130">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="a8e0e-131">O ASP.NET Core 2.x dá suporte para as versões do .NET Framework que implementam o .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-131">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="a8e0e-132">É altamente recomendável usar a versão mais recente do .NET framework.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-132">.NET Framework latest version is strongly recommended.</span></span>
* <span data-ttu-id="a8e0e-133">.NET Framework 4.6.1 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-133">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="a8e0e-134">O ASP.NET Core 3.0 e posterior somente executará no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-134">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="a8e0e-135">Para obter mais detalhes sobre essa alteração, confira [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Uma primeira análise das alterações no ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="a8e0e-135">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="a8e0e-136">Há várias vantagens em direcionar para o .NET Core, e essas vantagens aumentam com cada versão.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-136">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="a8e0e-137">Algumas vantagens do .NET Core em relação ao .NET Framework incluem:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-137">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="a8e0e-138">Multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-138">Cross-platform.</span></span> <span data-ttu-id="a8e0e-139">É executado no Windows, no Linux e no macOS.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-139">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="a8e0e-140">desempenho aprimorado</span><span class="sxs-lookup"><span data-stu-id="a8e0e-140">Improved performance</span></span>
* [<span data-ttu-id="a8e0e-141">Controle de versão lado a lado</span><span class="sxs-lookup"><span data-stu-id="a8e0e-141">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="a8e0e-142">Novas APIs</span><span class="sxs-lookup"><span data-stu-id="a8e0e-142">New APIs</span></span>
* <span data-ttu-id="a8e0e-143">Código-fonte aberto</span><span class="sxs-lookup"><span data-stu-id="a8e0e-143">Open source</span></span>

<span data-ttu-id="a8e0e-144">Estamos trabalhando duro para diminuir a diferença de API entre o .NET Framework e o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-144">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="a8e0e-145">O [Pacote de Compatibilidade do Windows](/dotnet/core/porting/windows-compat-pack) disponibilizou milhares de APIs exclusivas do Windows no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-145">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="a8e0e-146">Essas APIs não estavam disponíveis no .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-146">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="a8e0e-147">Caminho de aprendizado recomendado</span><span class="sxs-lookup"><span data-stu-id="a8e0e-147">Recommended learning path</span></span>

<span data-ttu-id="a8e0e-148">Recomendamos a seguinte sequência de tutoriais e artigos para obter uma introdução ao desenvolvimento de aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-148">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="a8e0e-149">Siga um tutorial para o tipo de aplicativo que você deseja desenvolver ou manter:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-149">Follow a tutorial for the type of app you want to develop or maintain:</span></span>

   |<span data-ttu-id="a8e0e-150">Tipo de aplicativo</span><span class="sxs-lookup"><span data-stu-id="a8e0e-150">App type</span></span>  |<span data-ttu-id="a8e0e-151">Cenário</span><span class="sxs-lookup"><span data-stu-id="a8e0e-151">Scenario</span></span>  |<span data-ttu-id="a8e0e-152">Tutorial</span><span class="sxs-lookup"><span data-stu-id="a8e0e-152">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="a8e0e-153">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="a8e0e-153">Web app</span></span>                   | <span data-ttu-id="a8e0e-154">Para novo desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="a8e0e-154">For new development</span></span>        |[<span data-ttu-id="a8e0e-155">Introdução a Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="a8e0e-155">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="a8e0e-156">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="a8e0e-156">Web app</span></span>                   | <span data-ttu-id="a8e0e-157">Para manter um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="a8e0e-157">For maintaining an MVC app</span></span> |[<span data-ttu-id="a8e0e-158">Introdução ao MVC</span><span class="sxs-lookup"><span data-stu-id="a8e0e-158">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="a8e0e-159">API Web</span><span class="sxs-lookup"><span data-stu-id="a8e0e-159">Web API</span></span>                   |                            |<span data-ttu-id="a8e0e-160">[Criar uma API Web](xref:tutorials/first-web-api)\*</span><span class="sxs-lookup"><span data-stu-id="a8e0e-160">[Create a web API](xref:tutorials/first-web-api)\*</span></span>  |
   |<span data-ttu-id="a8e0e-161">Aplicativo em tempo real</span><span class="sxs-lookup"><span data-stu-id="a8e0e-161">Real-time app</span></span>             |                            |[<span data-ttu-id="a8e0e-162">Introdução ao SignalR</span><span class="sxs-lookup"><span data-stu-id="a8e0e-162">Get started with SignalR</span></span>](xref:tutorials/signalr) |
   |<span data-ttu-id="a8e0e-163">Aplicativo mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="a8e0e-163">Blazor app</span></span>                |                            |[<span data-ttu-id="a8e0e-164">Introdução ao mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="a8e0e-164">Get started with Blazor</span></span>](xref:blazor/get-started) |
   |<span data-ttu-id="a8e0e-165">Aplicativo de chamada de procedimento remoto</span><span class="sxs-lookup"><span data-stu-id="a8e0e-165">Remote Procedure Call app</span></span> |                            |[<span data-ttu-id="a8e0e-166">Introdução a um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="a8e0e-166">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |

1. <span data-ttu-id="a8e0e-167">Siga um tutorial que mostra como fazer o acesso a dados básicos:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-167">Follow a tutorial that shows how to do basic data access:</span></span>

   |<span data-ttu-id="a8e0e-168">Cenário</span><span class="sxs-lookup"><span data-stu-id="a8e0e-168">Scenario</span></span>  |<span data-ttu-id="a8e0e-169">Tutorial</span><span class="sxs-lookup"><span data-stu-id="a8e0e-169">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="a8e0e-170">Para novo desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="a8e0e-170">For new development</span></span>        |[<span data-ttu-id="a8e0e-171">Razor Pages com o Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a8e0e-171">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="a8e0e-172">Para manter um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="a8e0e-172">For maintaining an MVC app</span></span> |[<span data-ttu-id="a8e0e-173">MVC com o Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a8e0e-173">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

1. <span data-ttu-id="a8e0e-174">Leia uma visão geral dos recursos do ASP.NET Core que se aplicam a todos os tipos de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-174">Read an overview of ASP.NET Core features that apply to all app types:</span></span>

   * [<span data-ttu-id="a8e0e-175">Conceitos básicos</span><span class="sxs-lookup"><span data-stu-id="a8e0e-175">Fundamentals</span></span>](xref:fundamentals/index)

1. <span data-ttu-id="a8e0e-176">Pesquise no Sumário outros tópicos de interesse.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-176">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="a8e0e-177">\* Há um novo [tutorial de API Web que você segue totalmente no navegador](https://docs.microsoft.com/learn/modules/build-web-api-net-core), sem necessidade de instalação local do IDE.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-177">\* There is a new [web API tutorial that you follow entirely in the browser](https://docs.microsoft.com/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span>  <span data-ttu-id="a8e0e-178">O código é executado em um [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) e [curl](https://curl.haxx.se/) é usado para teste.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-178">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migration-from-the-net-framework"></a><span data-ttu-id="a8e0e-179">Migração do .NET Framework</span><span class="sxs-lookup"><span data-stu-id="a8e0e-179">Migration from the .NET Framework</span></span>

<span data-ttu-id="a8e0e-180">Para obter um guia de referência para migrar aplicativos ASP.NET para ASP.NET Core, consulte <xref:migration/proper-to-2x/index>.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-180">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="a8e0e-181">Como baixar uma amostra</span><span class="sxs-lookup"><span data-stu-id="a8e0e-181">How to download a sample</span></span>

<span data-ttu-id="a8e0e-182">Muitos dos artigos e tutoriais incluem links para exemplos de código.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-182">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="a8e0e-183">[Baixe o arquivo zip do repositório ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="a8e0e-183">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="a8e0e-184">Descompacte o arquivo *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-184">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="a8e0e-185">Use a URL no link de exemplo para ajudá-lo a navegar até o diretório de exemplo.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-185">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="a8e0e-186">Diretivas do pré-processador no código de exemplo</span><span class="sxs-lookup"><span data-stu-id="a8e0e-186">Preprocessor directives in sample code</span></span>

<span data-ttu-id="a8e0e-187">Para demonstrar vários cenários, os aplicativos de exemplo usam as diretivas de pré-processador `#define` e `#if-#else/#elif-#endif` para compilar e executar seletivamente diferentes seções de código de exemplo.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-187">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="a8e0e-188">Para os exemplos que fazem uso dessa abordagem, defina a diretiva `#define` na parte superior dos C# arquivos para definir o símbolo associado ao cenário que você deseja executar.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-188">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="a8e0e-189">Alguns exemplos exigem a definição do símbolo na parte superior de vários arquivos para executar um cenário.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-189">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="a8e0e-190">Por exemplo, a seguinte lista de símbolo `#define` indica que quatro cenários estão disponíveis (um cenário por símbolo).</span><span class="sxs-lookup"><span data-stu-id="a8e0e-190">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="a8e0e-191">A configuração da amostra atual executa o cenário `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-191">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="a8e0e-192">Para alterar a amostra que executará o cenário `ExpandDefault`, defina o símbolo `ExpandDefault` e deixe os símbolos restantes comentados de fora:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-192">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="a8e0e-193">Para obter mais informações sobre como usar [diretivas de pré-processador C#](/dotnet/csharp/language-reference/preprocessor-directives/) para compilar seletivamente as seções de código, consulte [#define (Referência C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) e [#if (Referência C#) ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="a8e0e-193">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="a8e0e-194">Regiões no código de exemplo</span><span class="sxs-lookup"><span data-stu-id="a8e0e-194">Regions in sample code</span></span>

<span data-ttu-id="a8e0e-195">Alguns aplicativos de exemplo contêm seções de código circundadas por [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) e [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# diretivas.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-195">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="a8e0e-196">O sistema de build de documentação injeta essas regiões nos tópicos renderizados da documentação.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-196">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="a8e0e-197">Os nomes das regiões geralmente contêm a palavra "snippet".</span><span class="sxs-lookup"><span data-stu-id="a8e0e-197">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="a8e0e-198">O exemplo a seguir mostra uma região chamada `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-198">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="a8e0e-199">O snippet de código C# precedente é referenciado no arquivo de markdown do tópico com a seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-199">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="a8e0e-200">Você pode ignorar (ou remover) com segurança as diretivas `#region` e `#endregion` que envolvem o código.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-200">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="a8e0e-201">Não altere o código nessas diretivas se você planeja executar os cenários de exemplo descritos no tópico.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-201">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="a8e0e-202">Fique à vontade para alterar o código ao experimentar com outros cenários.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-202">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="a8e0e-203">Para obter mais informações, veja [Contribuir para a documentação do ASP.NET: snippets de código](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="a8e0e-203">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="a8e0e-204">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a8e0e-204">Next steps</span></span>

<span data-ttu-id="a8e0e-205">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="a8e0e-205">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="a8e0e-206">Conceitos básicos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8e0e-206">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="a8e0e-207">O [Community Standup semanal do ASP.NET](https://live.asp.net/) aborda o progresso e os planos da equipe.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-207">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="a8e0e-208">Ele apresenta o novo software de terceiros e blogs.</span><span class="sxs-lookup"><span data-stu-id="a8e0e-208">It features new blogs and third-party software.</span></span>
