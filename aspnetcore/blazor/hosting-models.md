---
title: Modelos de Hospedagem de Blazor ASP.NET Core
author: guardrex
description: Entenda Blazor Webassembly e modelos de hospedagem do Blazor Server.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 7676d16bddf146ea38619ed35c5e32c5bce731de
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943752"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a><span data-ttu-id="a9a96-103">Modelos de Hospedagem de Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9a96-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="a9a96-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a9a96-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="a9a96-105"> é uma estrutura da Web criada para executar o lado do cliente no navegador em um tempo de execução .NET baseado em [Webassembly](https://webassembly.org/)( *Blazor Webassembly*) ou no lado do servidor no ASP.NET Core ( *Blazor Server*).</span><span class="sxs-lookup"><span data-stu-id="a9a96-105"> is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="a9a96-106">Independentemente do modelo de hospedagem, os modelos de aplicativo e componente *são os mesmos*.</span><span class="sxs-lookup"><span data-stu-id="a9a96-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="a9a96-107">Para criar um projeto para os modelos de hospedagem descritos neste artigo, consulte <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="a9a96-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="a9a96-108"> Webassembly</span><span class="sxs-lookup"><span data-stu-id="a9a96-108"> WebAssembly</span></span>

<span data-ttu-id="a9a96-109">O modelo de hospedagem principal para Blazor está executando o lado do cliente no navegador no Webassembly.</span><span class="sxs-lookup"><span data-stu-id="a9a96-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="a9a96-110">O aplicativo Blazor, suas dependências e o tempo de execução do .NET são baixados para o navegador.</span><span class="sxs-lookup"><span data-stu-id="a9a96-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="a9a96-111">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="a9a96-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="a9a96-112">As atualizações da interface do usuário e o tratamento de eventos ocorrem no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="a9a96-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="a9a96-113">Os ativos do aplicativo são implantados como arquivos estáticos em um servidor Web ou serviço capaz de fornecer conteúdo estático aos clientes.</span><span class="sxs-lookup"><span data-stu-id="a9a96-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![[! Parar. Webassembly não LOC (mais alto)]<span data-ttu-id="a9a96-114">: O [! Parar. O aplicativo no-LOC (mais alto)] é executado em um thread de interface do usuário dentro do navegador.</span><span class="sxs-lookup"><span data-stu-id="a9a96-114"> WebAssembly: The Blazor app runs on a UI thread inside the browser.</span></span>](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="a9a96-115">Para criar um aplicativo Blazor usando o modelo de hospedagem do lado do cliente, use o modelo de **aplicativo Webassembly doBlazor** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="a9a96-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="a9a96-116">Depois de selecionar o modelo de **aplicativo WebassemblyBlazor** , você tem a opção de configurar o aplicativo para usar um back-end ASP.NET Core marcando a caixa de seleção **ASP.NET Core hospedado** ([dotnet New blazorwasm – hospedado](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="a9a96-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="a9a96-117">O aplicativo ASP.NET Core serve o aplicativo Blazor aos clientes.</span><span class="sxs-lookup"><span data-stu-id="a9a96-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="a9a96-118">O aplicativo Webassembly Blazor pode interagir com o servidor pela rede usando chamadas de API Web ou [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="a9a96-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="a9a96-119">Os modelos incluem o script mais *incrivelmente. Webassembly. js* que manipula:</span><span class="sxs-lookup"><span data-stu-id="a9a96-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="a9a96-120">Baixar o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9a96-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="a9a96-121">Inicialização do tempo de execução para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9a96-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="a9a96-122">O modelo de hospedagem Webassembly Blazor oferece vários benefícios:</span><span class="sxs-lookup"><span data-stu-id="a9a96-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="a9a96-123">Não há nenhuma dependência do lado do servidor .NET.</span><span class="sxs-lookup"><span data-stu-id="a9a96-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="a9a96-124">O aplicativo está totalmente funcionando depois de baixado para o cliente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="a9a96-125">Recursos e funcionalidades do cliente são totalmente aproveitados.</span><span class="sxs-lookup"><span data-stu-id="a9a96-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="a9a96-126">O trabalho é descarregado do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="a9a96-127">Um servidor Web ASP.NET Core não é necessário para hospedar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9a96-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="a9a96-128">Cenários de implantação sem servidor são possíveis (por exemplo, servindo o aplicativo de uma CDN).</span><span class="sxs-lookup"><span data-stu-id="a9a96-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="a9a96-129">Há desvantagens em Blazor Hospedagem de Webassembly:</span><span class="sxs-lookup"><span data-stu-id="a9a96-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="a9a96-130">O aplicativo é restrito aos recursos do navegador.</span><span class="sxs-lookup"><span data-stu-id="a9a96-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="a9a96-131">Hardware e software de cliente com capacidade (por exemplo, suporte a Webassembly) é necessário.</span><span class="sxs-lookup"><span data-stu-id="a9a96-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="a9a96-132">O tamanho do download é maior e os aplicativos demoram mais para serem carregados.</span><span class="sxs-lookup"><span data-stu-id="a9a96-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="a9a96-133">O suporte ao tempo de execução e às ferramentas do .NET é menos maduro.</span><span class="sxs-lookup"><span data-stu-id="a9a96-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="a9a96-134">Por exemplo, existem limitações em [.net Standard](/dotnet/standard/net-standard) suporte e depuração.</span><span class="sxs-lookup"><span data-stu-id="a9a96-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="opno-locblazor-server"></a><span data-ttu-id="a9a96-135">Servidor de Blazor</span><span class="sxs-lookup"><span data-stu-id="a9a96-135">Blazor Server</span></span>

<span data-ttu-id="a9a96-136">Com o modelo de hospedagem do servidor Blazor, o aplicativo é executado no servidor de dentro de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9a96-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="a9a96-137">As atualizações de interface do usuário, manipulação de eventos e chamadas JavaScript são manipuladas por uma conexão [SignalR](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="a9a96-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![O navegador interage com o aplicativo (hospedado dentro de um aplicativo ASP.NET Core) no servidor por meio de um [! Parar. Conexão não-LOC (Signalr)].](hosting-models/_static/blazor-server.png)

<span data-ttu-id="a9a96-139">Para criar um aplicativo Blazor usando o modelo de hospedagem do Blazor Server, use o modelo de **aplicativo ASP.NET CoreBlazor Server** ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="a9a96-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="a9a96-140">O aplicativo ASP.NET Core hospeda o aplicativo Blazor Server e cria o ponto de extremidade SignalR onde os clientes se conectam.</span><span class="sxs-lookup"><span data-stu-id="a9a96-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="a9a96-141">O aplicativo ASP.NET Core referencia a classe `Startup` do aplicativo a ser adicionada:</span><span class="sxs-lookup"><span data-stu-id="a9a96-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="a9a96-142">Serviços do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-142">Server-side services.</span></span>
* <span data-ttu-id="a9a96-143">O aplicativo para o pipeline de tratamento de solicitação.</span><span class="sxs-lookup"><span data-stu-id="a9a96-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="a9a96-144">O script mais *incrivelmente. Server. js*&dagger; estabelece a conexão do cliente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="a9a96-145">É responsabilidade do aplicativo persistir e restaurar o estado do aplicativo, conforme necessário (por exemplo, no caso de uma conexão de rede perdida).</span><span class="sxs-lookup"><span data-stu-id="a9a96-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="a9a96-146">O modelo de hospedagem do Blazor Server oferece vários benefícios:</span><span class="sxs-lookup"><span data-stu-id="a9a96-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="a9a96-147">O tamanho do download é significativamente menor do que um aplicativo Webassembly Blazor e o aplicativo é carregado muito mais rapidamente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="a9a96-148">O aplicativo aproveita totalmente os recursos do servidor, incluindo o uso de qualquer API compatível com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9a96-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="a9a96-149">O .NET Core no servidor é usado para executar o aplicativo, portanto, as ferramentas .NET existentes, como depuração, funcionam conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="a9a96-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="a9a96-150">Há suporte para clientes finos.</span><span class="sxs-lookup"><span data-stu-id="a9a96-150">Thin clients are supported.</span></span> <span data-ttu-id="a9a96-151">Por exemplo, os aplicativos do Blazor Server funcionam com navegadores que não dão suporte ao Webassembly e a dispositivos com restrição de recursos.</span><span class="sxs-lookup"><span data-stu-id="a9a96-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="a9a96-152">A base .NET/C# código do aplicativo, incluindo o código de componente do aplicativo, não é servida aos clientes.</span><span class="sxs-lookup"><span data-stu-id="a9a96-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="a9a96-153">Há desvantagens em Blazor Hospedagem de servidor:</span><span class="sxs-lookup"><span data-stu-id="a9a96-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="a9a96-154">A latência mais alta geralmente existe.</span><span class="sxs-lookup"><span data-stu-id="a9a96-154">Higher latency usually exists.</span></span> <span data-ttu-id="a9a96-155">Cada interação do usuário envolve um salto de rede.</span><span class="sxs-lookup"><span data-stu-id="a9a96-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="a9a96-156">Não há suporte offline.</span><span class="sxs-lookup"><span data-stu-id="a9a96-156">There's no offline support.</span></span> <span data-ttu-id="a9a96-157">Se a conexão do cliente falhar, o aplicativo para de funcionar.</span><span class="sxs-lookup"><span data-stu-id="a9a96-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="a9a96-158">A escalabilidade é desafiadora para aplicativos com muitos usuários.</span><span class="sxs-lookup"><span data-stu-id="a9a96-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="a9a96-159">O servidor deve gerenciar várias conexões de cliente e manipular o estado do cliente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="a9a96-160">Um servidor de ASP.NET Core é necessário para atender ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9a96-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="a9a96-161">Cenários de implantação sem servidor não são possíveis (por exemplo, servir o aplicativo de uma CDN).</span><span class="sxs-lookup"><span data-stu-id="a9a96-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="a9a96-162">&dagger;o script mais *incrivelmente. Server. js* é servido por meio de um recurso incorporado na estrutura compartilhada ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9a96-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="a9a96-163">Comparação com a interface do usuário renderizada pelo servidor</span><span class="sxs-lookup"><span data-stu-id="a9a96-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="a9a96-164">Uma maneira de entender Blazor aplicativos de servidor é entender como ele difere dos modelos tradicionais para renderizar a interface do usuário em aplicativos ASP.NET Core usando exibições do Razor ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a9a96-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="a9a96-165">Ambos os modelos usam a linguagem Razor para descrever o conteúdo HTML, mas são significativamente diferentes na forma como a marcação é renderizada.</span><span class="sxs-lookup"><span data-stu-id="a9a96-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="a9a96-166">Quando uma página Razor ou exibição é renderizada, cada linha do código do Razor emite HTML na forma de texto.</span><span class="sxs-lookup"><span data-stu-id="a9a96-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="a9a96-167">Após a renderização, o servidor descarta a página ou a instância de exibição, incluindo qualquer estado produzido.</span><span class="sxs-lookup"><span data-stu-id="a9a96-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="a9a96-168">Quando outra solicitação para a página ocorre, por exemplo, quando a validação do servidor falha e o resumo de validação é exibido:</span><span class="sxs-lookup"><span data-stu-id="a9a96-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="a9a96-169">A página inteira é reprocessada para o texto HTML novamente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="a9a96-170">A página é enviada ao cliente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-170">The page is sent to the client.</span></span>

<span data-ttu-id="a9a96-171">Um aplicativo Blazor é composto por elementos reutilizáveis da interface do usuário chamada *componentes*.</span><span class="sxs-lookup"><span data-stu-id="a9a96-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="a9a96-172">Um componente contém C# código, marcação e outros componentes.</span><span class="sxs-lookup"><span data-stu-id="a9a96-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="a9a96-173">Quando um componente é renderizado, Blazor produz um grafo dos componentes incluídos de forma semelhante a um XML (Modelo de Objeto do Documento) ou HTML (DOM).</span><span class="sxs-lookup"><span data-stu-id="a9a96-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="a9a96-174">Esse grafo inclui o estado do componente mantido em Propriedades e campos.</span><span class="sxs-lookup"><span data-stu-id="a9a96-174">This graph includes component state held in properties and fields.</span></span> Blazor<span data-ttu-id="a9a96-175"> avalia o grafo de componente para produzir uma representação binária da marcação.</span><span class="sxs-lookup"><span data-stu-id="a9a96-175"> evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="a9a96-176">O formato binário pode ser:</span><span class="sxs-lookup"><span data-stu-id="a9a96-176">The binary format can be:</span></span>

* <span data-ttu-id="a9a96-177">Transformado em texto HTML (durante o pré-processamento).</span><span class="sxs-lookup"><span data-stu-id="a9a96-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="a9a96-178">Usado para atualizar a marcação com eficiência durante a renderização regular.</span><span class="sxs-lookup"><span data-stu-id="a9a96-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="a9a96-179">Uma atualização de interface do usuário no Blazor é disparada por:</span><span class="sxs-lookup"><span data-stu-id="a9a96-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="a9a96-180">Interação do usuário, como selecionar um botão.</span><span class="sxs-lookup"><span data-stu-id="a9a96-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="a9a96-181">Gatilhos de aplicativo, como um temporizador.</span><span class="sxs-lookup"><span data-stu-id="a9a96-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="a9a96-182">O grafo é rerenderizado e uma *comparação* de interface do usuário (diferença) é calculada.</span><span class="sxs-lookup"><span data-stu-id="a9a96-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="a9a96-183">Essa diferença é o menor conjunto de edições DOM necessárias para atualizar a interface do usuário no cliente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="a9a96-184">A comparação é enviada ao cliente em um formato binário e aplicada pelo navegador.</span><span class="sxs-lookup"><span data-stu-id="a9a96-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="a9a96-185">Um componente é descartado depois que o usuário navega para fora dele no cliente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="a9a96-186">Embora um usuário esteja interagindo com um componente, o estado do componente (serviços, recursos) deve ser mantido na memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="a9a96-187">Como o estado de muitos componentes pode ser mantido pelo servidor simultaneamente, o esgotamento de memória é uma preocupação que deve ser resolvida.</span><span class="sxs-lookup"><span data-stu-id="a9a96-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="a9a96-188">Para obter orientação sobre como criar um aplicativo do Blazor Server para garantir o melhor uso da memória do servidor, consulte <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="a9a96-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="circuits"></a><span data-ttu-id="a9a96-189">Circuitos</span><span class="sxs-lookup"><span data-stu-id="a9a96-189">Circuits</span></span>

<span data-ttu-id="a9a96-190">Um aplicativo do Blazor Server é criado sobre [ASP.NET Core SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="a9a96-190">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="a9a96-191">Cada cliente se comunica com o servidor por meio de uma ou mais conexões SignalR chamadas de *circuito*.</span><span class="sxs-lookup"><span data-stu-id="a9a96-191">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="a9a96-192">Um circuito é A abstração Blazorde SignalR conexões que podem tolerar interrupções de rede temporárias.</span><span class="sxs-lookup"><span data-stu-id="a9a96-192">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="a9a96-193">Quando um cliente Blazor vê que a conexão SignalR está desconectada, ele tenta se reconectar ao servidor usando uma nova conexão SignalR.</span><span class="sxs-lookup"><span data-stu-id="a9a96-193">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="a9a96-194">Cada tela do navegador (guia do navegador ou iframe) que está conectada a um aplicativo do Blazor Server usa uma conexão SignalR.</span><span class="sxs-lookup"><span data-stu-id="a9a96-194">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="a9a96-195">Essa é ainda outra distinção importante em comparação com os aplicativos típicos renderizados pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-195">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="a9a96-196">Em um aplicativo renderizado pelo servidor, a abertura do mesmo aplicativo em várias telas do navegador geralmente não se traduz em demandas de recursos adicionais no servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-196">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="a9a96-197">Em um aplicativo do Blazor Server, cada tela do navegador requer um circuito separado e instâncias separadas do estado do componente a serem gerenciadas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-197">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

Blazor<span data-ttu-id="a9a96-198"> considera fechar uma guia do navegador ou navegar até uma URL externa um encerramento *normal* .</span><span class="sxs-lookup"><span data-stu-id="a9a96-198"> considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="a9a96-199">No caso de um encerramento normal, o circuito e os recursos associados são lançados imediatamente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-199">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="a9a96-200">Um cliente também pode se desconectar não normalmente, por exemplo, devido a uma interrupção de rede.</span><span class="sxs-lookup"><span data-stu-id="a9a96-200">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> Blazor<span data-ttu-id="a9a96-201"> Server armazena circuitos desconectados para um intervalo configurável para permitir que o cliente se reconecte.</span><span class="sxs-lookup"><span data-stu-id="a9a96-201"> Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="a9a96-202">Para obter mais informações, consulte a seção [reconexão com o mesmo servidor](#reconnection-to-the-same-server) .</span><span class="sxs-lookup"><span data-stu-id="a9a96-202">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="a9a96-203">Latência da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="a9a96-203">UI Latency</span></span>

<span data-ttu-id="a9a96-204">A latência da interface do usuário é o tempo que leva de uma ação iniciada até a hora em que a interface do usuário é atualizada.</span><span class="sxs-lookup"><span data-stu-id="a9a96-204">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="a9a96-205">Valores menores para latência de interface do usuário são imperativos para um aplicativo se sentir responsivo a um usuário.</span><span class="sxs-lookup"><span data-stu-id="a9a96-205">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="a9a96-206">Em um aplicativo do Blazor Server, cada ação é enviada ao servidor, processada e uma comparação da interface do usuário é enviada de volta.</span><span class="sxs-lookup"><span data-stu-id="a9a96-206">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="a9a96-207">Consequentemente, a latência da interface do usuário é a soma da latência da rede e a latência do servidor no processamento da ação.</span><span class="sxs-lookup"><span data-stu-id="a9a96-207">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="a9a96-208">Para um aplicativo de linha de negócios limitado a uma rede corporativa privada, o efeito sobre as percepções do usuário de latência devido à latência da rede geralmente é imperceptível.</span><span class="sxs-lookup"><span data-stu-id="a9a96-208">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="a9a96-209">Para um aplicativo implantado pela Internet, a latência pode ser perceptível para os usuários, especialmente se os usuários forem amplamente distribuídos geograficamente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-209">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="a9a96-210">O uso de memória também pode contribuir para a latência do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9a96-210">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="a9a96-211">O aumento do uso da memória resulta em uma coleta de lixo frequente ou na paginação da memória no disco, e conseqüentemente aumenta a latência da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="a9a96-211">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="a9a96-212">Para obter mais informações, consulte <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="a9a96-212">For more information, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="a9a96-213">os aplicativos do Blazor Server devem ser otimizados para minimizar a latência da interface do usuário ao reduzir a latência de rede e o uso de memória</span><span class="sxs-lookup"><span data-stu-id="a9a96-213">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="a9a96-214">Para obter uma abordagem para medir a latência de rede, consulte <xref:host-and-deploy/blazor/server#measure-network-latency>.</span><span class="sxs-lookup"><span data-stu-id="a9a96-214">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="a9a96-215">Para obter mais informações sobre SignalR e Blazor, consulte:</span><span class="sxs-lookup"><span data-stu-id="a9a96-215">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a><span data-ttu-id="a9a96-216">Conexão com o servidor</span><span class="sxs-lookup"><span data-stu-id="a9a96-216">Connection to the server</span></span>

<span data-ttu-id="a9a96-217">os aplicativos do Blazor Server exigem uma conexão de SignalR ativa com o servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-217">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="a9a96-218">Se a conexão for perdida, o aplicativo tentará se reconectar ao servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-218">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="a9a96-219">Desde que o estado do cliente ainda esteja na memória, a sessão do cliente será retomada sem perder o estado.</span><span class="sxs-lookup"><span data-stu-id="a9a96-219">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

#### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="a9a96-220">Reconexão com o mesmo servidor</span><span class="sxs-lookup"><span data-stu-id="a9a96-220">Reconnection to the same server</span></span>

<span data-ttu-id="a9a96-221">Um aplicativo do Blazor Server é renderizado em resposta à primeira solicitação do cliente, que configura o estado da interface do usuário no servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-221">A Blazor Server app prerenders in response to the first client request, which sets up the UI state on the server.</span></span> <span data-ttu-id="a9a96-222">Quando o cliente tenta criar uma conexão SignalR, o cliente deve se reconectar ao mesmo servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-222">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> <span data-ttu-id="a9a96-223">os aplicativos do Blazor Server que usam mais de um servidor back-end devem implementar *sessões adesivas* para conexões de SignalR.</span><span class="sxs-lookup"><span data-stu-id="a9a96-223">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="a9a96-224">É recomendável usar o [serviço de SignalR do Azure](/azure/azure-signalr) para aplicativos do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a9a96-224">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="a9a96-225">O serviço permite escalar verticalmente um aplicativo do Blazor Server para um grande número de conexões de SignalR simultâneas.</span><span class="sxs-lookup"><span data-stu-id="a9a96-225">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="a9a96-226">As sessões adesivas são habilitadas para o serviço de SignalR do Azure definindo a opção `ServerStickyMode` do serviço ou o valor de configuração como `Required`.</span><span class="sxs-lookup"><span data-stu-id="a9a96-226">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="a9a96-227">Para obter mais informações, consulte <xref:host-and-deploy/blazor/server#signalr-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a9a96-227">For more information, see <xref:host-and-deploy/blazor/server#signalr-configuration>.</span></span>

<span data-ttu-id="a9a96-228">Ao usar o IIS, as sessões adesivas são habilitadas com Application Request Routing.</span><span class="sxs-lookup"><span data-stu-id="a9a96-228">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="a9a96-229">Para obter mais informações, consulte [balanceamento de carga http usando Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="a9a96-229">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="a9a96-230">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="a9a96-230">Reflect the connection state in the UI</span></span>

<span data-ttu-id="a9a96-231">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="a9a96-231">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="a9a96-232">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-232">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="a9a96-233">Para personalizar a interface do usuário, defina um elemento com uma `id` de `components-reconnect-modal` na `<body>` da página do Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a9a96-233">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="a9a96-234">A tabela a seguir descreve as classes CSS aplicadas ao elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="a9a96-234">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="a9a96-235">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="a9a96-235">CSS class</span></span>                       | <span data-ttu-id="a9a96-236">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="a9a96-236">Indicates&hellip;</span></span> |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | <span data-ttu-id="a9a96-237">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="a9a96-237">A lost connection.</span></span> <span data-ttu-id="a9a96-238">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="a9a96-238">The client is attempting to reconnect.</span></span> <span data-ttu-id="a9a96-239">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="a9a96-239">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="a9a96-240">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-240">An active connection is re-established to the server.</span></span> <span data-ttu-id="a9a96-241">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="a9a96-241">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="a9a96-242">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="a9a96-242">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="a9a96-243">Para tentar a reconexão, chame `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="a9a96-243">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="a9a96-244">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="a9a96-244">Reconnection rejected.</span></span> <span data-ttu-id="a9a96-245">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="a9a96-245">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="a9a96-246">Para recarregar o aplicativo, chame `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="a9a96-246">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="a9a96-247">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="a9a96-247">This connection state may result when:</span></span><ul><li><span data-ttu-id="a9a96-248">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-248">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="a9a96-249">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="a9a96-249">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="a9a96-250">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="a9a96-250">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="a9a96-251">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="a9a96-251">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="a9a96-252">Reconexão com estado após o pré-processamento</span><span class="sxs-lookup"><span data-stu-id="a9a96-252">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="a9a96-253">os aplicativos do Blazor Server são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="a9a96-253">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="a9a96-254">Isso é configurado na página Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a9a96-254">This is set up in the *_Host.cshtml* Razor page:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

<span data-ttu-id="a9a96-255">`RenderMode` configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="a9a96-255">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="a9a96-256">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="a9a96-256">Is prerendered into the page.</span></span>
* <span data-ttu-id="a9a96-257">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo Blazor do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="a9a96-257">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="a9a96-258">{1&gt;Descrição&lt;1}</span><span class="sxs-lookup"><span data-stu-id="a9a96-258">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="a9a96-259">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a9a96-259">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="a9a96-260">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-260">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="a9a96-261">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a9a96-261">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="a9a96-262">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="a9a96-262">Output from the component isn't included.</span></span> <span data-ttu-id="a9a96-263">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-263">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="a9a96-264">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="a9a96-264">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="a9a96-265">{1&gt;Descrição&lt;1}</span><span class="sxs-lookup"><span data-stu-id="a9a96-265">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="a9a96-266">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a9a96-266">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="a9a96-267">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-267">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="a9a96-268">Não há suporte para parâmetros.</span><span class="sxs-lookup"><span data-stu-id="a9a96-268">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="a9a96-269">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a9a96-269">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="a9a96-270">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="a9a96-270">Output from the component isn't included.</span></span> <span data-ttu-id="a9a96-271">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-271">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="a9a96-272">Não há suporte para parâmetros.</span><span class="sxs-lookup"><span data-stu-id="a9a96-272">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="a9a96-273">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="a9a96-273">Renders the component into static HTML.</span></span> <span data-ttu-id="a9a96-274">Há suporte para os parâmetros.</span><span class="sxs-lookup"><span data-stu-id="a9a96-274">Parameters are supported.</span></span> |

::: moniker-end

<span data-ttu-id="a9a96-275">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="a9a96-275">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="a9a96-276">Quando `RenderMode` é `ServerPrerendered`, o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="a9a96-276">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="a9a96-277">Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente*e o componente agora é interativo.</span><span class="sxs-lookup"><span data-stu-id="a9a96-277">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="a9a96-278">Se o método de ciclo de vida de [{Async} OnInitialized](xref:blazor/lifecycle#component-initialization-methods) para inicializar o componente estiver presente, o método será executado *duas vezes*:</span><span class="sxs-lookup"><span data-stu-id="a9a96-278">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="a9a96-279">Quando o componente é renderizado estaticamente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-279">When the component is prerendered statically.</span></span>
* <span data-ttu-id="a9a96-280">Depois que a conexão do servidor tiver sido estabelecida.</span><span class="sxs-lookup"><span data-stu-id="a9a96-280">After the server connection has been established.</span></span>

<span data-ttu-id="a9a96-281">Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.</span><span class="sxs-lookup"><span data-stu-id="a9a96-281">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="a9a96-282">Para evitar o cenário de processamento duplo em um aplicativo do Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="a9a96-282">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="a9a96-283">Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9a96-283">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="a9a96-284">Use o identificador durante o pré-processamento para salvar o estado do componente.</span><span class="sxs-lookup"><span data-stu-id="a9a96-284">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="a9a96-285">Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="a9a96-285">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="a9a96-286">O código a seguir demonstra uma `WeatherForecastService` atualizada em um aplicativo do Blazor Server baseado em modelo que evita a renderização dupla:</span><span class="sxs-lookup"><span data-stu-id="a9a96-286">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] Summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="a9a96-287">Renderizar componentes interativos com estado de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="a9a96-287">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="a9a96-288">Os componentes interativos com estado podem ser adicionados a uma página ou exibição Razor.</span><span class="sxs-lookup"><span data-stu-id="a9a96-288">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="a9a96-289">Quando a página ou a exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="a9a96-289">When the page or view renders:</span></span>

* <span data-ttu-id="a9a96-290">O componente é renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="a9a96-290">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="a9a96-291">O estado inicial do componente usado para o pré-processamento é perdido.</span><span class="sxs-lookup"><span data-stu-id="a9a96-291">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="a9a96-292">O novo estado do componente é criado quando a conexão de SignalR é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="a9a96-292">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="a9a96-293">A seguinte página Razor renderiza um componente `Counter`:</span><span class="sxs-lookup"><span data-stu-id="a9a96-293">The following Razor page renders a `Counter` component:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="a9a96-294">Renderizar componentes não interativos de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="a9a96-294">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="a9a96-295">Na página Razor a seguir, o componente `Counter` é processado estaticamente com um valor inicial que é especificado usando um formulário:</span><span class="sxs-lookup"><span data-stu-id="a9a96-295">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<Counter>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

<span data-ttu-id="a9a96-296">Como `MyComponent` é processado estaticamente, o componente não pode ser interativo.</span><span class="sxs-lookup"><span data-stu-id="a9a96-296">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="a9a96-297">Detectar quando o aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="a9a96-297">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="a9a96-298">Configurar o cliente do SignalR para aplicativos do Blazor Server</span><span class="sxs-lookup"><span data-stu-id="a9a96-298">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="a9a96-299">Às vezes, você precisa configurar o cliente SignalR usado por aplicativos do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a9a96-299">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="a9a96-300">Por exemplo, talvez você queira configurar o registro em log no cliente SignalR para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="a9a96-300">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="a9a96-301">Para configurar o cliente do SignalR no arquivo *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a9a96-301">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="a9a96-302">Adicione um atributo `autostart="false"` à marca de `<script>` para o script mais *incrivelmente. Server. js* .</span><span class="sxs-lookup"><span data-stu-id="a9a96-302">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="a9a96-303">Chame `Blazor.start` e passe um objeto de configuração que especifica o construtor de SignalR.</span><span class="sxs-lookup"><span data-stu-id="a9a96-303">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="a9a96-304">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a9a96-304">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
