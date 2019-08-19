---
title: Modelos de hospedagem mais amASP.NET Core
author: guardrex
description: Entenda os modelos de hospedagem mais podirecionais do lado do cliente e do lado do servidor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/hosting-models
ms.openlocfilehash: 64393e826cb17550085f468f5916fca55973908f
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993383"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="90f7a-103">Modelos de hospedagem mais amASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90f7a-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="90f7a-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="90f7a-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="90f7a-105">O mais elaborador é uma estrutura da Web criada para executar o lado do cliente no navegador em um tempo de execução .net baseado em [Webassembly](https://webassembly.org/) (*mais de um lado do cliente*) ou no lado do servidor no ASP.NET Core (*mais alto servidor*).</span><span class="sxs-lookup"><span data-stu-id="90f7a-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor client-side*) or server-side in ASP.NET Core (*Blazor server-side*).</span></span> <span data-ttu-id="90f7a-106">Independentemente do modelo de hospedagem, os modelos de aplicativo e componente *são os mesmos*.</span><span class="sxs-lookup"><span data-stu-id="90f7a-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="90f7a-107">Para criar um projeto para os modelos de hospedagem descritos neste artigo, consulte <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="90f7a-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="client-side"></a><span data-ttu-id="90f7a-108">Lado do cliente</span><span class="sxs-lookup"><span data-stu-id="90f7a-108">Client-side</span></span>

<span data-ttu-id="90f7a-109">O modelo de hospedagem principal para o mais alto é executar o lado do cliente no navegador no Webassembly.</span><span class="sxs-lookup"><span data-stu-id="90f7a-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="90f7a-110">O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="90f7a-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="90f7a-111">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="90f7a-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="90f7a-112">As atualizações da interface do usuário e o tratamento de eventos ocorrem no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="90f7a-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="90f7a-113">Os ativos do aplicativo são implantados como arquivos estáticos em um servidor Web ou serviço capaz de fornecer conteúdo estático aos clientes.</span><span class="sxs-lookup"><span data-stu-id="90f7a-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![No lado do cliente: O aplicativo mais incrivelmente é executado em um thread de interface do usuário dentro do navegador.](hosting-models/_static/client-side.png)

<span data-ttu-id="90f7a-115">Para criar um aplicativo mais novo usando o modelo de hospedagem do lado do cliente, use o modelo de **aplicativo Webassembly** mais[novo (dotNet New blazorwasm](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="90f7a-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="90f7a-116">Depois de selecionar o modelo de **aplicativo Webassembly** mais novo, você tem a opção de configurar o aplicativo para usar um ASP.NET Core back-end marcando a caixa de seleção **ASP.NET Core hospedado** ([dotnet New blazorwasm – hospedado](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="90f7a-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="90f7a-117">O aplicativo ASP.NET Core serve para o aplicativo mais incrivelmente para os clientes.</span><span class="sxs-lookup"><span data-stu-id="90f7a-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="90f7a-118">O aplicativo do lado do cliente mais novo pode interagir com o servidor pela rede usando chamadas de API Web ou [signalr](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="90f7a-118">The Blazor client-side app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="90f7a-119">Os modelos incluem o script mais *incrivelmente. Webassembly. js* que manipula:</span><span class="sxs-lookup"><span data-stu-id="90f7a-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="90f7a-120">Baixar o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90f7a-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="90f7a-121">Inicialização do tempo de execução para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90f7a-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="90f7a-122">O modelo de hospedagem do lado do cliente oferece vários benefícios:</span><span class="sxs-lookup"><span data-stu-id="90f7a-122">The client-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="90f7a-123">Não há nenhuma dependência do lado do servidor .NET.</span><span class="sxs-lookup"><span data-stu-id="90f7a-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="90f7a-124">O aplicativo está totalmente funcionando depois de baixado para o cliente.</span><span class="sxs-lookup"><span data-stu-id="90f7a-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="90f7a-125">Recursos e funcionalidades do cliente são totalmente aproveitados.</span><span class="sxs-lookup"><span data-stu-id="90f7a-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="90f7a-126">O trabalho é descarregado do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="90f7a-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="90f7a-127">Um servidor Web ASP.NET Core não é necessário para hospedar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90f7a-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="90f7a-128">Cenários de implantação sem servidor são possíveis (por exemplo, servindo o aplicativo de uma CDN).</span><span class="sxs-lookup"><span data-stu-id="90f7a-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="90f7a-129">Há desvantagens na hospedagem do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="90f7a-129">There are downsides to client-side hosting:</span></span>

* <span data-ttu-id="90f7a-130">O aplicativo é restrito aos recursos do navegador.</span><span class="sxs-lookup"><span data-stu-id="90f7a-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="90f7a-131">Hardware e software de cliente com capacidade (por exemplo, suporte a Webassembly) é necessário.</span><span class="sxs-lookup"><span data-stu-id="90f7a-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="90f7a-132">O tamanho do download é maior e os aplicativos demoram mais para serem carregados.</span><span class="sxs-lookup"><span data-stu-id="90f7a-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="90f7a-133">O suporte ao tempo de execução e às ferramentas do .NET é menos maduro.</span><span class="sxs-lookup"><span data-stu-id="90f7a-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="90f7a-134">Por exemplo, existem limitações em [.net Standard](/dotnet/standard/net-standard) suporte e depuração.</span><span class="sxs-lookup"><span data-stu-id="90f7a-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="server-side"></a><span data-ttu-id="90f7a-135">Lado do servidor</span><span class="sxs-lookup"><span data-stu-id="90f7a-135">Server-side</span></span>

<span data-ttu-id="90f7a-136">Com o modelo de hospedagem do lado do servidor, o aplicativo é executado no servidor de dentro de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="90f7a-136">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="90f7a-137">As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="90f7a-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![O navegador interage com o aplicativo (hospedado dentro de um aplicativo ASP.NET Core) no servidor por meio de uma conexão de sinalização.](hosting-models/_static/server-side.png)

<span data-ttu-id="90f7a-139">Para criar um aplicativo mais novo usando o modelo de hospedagem do lado do servidor, use o modelo de **aplicativo de servidor** ASP.NET Core mais alto ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="90f7a-139">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="90f7a-140">O aplicativo ASP.NET Core hospeda o aplicativo do lado do servidor e cria o ponto de extremidade do Signalr onde os clientes se conectam.</span><span class="sxs-lookup"><span data-stu-id="90f7a-140">The ASP.NET Core app hosts the server-side app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="90f7a-141">O aplicativo ASP.NET Core referencia a classe do `Startup` aplicativo a ser adicionada:</span><span class="sxs-lookup"><span data-stu-id="90f7a-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="90f7a-142">Serviços do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="90f7a-142">Server-side services.</span></span>
* <span data-ttu-id="90f7a-143">O aplicativo para o pipeline de tratamento de solicitação.</span><span class="sxs-lookup"><span data-stu-id="90f7a-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="90f7a-144">O script&dagger; mais *incrivelmente. Server. js* estabelece a conexão do cliente.</span><span class="sxs-lookup"><span data-stu-id="90f7a-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="90f7a-145">É responsabilidade do aplicativo persistir e restaurar o estado do aplicativo, conforme necessário (por exemplo, no caso de uma conexão de rede perdida).</span><span class="sxs-lookup"><span data-stu-id="90f7a-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="90f7a-146">O modelo de hospedagem do lado do servidor oferece vários benefícios:</span><span class="sxs-lookup"><span data-stu-id="90f7a-146">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="90f7a-147">O tamanho do download é significativamente menor do que um aplicativo do lado do cliente e o aplicativo é carregado muito mais rapidamente.</span><span class="sxs-lookup"><span data-stu-id="90f7a-147">Download size is significantly smaller than a client-side app, and the app loads much faster.</span></span>
* <span data-ttu-id="90f7a-148">O aplicativo aproveita totalmente os recursos do servidor, incluindo o uso de qualquer API compatível com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="90f7a-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="90f7a-149">O .NET Core no servidor é usado para executar o aplicativo, portanto, as ferramentas .NET existentes, como depuração, funcionam conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="90f7a-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="90f7a-150">Há suporte para clientes finos.</span><span class="sxs-lookup"><span data-stu-id="90f7a-150">Thin clients are supported.</span></span> <span data-ttu-id="90f7a-151">Por exemplo, aplicativos do lado do servidor funcionam com navegadores que não dão suporte ao Webassembly e a dispositivos com restrição de recursos.</span><span class="sxs-lookup"><span data-stu-id="90f7a-151">For example, server-side apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="90f7a-152">A base .NET/C# código do aplicativo, incluindo o código de componente do aplicativo, não é servida aos clientes.</span><span class="sxs-lookup"><span data-stu-id="90f7a-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="90f7a-153">Há desvantagens na hospedagem do lado do servidor:</span><span class="sxs-lookup"><span data-stu-id="90f7a-153">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="90f7a-154">A latência mais alta geralmente existe.</span><span class="sxs-lookup"><span data-stu-id="90f7a-154">Higher latency usually exists.</span></span> <span data-ttu-id="90f7a-155">Cada interação do usuário envolve um salto de rede.</span><span class="sxs-lookup"><span data-stu-id="90f7a-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="90f7a-156">Não há suporte offline.</span><span class="sxs-lookup"><span data-stu-id="90f7a-156">There's no offline support.</span></span> <span data-ttu-id="90f7a-157">Se a conexão do cliente falhar, o aplicativo para de funcionar.</span><span class="sxs-lookup"><span data-stu-id="90f7a-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="90f7a-158">A escalabilidade é desafiadora para aplicativos com muitos usuários.</span><span class="sxs-lookup"><span data-stu-id="90f7a-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="90f7a-159">O servidor deve gerenciar várias conexões de cliente e manipular o estado do cliente.</span><span class="sxs-lookup"><span data-stu-id="90f7a-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="90f7a-160">Um servidor de ASP.NET Core é necessário para atender ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90f7a-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="90f7a-161">Cenários de implantação sem servidor não são possíveis (por exemplo, servir o aplicativo de uma CDN).</span><span class="sxs-lookup"><span data-stu-id="90f7a-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="90f7a-162">&dagger;O script mais *incrivelmente. Server. js* é servido por meio de um recurso incorporado na estrutura compartilhada ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="90f7a-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="90f7a-163">Reconexão com o mesmo servidor</span><span class="sxs-lookup"><span data-stu-id="90f7a-163">Reconnection to the same server</span></span>

<span data-ttu-id="90f7a-164">Os aplicativos mais no lado do servidor exigem uma conexão ativa do signalr com o servidor.</span><span class="sxs-lookup"><span data-stu-id="90f7a-164">Blazor server-side apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="90f7a-165">Se a conexão for perdida, o aplicativo tentará se reconectar ao servidor.</span><span class="sxs-lookup"><span data-stu-id="90f7a-165">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="90f7a-166">Desde que o estado do cliente ainda esteja na memória, a sessão do cliente será retomada sem perder o estado.</span><span class="sxs-lookup"><span data-stu-id="90f7a-166">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>
 
<span data-ttu-id="90f7a-167">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="90f7a-167">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="90f7a-168">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="90f7a-168">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="90f7a-169">Para personalizar a interface do usuário, defina um `components-reconnect-modal` elemento com `id` como na página Razor *_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="90f7a-169">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="90f7a-170">O cliente atualiza esse elemento com uma das seguintes classes CSS com base no estado da conexão:</span><span class="sxs-lookup"><span data-stu-id="90f7a-170">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>
 
* <span data-ttu-id="90f7a-171">`components-reconnect-show`&ndash; Mostre a interface do usuário para indicar que a conexão foi perdida e que o cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="90f7a-171">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="90f7a-172">`components-reconnect-hide`&ndash; O cliente tem uma conexão ativa, oculta a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="90f7a-172">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="90f7a-173">`components-reconnect-failed`&ndash; Falha na reconexão.</span><span class="sxs-lookup"><span data-stu-id="90f7a-173">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="90f7a-174">Para tentar reconectar novamente, chame `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="90f7a-174">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="90f7a-175">Reconexão com estado após o pré-processamento</span><span class="sxs-lookup"><span data-stu-id="90f7a-175">Stateful reconnection after prerendering</span></span>
 
<span data-ttu-id="90f7a-176">Aplicativos de mais alto do lado do servidor são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="90f7a-176">Blazor server-side apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="90f7a-177">Isso é configurado na página Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="90f7a-177">This is set up in the *_Host.cshtml* Razor page:</span></span>
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
<span data-ttu-id="90f7a-178">O cliente se reconecta ao servidor com o mesmo estado que foi usado para PreRender o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90f7a-178">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="90f7a-179">Se o estado do aplicativo ainda estiver na memória, o estado do componente não será reprocessado depois que a conexão do Signalr for estabelecida.</span><span class="sxs-lookup"><span data-stu-id="90f7a-179">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="90f7a-180">Renderizar componentes interativos com estado de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="90f7a-180">Render stateful interactive components from Razor pages and views</span></span>
 
<span data-ttu-id="90f7a-181">Os componentes interativos com estado podem ser adicionados a uma página ou exibição Razor.</span><span class="sxs-lookup"><span data-stu-id="90f7a-181">Stateful interactive components can be added to a Razor page or view.</span></span> <span data-ttu-id="90f7a-182">Quando a página ou a exibição é renderizada, o componente é renderizado com ele.</span><span class="sxs-lookup"><span data-stu-id="90f7a-182">When the page or view renders, the component is prerendered with it.</span></span> <span data-ttu-id="90f7a-183">O aplicativo se reconecta ao estado do componente depois que a conexão do cliente é estabelecida, desde que o estado ainda esteja na memória.</span><span class="sxs-lookup"><span data-stu-id="90f7a-183">The app then reconnects to the component state once the client connection is established as long as the state is still in memory.</span></span>
 
<span data-ttu-id="90f7a-184">Por exemplo, a seguinte página Razor renderiza um `Counter` componente com uma contagem inicial que é especificada usando um formulário:</span><span class="sxs-lookup"><span data-stu-id="90f7a-184">For example, the following Razor page renders a `Counter` component with an initial count that's specified using a form:</span></span>
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="90f7a-185">Detectar quando o aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="90f7a-185">Detect when the app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a><span data-ttu-id="90f7a-186">Configurar o cliente Signalr para aplicativos do lado do servidor mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="90f7a-186">Configure the SignalR client for Blazor server-side apps</span></span>
 
<span data-ttu-id="90f7a-187">Às vezes, você precisa configurar o cliente Signalr usado pelos aplicativos mais usados no servidor.</span><span class="sxs-lookup"><span data-stu-id="90f7a-187">Sometimes, you need to configure the SignalR client used by Blazor server-side apps.</span></span> <span data-ttu-id="90f7a-188">Por exemplo, talvez você queira configurar o registro em log no cliente do Signalr para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="90f7a-188">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
 
<span data-ttu-id="90f7a-189">Para configurar o cliente Signalr no arquivo *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="90f7a-189">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="90f7a-190">Adicione um `autostart="false"` atributo `<script>` à marca para o script mais *incrivelmente. Server. js* .</span><span class="sxs-lookup"><span data-stu-id="90f7a-190">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="90f7a-191">Chame `Blazor.start` e passe um objeto de configuração que especifica o construtor de signalr.</span><span class="sxs-lookup"><span data-stu-id="90f7a-191">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="90f7a-192">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="90f7a-192">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
