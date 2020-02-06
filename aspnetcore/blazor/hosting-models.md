---
title: Modelos de Hospedagem de Blazor ASP.NET Core
author: guardrex
description: Entenda Blazor Webassembly e modelos de hospedagem do Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 7b4d4aca0bc4650c31bc8e5c4a84ecbad6a49b09
ms.sourcegitcommit: 0e21d4f8111743bcb205a2ae0f8e57910c3e8c25
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77034074"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="17a16-103">Modelos de hospedagem mais amASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17a16-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="17a16-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="17a16-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="17a16-105">É uma estrutura da Web criada para executar o lado do cliente no navegador em um tempo de execução .NET baseado em [Webassembly](https://webassembly.org/)(mais alto*Webassembly*) ou no lado do servidor no ASP.NET Core (*servidor*de mais Altova).</span><span class="sxs-lookup"><span data-stu-id="17a16-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="17a16-106">Independentemente do modelo de hospedagem, os modelos de aplicativo e componente *são os mesmos*.</span><span class="sxs-lookup"><span data-stu-id="17a16-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="17a16-107">Para criar um projeto para os modelos de hospedagem descritos neste artigo, consulte <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="17a16-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="17a16-108">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="17a16-108">Blazor WebAssembly</span></span>

<span data-ttu-id="17a16-109">O modelo de hospedagem principal para o mais alto é executar o lado do cliente no navegador no Webassembly.</span><span class="sxs-lookup"><span data-stu-id="17a16-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="17a16-110">O aplicativo do Blazor, suas dependências e o runtime do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="17a16-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="17a16-111">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="17a16-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="17a16-112">As atualizações da interface do usuário e o tratamento de eventos ocorrem no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="17a16-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="17a16-113">Os ativos do aplicativo são implantados como arquivos estáticos em um servidor Web ou serviço capaz de fornecer conteúdo estático aos clientes.</span><span class="sxs-lookup"><span data-stu-id="17a16-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Webassembly mais incrivelmente: o aplicativo mais incrivelmente é executado em um thread de interface do usuário dentro do navegador.](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="17a16-115">Para criar um aplicativo mais novo usando o modelo de hospedagem do lado do cliente, use o modelo de **aplicativo Webassembly** mais[novo (dotNet New blazorwasm](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="17a16-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="17a16-116">Depois de selecionar o modelo de **aplicativo Webassembly** mais novo, você tem a opção de configurar o aplicativo para usar um ASP.NET Core back-end marcando a caixa de seleção **ASP.NET Core hospedado** ([dotnet New blazorwasm – hospedado](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="17a16-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="17a16-117">O aplicativo ASP.NET Core serve para o aplicativo mais incrivelmente para os clientes.</span><span class="sxs-lookup"><span data-stu-id="17a16-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="17a16-118">O aplicativo Webassembly mais novo pode interagir com o servidor pela rede usando chamadas de API Web ou [signalr](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>).</span><span class="sxs-lookup"><span data-stu-id="17a16-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>).</span></span>

<span data-ttu-id="17a16-119">Os modelos incluem o script de `blazor.webassembly.js` que manipula:</span><span class="sxs-lookup"><span data-stu-id="17a16-119">The templates include the `blazor.webassembly.js` script that handles:</span></span>

* <span data-ttu-id="17a16-120">Baixar o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="17a16-121">Inicialização do tempo de execução para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="17a16-122">O modelo de hospedagem Webassembly mais incrivelmente oferece vários benefícios:</span><span class="sxs-lookup"><span data-stu-id="17a16-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="17a16-123">Não há nenhuma dependência do lado do servidor .NET.</span><span class="sxs-lookup"><span data-stu-id="17a16-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="17a16-124">O aplicativo está totalmente funcionando depois de baixado para o cliente.</span><span class="sxs-lookup"><span data-stu-id="17a16-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="17a16-125">Recursos e funcionalidades do cliente são totalmente aproveitados.</span><span class="sxs-lookup"><span data-stu-id="17a16-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="17a16-126">O trabalho é descarregado do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="17a16-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="17a16-127">Um servidor Web ASP.NET Core não é necessário para hospedar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="17a16-128">Cenários de implantação sem servidor são possíveis (por exemplo, servindo o aplicativo de uma CDN).</span><span class="sxs-lookup"><span data-stu-id="17a16-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="17a16-129">Há desvantagens na Hospedagem de Webassembly mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="17a16-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="17a16-130">O aplicativo é restrito aos recursos do navegador.</span><span class="sxs-lookup"><span data-stu-id="17a16-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="17a16-131">Hardware e software de cliente com capacidade (por exemplo, suporte a Webassembly) é necessário.</span><span class="sxs-lookup"><span data-stu-id="17a16-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="17a16-132">O tamanho do download é maior e os aplicativos demoram mais para serem carregados.</span><span class="sxs-lookup"><span data-stu-id="17a16-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="17a16-133">O suporte ao tempo de execução e às ferramentas do .NET é menos maduro.</span><span class="sxs-lookup"><span data-stu-id="17a16-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="17a16-134">Por exemplo, existem limitações em [.net Standard](/dotnet/standard/net-standard) suporte e depuração.</span><span class="sxs-lookup"><span data-stu-id="17a16-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="17a16-135">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="17a16-135">Blazor Server</span></span>

<span data-ttu-id="17a16-136">Com o modelo de Hospedagem de servidor mais incrivelmente, o aplicativo é executado no servidor de dentro de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="17a16-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="17a16-137">As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="17a16-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![O navegador interage com o aplicativo (hospedado dentro de um aplicativo ASP.NET Core) no servidor por meio de uma conexão de sinalização.](hosting-models/_static/blazor-server.png)

<span data-ttu-id="17a16-139">Para criar um aplicativo mais novo usando o modelo de Hospedagem de servidor mais novo, use o modelo de **aplicativo de servidor** ASP.NET Core mais incrivelmente ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="17a16-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="17a16-140">O aplicativo ASP.NET Core hospeda o aplicativo de servidor mais incrivelmente e cria o ponto de extremidade do Signalr onde os clientes se conectam.</span><span class="sxs-lookup"><span data-stu-id="17a16-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="17a16-141">O aplicativo ASP.NET Core referencia a classe `Startup` do aplicativo a ser adicionada:</span><span class="sxs-lookup"><span data-stu-id="17a16-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="17a16-142">Serviços do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-142">Server-side services.</span></span>
* <span data-ttu-id="17a16-143">O aplicativo para o pipeline de tratamento de solicitação.</span><span class="sxs-lookup"><span data-stu-id="17a16-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="17a16-144">O script de `blazor.server.js`&dagger; estabelece a conexão do cliente.</span><span class="sxs-lookup"><span data-stu-id="17a16-144">The `blazor.server.js` script&dagger; establishes the client connection.</span></span> <span data-ttu-id="17a16-145">É responsabilidade do aplicativo persistir e restaurar o estado do aplicativo, conforme necessário (por exemplo, no caso de uma conexão de rede perdida).</span><span class="sxs-lookup"><span data-stu-id="17a16-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="17a16-146">O modelo de Hospedagem de servidor mais incrivelmente oferece vários benefícios:</span><span class="sxs-lookup"><span data-stu-id="17a16-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="17a16-147">O tamanho do download é significativamente menor do que um aplicativo Webassembly mais grande e o aplicativo é carregado com muito mais rapidez.</span><span class="sxs-lookup"><span data-stu-id="17a16-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="17a16-148">O aplicativo aproveita totalmente os recursos do servidor, incluindo o uso de qualquer API compatível com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="17a16-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="17a16-149">O .NET Core no servidor é usado para executar o aplicativo, portanto, as ferramentas .NET existentes, como depuração, funcionam conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="17a16-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="17a16-150">Há suporte para clientes finos.</span><span class="sxs-lookup"><span data-stu-id="17a16-150">Thin clients are supported.</span></span> <span data-ttu-id="17a16-151">Por exemplo, os aplicativos de servidor mais úteis funcionam com navegadores que não dão suporte ao Webassembly e a dispositivos com restrição de recursos.</span><span class="sxs-lookup"><span data-stu-id="17a16-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="17a16-152">A base .NET/C# código do aplicativo, incluindo o código de componente do aplicativo, não é servida aos clientes.</span><span class="sxs-lookup"><span data-stu-id="17a16-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="17a16-153">Há desvantagens na Hospedagem de servidores mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="17a16-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="17a16-154">A latência mais alta geralmente existe.</span><span class="sxs-lookup"><span data-stu-id="17a16-154">Higher latency usually exists.</span></span> <span data-ttu-id="17a16-155">Cada interação do usuário envolve um salto de rede.</span><span class="sxs-lookup"><span data-stu-id="17a16-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="17a16-156">Não há suporte offline.</span><span class="sxs-lookup"><span data-stu-id="17a16-156">There's no offline support.</span></span> <span data-ttu-id="17a16-157">Se a conexão do cliente falhar, o aplicativo para de funcionar.</span><span class="sxs-lookup"><span data-stu-id="17a16-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="17a16-158">A escalabilidade é desafiadora para aplicativos com muitos usuários.</span><span class="sxs-lookup"><span data-stu-id="17a16-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="17a16-159">O servidor deve gerenciar várias conexões de cliente e manipular o estado do cliente.</span><span class="sxs-lookup"><span data-stu-id="17a16-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="17a16-160">Um servidor de ASP.NET Core é necessário para atender ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="17a16-161">Cenários de implantação sem servidor não são possíveis (por exemplo, servir o aplicativo de uma CDN).</span><span class="sxs-lookup"><span data-stu-id="17a16-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="17a16-162">&dagger;o script de `blazor.server.js` é servido por meio de um recurso inserido na estrutura compartilhada ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="17a16-162">&dagger;The `blazor.server.js` script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="17a16-163">Comparação com a interface do usuário renderizada pelo servidor</span><span class="sxs-lookup"><span data-stu-id="17a16-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="17a16-164">Uma maneira de entender os aplicativos de servidor mais incrivelmente é entender como ele difere dos modelos tradicionais de renderização da interface do usuário em aplicativos ASP.NET Core usando exibições do Razor ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="17a16-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="17a16-165">Ambos os modelos usam a linguagem Razor para descrever o conteúdo HTML, mas são significativamente diferentes na forma como a marcação é renderizada.</span><span class="sxs-lookup"><span data-stu-id="17a16-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="17a16-166">Quando uma página Razor ou exibição é renderizada, cada linha do código do Razor emite HTML na forma de texto.</span><span class="sxs-lookup"><span data-stu-id="17a16-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="17a16-167">Após a renderização, o servidor descarta a página ou a instância de exibição, incluindo qualquer estado produzido.</span><span class="sxs-lookup"><span data-stu-id="17a16-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="17a16-168">Quando outra solicitação para a página ocorre, por exemplo, quando a validação do servidor falha e o resumo de validação é exibido:</span><span class="sxs-lookup"><span data-stu-id="17a16-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="17a16-169">A página inteira é reprocessada para o texto HTML novamente.</span><span class="sxs-lookup"><span data-stu-id="17a16-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="17a16-170">A página é enviada ao cliente.</span><span class="sxs-lookup"><span data-stu-id="17a16-170">The page is sent to the client.</span></span>

<span data-ttu-id="17a16-171">Um aplicativo mais fácil é composto de elementos reutilizáveis da interface do usuário chamada de *componentes*.</span><span class="sxs-lookup"><span data-stu-id="17a16-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="17a16-172">Um componente contém C# código, marcação e outros componentes.</span><span class="sxs-lookup"><span data-stu-id="17a16-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="17a16-173">Quando um componente é renderizado, um mais claro produz um gráfico dos componentes incluídos de forma semelhante a um XML ou Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="17a16-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="17a16-174">Esse grafo inclui o estado do componente mantido em Propriedades e campos.</span><span class="sxs-lookup"><span data-stu-id="17a16-174">This graph includes component state held in properties and fields.</span></span> <span data-ttu-id="17a16-175">O mais claro avalia o grafo de componentes para produzir uma representação binária da marcação.</span><span class="sxs-lookup"><span data-stu-id="17a16-175">Blazor evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="17a16-176">O formato binário pode ser:</span><span class="sxs-lookup"><span data-stu-id="17a16-176">The binary format can be:</span></span>

* <span data-ttu-id="17a16-177">Transformado em texto HTML (durante o pré-processamento).</span><span class="sxs-lookup"><span data-stu-id="17a16-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="17a16-178">Usado para atualizar a marcação com eficiência durante a renderização regular.</span><span class="sxs-lookup"><span data-stu-id="17a16-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="17a16-179">Uma atualização de interface do usuário no mais incrivelmente é disparada por:</span><span class="sxs-lookup"><span data-stu-id="17a16-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="17a16-180">Interação do usuário, como selecionar um botão.</span><span class="sxs-lookup"><span data-stu-id="17a16-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="17a16-181">Gatilhos de aplicativo, como um temporizador.</span><span class="sxs-lookup"><span data-stu-id="17a16-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="17a16-182">O grafo é rerenderizado e uma *comparação* de interface do usuário (diferença) é calculada.</span><span class="sxs-lookup"><span data-stu-id="17a16-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="17a16-183">Essa diferença é o menor conjunto de edições DOM necessárias para atualizar a interface do usuário no cliente.</span><span class="sxs-lookup"><span data-stu-id="17a16-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="17a16-184">A comparação é enviada ao cliente em um formato binário e aplicada pelo navegador.</span><span class="sxs-lookup"><span data-stu-id="17a16-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="17a16-185">Um componente é descartado depois que o usuário navega para fora dele no cliente.</span><span class="sxs-lookup"><span data-stu-id="17a16-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="17a16-186">Embora um usuário esteja interagindo com um componente, o estado do componente (serviços, recursos) deve ser mantido na memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="17a16-187">Como o estado de muitos componentes pode ser mantido pelo servidor simultaneamente, o esgotamento de memória é uma preocupação que deve ser resolvida.</span><span class="sxs-lookup"><span data-stu-id="17a16-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="17a16-188">Para obter orientação sobre como criar um aplicativo de servidor mais incrivelmente para garantir o melhor uso da memória do servidor, consulte <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="17a16-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="17a16-189">Integrar componentes do Razor em aplicativos Razor Pages e MVC</span><span class="sxs-lookup"><span data-stu-id="17a16-189">Integrate Razor components into Razor Pages and MVC apps</span></span>

#### <a name="use-components-in-pages-and-views"></a><span data-ttu-id="17a16-190">Usar componentes em páginas e exibições</span><span class="sxs-lookup"><span data-stu-id="17a16-190">Use components in pages and views</span></span>

<span data-ttu-id="17a16-191">Um aplicativo Razor Pages ou MVC existente pode integrar componentes Razor em páginas e exibições:</span><span class="sxs-lookup"><span data-stu-id="17a16-191">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="17a16-192">No arquivo de layout do aplicativo ( *_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="17a16-192">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="17a16-193">Adicione a seguinte marcação de `<base>` ao elemento `<head>`:</span><span class="sxs-lookup"><span data-stu-id="17a16-193">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="17a16-194">O valor de `href` (o *caminho base do aplicativo*) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz (`/`).</span><span class="sxs-lookup"><span data-stu-id="17a16-194">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="17a16-195">Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do artigo <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="17a16-195">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="17a16-196">O arquivo *_Layout. cshtml* está localizado na pasta *páginas/compartilhada* em um aplicativo Razor pages ou *exibições/pastas compartilhadas* em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="17a16-196">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="17a16-197">Adicione uma marca de `<script>` para o script mais *incrivelmente. Server. js* dentro da marca de fechamento `</body>`:</span><span class="sxs-lookup"><span data-stu-id="17a16-197">Add a `<script>` tag for the *blazor.server.js* script inside of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="17a16-198">A estrutura adiciona o mais de um script mais *. Server. js* ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-198">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="17a16-199">Não é necessário adicionar manualmente o script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-199">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="17a16-200">Adicione um arquivo *_Imports. Razor* à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace`, para o namespace do aplicativo):</span><span class="sxs-lookup"><span data-stu-id="17a16-200">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="17a16-201">Em `Startup.ConfigureServices`, adicione o serviço de servidor mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="17a16-201">In `Startup.ConfigureServices`, add the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="17a16-202">Em `Startup.Configure`, adicione o ponto de extremidade do Hub mais incrivelmente a `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="17a16-202">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="17a16-203">Integre componentes em qualquer página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="17a16-203">Integrate components into any page or view.</span></span> <span data-ttu-id="17a16-204">Para obter mais informações, consulte a seção *integrar componentes em Razor Pages e aplicativos MVC* do artigo <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="17a16-204">For more information, see the *Integrate components into Razor Pages and MVC apps* section of the <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> article.</span></span>

#### <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="17a16-205">Usar componentes roteáveis em um aplicativo Razor Pages</span><span class="sxs-lookup"><span data-stu-id="17a16-205">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="17a16-206">Para dar suporte a componentes roteáveis do Razor em aplicativos Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="17a16-206">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="17a16-207">Siga as orientações na seção [usar componentes em páginas e exibições](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="17a16-207">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="17a16-208">Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="17a16-208">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="17a16-209">Adicione um arquivo *_Host. cshtml* à pasta *páginas* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="17a16-209">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="17a16-210">Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="17a16-210">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="17a16-211">Adicione uma rota de baixa prioridade para a página *_Host. cshtml* à configuração do ponto de extremidade no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="17a16-211">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="17a16-212">Adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-212">Add routable components to the app.</span></span> <span data-ttu-id="17a16-213">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="17a16-213">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="17a16-214">Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta ao arquivo *pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="17a16-214">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Pages/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="17a16-215">Para obter mais informações, consulte <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="17a16-215">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

#### <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="17a16-216">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="17a16-216">Use routable components in an MVC app</span></span>

<span data-ttu-id="17a16-217">Para dar suporte a componentes roteáveis do Razor em aplicativos MVC:</span><span class="sxs-lookup"><span data-stu-id="17a16-217">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="17a16-218">Siga as orientações na seção [usar componentes em páginas e exibições](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="17a16-218">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="17a16-219">Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="17a16-219">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="17a16-220">Adicione um arquivo *_Host. cshtml* à pasta *views/Home* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="17a16-220">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="17a16-221">Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="17a16-221">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="17a16-222">Adicione uma ação ao controlador Home:</span><span class="sxs-lookup"><span data-stu-id="17a16-222">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="17a16-223">Adicione uma rota de baixa prioridade para a ação do controlador que retorna a exibição *_Host. cshtml* para a configuração do ponto de extremidade no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="17a16-223">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="17a16-224">Crie uma pasta de *páginas* e adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-224">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="17a16-225">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="17a16-225">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="17a16-226">Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta ao arquivo *views/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="17a16-226">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="17a16-227">Para obter mais informações, consulte <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="17a16-227">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

### <a name="circuits"></a><span data-ttu-id="17a16-228">Circuitos</span><span class="sxs-lookup"><span data-stu-id="17a16-228">Circuits</span></span>

<span data-ttu-id="17a16-229">Um aplicativo de servidor mais elaborado é criado com base no [signalr ASP.NET Core](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="17a16-229">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="17a16-230">Cada cliente se comunica com o servidor por meio de uma ou mais conexões de Signalr chamadas de *circuito*.</span><span class="sxs-lookup"><span data-stu-id="17a16-230">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="17a16-231">Um circuito é uma abstração de mais de uma vez sobre conexões de sinalização que podem tolerar interrupções de rede temporárias.</span><span class="sxs-lookup"><span data-stu-id="17a16-231">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="17a16-232">Quando um cliente mais novo perceber que a conexão do Signalr está desconectada, ele tentará se reconectar ao servidor usando uma nova conexão com o Signalr.</span><span class="sxs-lookup"><span data-stu-id="17a16-232">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="17a16-233">Cada tela do navegador (guia do navegador ou iframe) que está conectada a um aplicativo de servidor mais incrivelmente usa uma conexão de Signalr.</span><span class="sxs-lookup"><span data-stu-id="17a16-233">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="17a16-234">Essa é ainda outra distinção importante em comparação com os aplicativos típicos renderizados pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-234">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="17a16-235">Em um aplicativo renderizado pelo servidor, a abertura do mesmo aplicativo em várias telas do navegador geralmente não se traduz em demandas de recursos adicionais no servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-235">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="17a16-236">Em um aplicativo de servidor mais incrivelmente, cada tela do navegador requer um circuito separado e instâncias separadas do estado do componente a serem gerenciadas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-236">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

<span data-ttu-id="17a16-237">O mais incrivelmente considera fechar uma guia do navegador ou navegar até uma URL externa um encerramento *normal* .</span><span class="sxs-lookup"><span data-stu-id="17a16-237">Blazor considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="17a16-238">No caso de um encerramento normal, o circuito e os recursos associados são lançados imediatamente.</span><span class="sxs-lookup"><span data-stu-id="17a16-238">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="17a16-239">Um cliente também pode se desconectar não normalmente, por exemplo, devido a uma interrupção de rede.</span><span class="sxs-lookup"><span data-stu-id="17a16-239">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> <span data-ttu-id="17a16-240">O servidor mais incrivelmente armazena circuitos desconectados para um intervalo configurável para permitir que o cliente se reconecte.</span><span class="sxs-lookup"><span data-stu-id="17a16-240">Blazor Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="17a16-241">Para obter mais informações, consulte a seção [reconexão com o mesmo servidor](#reconnection-to-the-same-server) .</span><span class="sxs-lookup"><span data-stu-id="17a16-241">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="17a16-242">Latência da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="17a16-242">UI Latency</span></span>

<span data-ttu-id="17a16-243">A latência da interface do usuário é o tempo que leva de uma ação iniciada até a hora em que a interface do usuário é atualizada.</span><span class="sxs-lookup"><span data-stu-id="17a16-243">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="17a16-244">Valores menores para latência de interface do usuário são imperativos para um aplicativo se sentir responsivo a um usuário.</span><span class="sxs-lookup"><span data-stu-id="17a16-244">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="17a16-245">Em um aplicativo de servidor mais novo, cada ação é enviada para o servidor, processada e uma comparação da interface do usuário é enviada de volta.</span><span class="sxs-lookup"><span data-stu-id="17a16-245">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="17a16-246">Consequentemente, a latência da interface do usuário é a soma da latência da rede e a latência do servidor no processamento da ação.</span><span class="sxs-lookup"><span data-stu-id="17a16-246">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="17a16-247">Para um aplicativo de linha de negócios limitado a uma rede corporativa privada, o efeito sobre as percepções do usuário de latência devido à latência da rede geralmente é imperceptível.</span><span class="sxs-lookup"><span data-stu-id="17a16-247">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="17a16-248">Para um aplicativo implantado pela Internet, a latência pode ser perceptível para os usuários, especialmente se os usuários forem amplamente distribuídos geograficamente.</span><span class="sxs-lookup"><span data-stu-id="17a16-248">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="17a16-249">O uso de memória também pode contribuir para a latência do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-249">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="17a16-250">O aumento do uso da memória resulta em uma coleta de lixo frequente ou na paginação da memória no disco, e conseqüentemente aumenta a latência da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="17a16-250">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="17a16-251">Para obter mais informações, consulte <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="17a16-251">For more information, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="17a16-252">Aplicativos de servidor mais incrivelmente devem ser otimizados para minimizar a latência da interface do usuário, reduzindo a latência de rede e o uso de memória.</span><span class="sxs-lookup"><span data-stu-id="17a16-252">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="17a16-253">Para obter uma abordagem para medir a latência de rede, consulte <xref:host-and-deploy/blazor/server#measure-network-latency>.</span><span class="sxs-lookup"><span data-stu-id="17a16-253">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="17a16-254">Para obter mais informações sobre o Signalr e o mais incrivelmente, consulte:</span><span class="sxs-lookup"><span data-stu-id="17a16-254">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a><span data-ttu-id="17a16-255">Conexão com o servidor</span><span class="sxs-lookup"><span data-stu-id="17a16-255">Connection to the server</span></span>

<span data-ttu-id="17a16-256">Os aplicativos de servidor mais poseriais exigem uma conexão ativa de sinalização com o servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-256">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="17a16-257">Se a conexão for perdida, o aplicativo tentará se reconectar ao servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-257">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="17a16-258">Desde que o estado do cliente ainda esteja na memória, a sessão do cliente será retomada sem perder o estado.</span><span class="sxs-lookup"><span data-stu-id="17a16-258">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

#### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="17a16-259">Reconexão com o mesmo servidor</span><span class="sxs-lookup"><span data-stu-id="17a16-259">Reconnection to the same server</span></span>

<span data-ttu-id="17a16-260">Um aplicativo de servidor mais incrivelmente é renderizado em resposta à primeira solicitação do cliente, que configura o estado da interface do usuário no servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-260">A Blazor Server app prerenders in response to the first client request, which sets up the UI state on the server.</span></span> <span data-ttu-id="17a16-261">Quando o cliente tenta criar uma conexão de Signalr, o cliente deve se reconectar ao mesmo servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-261">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> <span data-ttu-id="17a16-262">Os aplicativos de servidor mais poseriais que usam mais de um servidor back-end devem implementar *sessões adesivas* para conexões de sinalização.</span><span class="sxs-lookup"><span data-stu-id="17a16-262">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="17a16-263">É recomendável usar o [serviço de signalr do Azure](/azure/azure-signalr) para aplicativos de servidor mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="17a16-263">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="17a16-264">O serviço permite escalar verticalmente um aplicativo de servidor mais recente para um grande número de conexões de Signaler simultâneas.</span><span class="sxs-lookup"><span data-stu-id="17a16-264">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="17a16-265">As sessões adesivas são habilitadas para o serviço de Signaler do Azure, definindo a opção de `ServerStickyMode` do serviço ou o valor de configuração como `Required`.</span><span class="sxs-lookup"><span data-stu-id="17a16-265">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="17a16-266">Para obter mais informações, consulte <xref:host-and-deploy/blazor/server#signalr-configuration>.</span><span class="sxs-lookup"><span data-stu-id="17a16-266">For more information, see <xref:host-and-deploy/blazor/server#signalr-configuration>.</span></span>

<span data-ttu-id="17a16-267">Ao usar o IIS, as sessões adesivas são habilitadas com Application Request Routing.</span><span class="sxs-lookup"><span data-stu-id="17a16-267">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="17a16-268">Para obter mais informações, consulte [balanceamento de carga http usando Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="17a16-268">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="17a16-269">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="17a16-269">Reflect the connection state in the UI</span></span>

<span data-ttu-id="17a16-270">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="17a16-270">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="17a16-271">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="17a16-271">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="17a16-272">Para personalizar a interface do usuário, defina um elemento com uma `id` de `components-reconnect-modal` na `<body>` da página do Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="17a16-272">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="17a16-273">A tabela a seguir descreve as classes CSS aplicadas ao elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="17a16-273">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="17a16-274">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="17a16-274">CSS class</span></span>                       | <span data-ttu-id="17a16-275">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="17a16-275">Indicates&hellip;</span></span> |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | <span data-ttu-id="17a16-276">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="17a16-276">A lost connection.</span></span> <span data-ttu-id="17a16-277">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="17a16-277">The client is attempting to reconnect.</span></span> <span data-ttu-id="17a16-278">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="17a16-278">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="17a16-279">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-279">An active connection is re-established to the server.</span></span> <span data-ttu-id="17a16-280">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="17a16-280">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="17a16-281">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="17a16-281">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="17a16-282">Para tentar a reconexão, chame `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="17a16-282">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="17a16-283">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="17a16-283">Reconnection rejected.</span></span> <span data-ttu-id="17a16-284">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="17a16-284">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="17a16-285">Para recarregar o aplicativo, chame `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="17a16-285">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="17a16-286">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="17a16-286">This connection state may result when:</span></span><ul><li><span data-ttu-id="17a16-287">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="17a16-287">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="17a16-288">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="17a16-288">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="17a16-289">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="17a16-289">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="17a16-290">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="17a16-290">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="17a16-291">Reconexão com estado após o pré-processamento</span><span class="sxs-lookup"><span data-stu-id="17a16-291">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="17a16-292">Os aplicativos de servidor mais poseriais são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="17a16-292">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="17a16-293">Isso é configurado na página Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="17a16-293">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="17a16-294">`RenderMode` configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="17a16-294">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="17a16-295">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="17a16-295">Is prerendered into the page.</span></span>
* <span data-ttu-id="17a16-296">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="17a16-296">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="17a16-297">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="17a16-297">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="17a16-298">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="17a16-298">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="17a16-299">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="17a16-299">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="17a16-300">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="17a16-300">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="17a16-301">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="17a16-301">Output from the component isn't included.</span></span> <span data-ttu-id="17a16-302">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="17a16-302">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="17a16-303">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="17a16-303">Renders the component into static HTML.</span></span> |

<span data-ttu-id="17a16-304">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="17a16-304">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="17a16-305">Quando `RenderMode` é `ServerPrerendered`, o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="17a16-305">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="17a16-306">Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente*e o componente agora é interativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-306">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="17a16-307">Se o método de ciclo de vida de [{Async} OnInitialized](xref:blazor/lifecycle#component-initialization-methods) para inicializar o componente estiver presente, o método será executado *duas vezes*:</span><span class="sxs-lookup"><span data-stu-id="17a16-307">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="17a16-308">Quando o componente é renderizado estaticamente.</span><span class="sxs-lookup"><span data-stu-id="17a16-308">When the component is prerendered statically.</span></span>
* <span data-ttu-id="17a16-309">Depois que a conexão do servidor tiver sido estabelecida.</span><span class="sxs-lookup"><span data-stu-id="17a16-309">After the server connection has been established.</span></span>

<span data-ttu-id="17a16-310">Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.</span><span class="sxs-lookup"><span data-stu-id="17a16-310">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="17a16-311">Para evitar o cenário de processamento duplo em um aplicativo do Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="17a16-311">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="17a16-312">Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-312">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="17a16-313">Use o identificador durante o pré-processamento para salvar o estado do componente.</span><span class="sxs-lookup"><span data-stu-id="17a16-313">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="17a16-314">Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="17a16-314">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="17a16-315">O código a seguir demonstra uma `WeatherForecastService` atualizada em um aplicativo do Blazor Server baseado em modelo que evita a renderização dupla:</span><span class="sxs-lookup"><span data-stu-id="17a16-315">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="17a16-316">Renderizar componentes interativos com estado de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="17a16-316">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="17a16-317">Os componentes interativos com estado podem ser adicionados a uma página ou exibição Razor.</span><span class="sxs-lookup"><span data-stu-id="17a16-317">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="17a16-318">Quando a página ou a exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="17a16-318">When the page or view renders:</span></span>

* <span data-ttu-id="17a16-319">O componente é renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="17a16-319">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="17a16-320">O estado inicial do componente usado para o pré-processamento é perdido.</span><span class="sxs-lookup"><span data-stu-id="17a16-320">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="17a16-321">O novo estado do componente é criado quando a conexão de SignalR é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="17a16-321">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="17a16-322">A seguinte página Razor renderiza um componente `Counter`:</span><span class="sxs-lookup"><span data-stu-id="17a16-322">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="17a16-323">Renderizar componentes não interativos de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="17a16-323">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="17a16-324">Na página Razor a seguir, o componente `Counter` é processado estaticamente com um valor inicial que é especificado usando um formulário:</span><span class="sxs-lookup"><span data-stu-id="17a16-324">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="17a16-325">Como `MyComponent` é processado estaticamente, o componente não pode ser interativo.</span><span class="sxs-lookup"><span data-stu-id="17a16-325">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="17a16-326">Detectar quando o aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="17a16-326">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="17a16-327">Configurar o cliente do SignalR para aplicativos do Blazor Server</span><span class="sxs-lookup"><span data-stu-id="17a16-327">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="17a16-328">Às vezes, você precisa configurar o cliente SignalR usado por aplicativos do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="17a16-328">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="17a16-329">Por exemplo, talvez você queira configurar o registro em log no cliente SignalR para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="17a16-329">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="17a16-330">Para configurar o cliente do SignalR no arquivo *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="17a16-330">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="17a16-331">Adicione um atributo `autostart="false"` à marca de `<script>` para o script de `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="17a16-331">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="17a16-332">Chame `Blazor.start` e passe um objeto de configuração que especifica o construtor de SignalR.</span><span class="sxs-lookup"><span data-stu-id="17a16-332">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="17a16-333">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="17a16-333">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:tutorials/signalr-blazor-webassembly>
