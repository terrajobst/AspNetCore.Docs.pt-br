---
title: Modelos de hospedagem de componentes do Razor
author: guardrex
description: Entenda Blazor do lado do cliente e componentes do lado do servidor ASP.NET Core Razor modelos de hospedagem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: razor-components/hosting-models
ms.openlocfilehash: 8ed70117c94bf1a3e4c208f70310bbf0473bae44
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515341"
---
# <a name="razor-components-hosting-models"></a><span data-ttu-id="02862-103">Modelos de hospedagem de componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="02862-103">Razor Components hosting models</span></span>

<span data-ttu-id="02862-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="02862-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="02862-105">Componentes do Razor é uma estrutura de web projetada para ser executado lado do cliente no navegador em um [WebAssembly](http://webassembly.org/)-com base em tempo de execução do .NET (*Blazor*) ou do servidor no ASP.NET Core (*Razor do ASP.NET Core Componentes*).</span><span class="sxs-lookup"><span data-stu-id="02862-105">Razor Components is a web framework designed to run client-side in the browser on a [WebAssembly](http://webassembly.org/)-based .NET runtime (*Blazor*) or server-side in ASP.NET Core (*ASP.NET Core Razor Components*).</span></span> <span data-ttu-id="02862-106">Independentemente dos modelos de hospedagem de modelo, o aplicativo e o componente *permanecem os mesmos*.</span><span class="sxs-lookup"><span data-stu-id="02862-106">Regardless of the hosting model, the app and component models *remain the same*.</span></span> <span data-ttu-id="02862-107">Este artigo discute os modelos de hospedagem disponíveis:</span><span class="sxs-lookup"><span data-stu-id="02862-107">This article discusses the available hosting models:</span></span>

* [<span data-ttu-id="02862-108">Blazor no lado do cliente</span><span class="sxs-lookup"><span data-stu-id="02862-108">Client-side Blazor</span></span>](#client-side-hosting-model)
* [<span data-ttu-id="02862-109">Componentes Razor ASP.NET Core no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="02862-109">Server-side ASP.NET Core Razor Components</span></span>](#server-side-hosting-model)

## <a name="client-side-hosting-model"></a><span data-ttu-id="02862-110">Modelo de hospedagem do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="02862-110">Client-side hosting model</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="02862-111">O principal modelo de hospedagem para Blazor é o lado do cliente em execução no navegador WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="02862-111">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="02862-112">O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="02862-112">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="02862-113">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="02862-113">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="02862-114">Atualizações da interface do usuário e a manipulação de eventos ocorrerem dentro do mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="02862-114">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="02862-115">Os ativos do aplicativo são implantados como arquivos estáticos em um servidor web ou serviço capaz de servir conteúdo estático para os clientes.</span><span class="sxs-lookup"><span data-stu-id="02862-115">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor lado do cliente: O aplicativo Blazor é executado em um thread de interface do usuário dentro do navegador.](hosting-models/_static/client-side.png)

<span data-ttu-id="02862-117">Para criar um aplicativo de Blazor usando o modelo de hospedagem do lado do cliente, use os seguintes modelos:</span><span class="sxs-lookup"><span data-stu-id="02862-117">To create a Blazor app using the client-side hosting model, use either of the following templates:</span></span>

* <span data-ttu-id="02862-118">**Blazor** ([blazor de novo dotnet](/dotnet/core/tools/dotnet-new)) &ndash; implantado como um conjunto de arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="02862-118">**Blazor** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; Deployed as a set of static files.</span></span>
* <span data-ttu-id="02862-119">**Blazor (ASP.NET Core hospedados)** ([blazorhosted de novo dotnet](/dotnet/core/tools/dotnet-new)) &ndash; hospedadas por um servidor do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02862-119">**Blazor (ASP.NET Core Hosted)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; Hosted by an ASP.NET Core server.</span></span> <span data-ttu-id="02862-120">O aplicativo ASP.NET Core atua como o aplicativo Blazor para clientes.</span><span class="sxs-lookup"><span data-stu-id="02862-120">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="02862-121">O aplicativo de Blazor do lado do cliente pode interagir com o servidor pela rede usando chamadas à API web ou [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="02862-121">The client-side Blazor app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="02862-122">Os modelos incluem o *components.webassembly.js* script que manipula:</span><span class="sxs-lookup"><span data-stu-id="02862-122">The templates include the *components.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="02862-123">Baixando o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02862-123">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="02862-124">Inicialização de tempo de execução para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02862-124">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="02862-125">O modelo de hospedagem do lado do cliente oferece vários benefícios.</span><span class="sxs-lookup"><span data-stu-id="02862-125">The client-side hosting model offers several benefits.</span></span> <span data-ttu-id="02862-126">Blazor do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="02862-126">Client-side Blazor:</span></span>

* <span data-ttu-id="02862-127">Não tenha nenhuma dependência do lado do servidor de .NET.</span><span class="sxs-lookup"><span data-stu-id="02862-127">Has no .NET server-side dependency.</span></span>
* <span data-ttu-id="02862-128">Aproveita totalmente funções e recursos de cliente.</span><span class="sxs-lookup"><span data-stu-id="02862-128">Fully leverages client resources and capabilities.</span></span>
* <span data-ttu-id="02862-129">Descarregamentos de trabalham do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="02862-129">Offloads work from the server to the client.</span></span>
* <span data-ttu-id="02862-130">Dá suporte a cenários offline.</span><span class="sxs-lookup"><span data-stu-id="02862-130">Supports offline scenarios.</span></span>

<span data-ttu-id="02862-131">Há desvantagens para hospedagem do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="02862-131">There are downsides to client-side hosting.</span></span> <span data-ttu-id="02862-132">Blazor do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="02862-132">Client-side Blazor:</span></span>

* <span data-ttu-id="02862-133">Restringe o aplicativo para os recursos do navegador.</span><span class="sxs-lookup"><span data-stu-id="02862-133">Restricts the app to the capabilities of the browser.</span></span>
* <span data-ttu-id="02862-134">Requer hardware compatível com cliente e software (por exemplo, WebAssembly suporte).</span><span class="sxs-lookup"><span data-stu-id="02862-134">Requires capable client hardware and software (for example, WebAssembly support).</span></span>
* <span data-ttu-id="02862-135">Tem um tamanho maior de download e o aplicativo mais do que o tempo de carregamento.</span><span class="sxs-lookup"><span data-stu-id="02862-135">Has a larger download size and longer app load time.</span></span>
* <span data-ttu-id="02862-136">Tem menos mature .NET runtime e o suporte de ferramentas (por exemplo, as limitações no [.NET Standard](/dotnet/standard/net-standard) suporte e depuração).</span><span class="sxs-lookup"><span data-stu-id="02862-136">Has less mature .NET runtime and tooling support (for example, limitations in [.NET Standard](/dotnet/standard/net-standard) support and debugging).</span></span>

## <a name="server-side-hosting-model"></a><span data-ttu-id="02862-137">Modelo de hospedagem do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="02862-137">Server-side hosting model</span></span>

<span data-ttu-id="02862-138">Com o modelo de hospedagem de servidor de componentes do ASP.NET Core Razor, o aplicativo é executado no servidor de dentro de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02862-138">With the ASP.NET Core Razor Components server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="02862-139">Atualizações da interface do usuário, a manipulação de eventos e chamadas de JavaScript são tratadas por um [SignalR](xref:signalr/introduction) conexão.</span><span class="sxs-lookup"><span data-stu-id="02862-139">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![ASP.NET Core Razor componentes do servidor: O navegador interage com o aplicativo (hospedado dentro de um aplicativo ASP.NET Core) no servidor ao longo de uma conexão SignalR.](hosting-models/_static/server-side.png)

<span data-ttu-id="02862-141">Para criar um aplicativo de componentes do Razor usando o modelo de hospedagem do lado do servidor, use o ASP.NET Core **componentes do Razor** modelo ([razorcomponents de novo dotnet](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="02862-141">To create a Razor Components app using the server-side hosting model, use the ASP.NET Core **Razor Components** template ([dotnet new razorcomponents](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="02862-142">O aplicativo ASP.NET Core hospeda o aplicativo do lado do servidor de componentes do Razor e configura o ponto de extremidade do SignalR em que os clientes se conectam.</span><span class="sxs-lookup"><span data-stu-id="02862-142">The ASP.NET Core app hosts the Razor Components server-side app and sets up the SignalR endpoint where clients connect.</span></span> <span data-ttu-id="02862-143">O aplicativo ASP.NET Core faz referência do aplicativo `Startup` classe a ser adicionada:</span><span class="sxs-lookup"><span data-stu-id="02862-143">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="02862-144">Serviços de componentes do Razor do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="02862-144">Server-side Razor Components services.</span></span>
* <span data-ttu-id="02862-145">O aplicativo para o pipeline de tratamento de solicitação.</span><span class="sxs-lookup"><span data-stu-id="02862-145">The app to the request handling pipeline.</span></span>

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

<span data-ttu-id="02862-146">O *components.server.js* script&dagger; estabelece a conexão de cliente.</span><span class="sxs-lookup"><span data-stu-id="02862-146">The *components.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="02862-147">É responsabilidade do aplicativo para manter e restaurar o estado do aplicativo conforme necessário (por exemplo, no caso de uma conexão de rede perdidas).</span><span class="sxs-lookup"><span data-stu-id="02862-147">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="02862-148">O modelo de hospedagem do lado do servidor oferece vários benefícios.</span><span class="sxs-lookup"><span data-stu-id="02862-148">The server-side hosting model offers several benefits.</span></span> <span data-ttu-id="02862-149">Componentes do lado do servidor Razor:</span><span class="sxs-lookup"><span data-stu-id="02862-149">Server-side Razor Components:</span></span>

* <span data-ttu-id="02862-150">Ter um tamanho de aplicativo significativamente menor do que um aplicativo do lado do cliente Blazor e carregados com mais rapidez.</span><span class="sxs-lookup"><span data-stu-id="02862-150">Have a significantly smaller app size than a client-side Blazor app and load much faster.</span></span>
* <span data-ttu-id="02862-151">Podem tirar total proveito dos recursos do servidor, incluindo o uso de quaisquer APIs compatíveis do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="02862-151">Can take full advantage of server capabilities, including using any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="02862-152">Executados no .NET Core no servidor, portanto, o .NET existentes das ferramentas, como depuração, funciona conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="02862-152">Run on .NET Core on the server, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="02862-153">Funciona com clientes finos (por exemplo, os navegadores que não dão suporte a recursos e WebAssembly restrita dispositivos).</span><span class="sxs-lookup"><span data-stu-id="02862-153">Works with thin clients (for example, browsers that don't support WebAssembly and resource constrained devices).</span></span>
* <span data-ttu-id="02862-154">.NET /C# base de código, incluindo o código de componente do aplicativo, não é atendido aos clientes.</span><span class="sxs-lookup"><span data-stu-id="02862-154">.NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="02862-155">Há desvantagens para o lado do servidor de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="02862-155">There are downsides to server-side hosting.</span></span> <span data-ttu-id="02862-156">Componentes do lado do servidor Razor:</span><span class="sxs-lookup"><span data-stu-id="02862-156">Server-side Razor Components:</span></span>

* <span data-ttu-id="02862-157">Ter latência mais alta: Cada interação do usuário envolve um salto de rede.</span><span class="sxs-lookup"><span data-stu-id="02862-157">Have higher latency: Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="02862-158">Não há suporte offline da oferta: Se a conexão de cliente falhar, o aplicativo deixará de funcionar.</span><span class="sxs-lookup"><span data-stu-id="02862-158">Offer no offline support: If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="02862-159">Reduziram escalabilidade: O servidor deve gerenciar várias conexões de cliente e manipular o estado do cliente.</span><span class="sxs-lookup"><span data-stu-id="02862-159">Have reduced scalability: The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="02862-160">Exige um servidor do ASP.NET Core para servir o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02862-160">Require an ASP.NET Core server to serve the app.</span></span> <span data-ttu-id="02862-161">Implantação sem um servidor (por exemplo, de uma CDN) não é possível.</span><span class="sxs-lookup"><span data-stu-id="02862-161">Deployment without a server (for example, from a CDN) isn't possible.</span></span>

<span data-ttu-id="02862-162">&dagger;O *components.server.js* script é publicado para o seguinte caminho: *bin / {depurar | Versão} / {TARGET FRAMEWORK} /publish/ {nome do aplicativo}. Aplicativo/dist/estrutura de _*.</span><span class="sxs-lookup"><span data-stu-id="02862-162">&dagger;The *components.server.js* script is published to the following path: *bin/{Debug|Release}/{TARGET FRAMEWORK}/publish/{APPLICATION NAME}.App/dist/_framework*.</span></span>
