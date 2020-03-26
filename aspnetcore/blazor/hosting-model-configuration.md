---
title: ASP.NET Core Blazor configuração do modelo de hospedagem
author: guardrex
description: Saiba mais sobre a configuração do modelo de hospedagem Blazor, incluindo como integrar componentes do Razor em aplicativos Razor Pages e MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306428"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="2cf57-103">ASP.NET Core configuração de modelo de hospedagem mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="2cf57-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="2cf57-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2cf57-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2cf57-105">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2cf57-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="2cf57-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="2cf57-106">Blazor WebAssembly</span></span>

<span data-ttu-id="2cf57-107">A partir da versão ASP.NET Core 3,2 Preview 3, o Webassembly mais claro dá suporte à configuração de:</span><span class="sxs-lookup"><span data-stu-id="2cf57-107">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="2cf57-108">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="2cf57-108">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="2cf57-109">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="2cf57-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="2cf57-110">Em um aplicativo hospedado mais, o [ambiente de tempo de execução](xref:fundamentals/environments) é o mesmo que o valor do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="2cf57-110">In a Blazor Hosted app, the [runtime environment](xref:fundamentals/environments) is the same as the server app's value.</span></span>

<span data-ttu-id="2cf57-111">Ao executar o aplicativo localmente, o ambiente assume como padrão o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2cf57-111">When running the app locally, the environment defaults to Development.</span></span> <span data-ttu-id="2cf57-112">Quando o aplicativo é publicado, o ambiente assume como padrão a produção.</span><span class="sxs-lookup"><span data-stu-id="2cf57-112">When the app is published, the environment defaults to Production.</span></span> <span data-ttu-id="2cf57-113">Para obter mais informações, incluindo como configurar o ambiente, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2cf57-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

> [!WARNING]
> <span data-ttu-id="2cf57-114">A configuração em um aplicativo Webassembly mais incrivelmente é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="2cf57-114">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="2cf57-115">**Não armazene os segredos ou as credenciais do aplicativo na configuração.**</span><span class="sxs-lookup"><span data-stu-id="2cf57-115">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="2cf57-116">Os arquivos de configuração são armazenados em cache para uso offline.</span><span class="sxs-lookup"><span data-stu-id="2cf57-116">Configuration files are cached for offline use.</span></span> <span data-ttu-id="2cf57-117">Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="2cf57-117">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="2cf57-118">A edição de arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="2cf57-118">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="2cf57-119">Os usuários têm versões em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="2cf57-119">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="2cf57-120">Os arquivos *Service-Worker. js* e *Service-Worker-assets. js* do PWA devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="2cf57-120">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="2cf57-121">Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="2cf57-121">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="2cf57-122">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="2cf57-122">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="2cf57-123">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="2cf57-123">Reflect the connection state in the UI</span></span>

<span data-ttu-id="2cf57-124">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="2cf57-124">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="2cf57-125">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="2cf57-125">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="2cf57-126">Para personalizar a interface do usuário, defina um elemento com uma `id` de `components-reconnect-modal` na `<body>` da página do Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2cf57-126">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="2cf57-127">A tabela a seguir descreve as classes CSS aplicadas ao elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="2cf57-127">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="2cf57-128">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="2cf57-128">CSS class</span></span>                       | <span data-ttu-id="2cf57-129">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="2cf57-129">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="2cf57-130">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="2cf57-130">A lost connection.</span></span> <span data-ttu-id="2cf57-131">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="2cf57-131">The client is attempting to reconnect.</span></span> <span data-ttu-id="2cf57-132">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="2cf57-132">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="2cf57-133">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="2cf57-133">An active connection is re-established to the server.</span></span> <span data-ttu-id="2cf57-134">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="2cf57-134">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="2cf57-135">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="2cf57-135">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="2cf57-136">Para tentar a reconexão, chame `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="2cf57-136">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="2cf57-137">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="2cf57-137">Reconnection rejected.</span></span> <span data-ttu-id="2cf57-138">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="2cf57-138">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="2cf57-139">Para recarregar o aplicativo, chame `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="2cf57-139">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="2cf57-140">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="2cf57-140">This connection state may result when:</span></span><ul><li><span data-ttu-id="2cf57-141">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="2cf57-141">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="2cf57-142">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="2cf57-142">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="2cf57-143">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="2cf57-143">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="2cf57-144">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="2cf57-144">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="2cf57-145">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="2cf57-145">Render mode</span></span>

<span data-ttu-id="2cf57-146">Os aplicativos de servidor mais poseriais são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="2cf57-146">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="2cf57-147">Isso é configurado na página Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2cf57-147">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="2cf57-148">`RenderMode` configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="2cf57-148">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="2cf57-149">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="2cf57-149">Is prerendered into the page.</span></span>
* <span data-ttu-id="2cf57-150">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="2cf57-150">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="2cf57-151">Descrição</span><span class="sxs-lookup"><span data-stu-id="2cf57-151">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="2cf57-152">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="2cf57-152">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="2cf57-153">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="2cf57-153">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="2cf57-154">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="2cf57-154">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="2cf57-155">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="2cf57-155">Output from the component isn't included.</span></span> <span data-ttu-id="2cf57-156">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="2cf57-156">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="2cf57-157">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="2cf57-157">Renders the component into static HTML.</span></span> |

<span data-ttu-id="2cf57-158">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="2cf57-158">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="2cf57-159">Renderizar componentes interativos com estado de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="2cf57-159">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="2cf57-160">Os componentes interativos com estado podem ser adicionados a uma página ou exibição Razor.</span><span class="sxs-lookup"><span data-stu-id="2cf57-160">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="2cf57-161">Quando a página ou a exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="2cf57-161">When the page or view renders:</span></span>

* <span data-ttu-id="2cf57-162">O componente é renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="2cf57-162">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="2cf57-163">O estado inicial do componente usado para o pré-processamento é perdido.</span><span class="sxs-lookup"><span data-stu-id="2cf57-163">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="2cf57-164">O novo estado do componente é criado quando a conexão de SignalR é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="2cf57-164">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="2cf57-165">A seguinte página Razor renderiza um componente `Counter`:</span><span class="sxs-lookup"><span data-stu-id="2cf57-165">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="2cf57-166">Renderizar componentes não interativos de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="2cf57-166">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="2cf57-167">Na página Razor a seguir, o componente `Counter` é processado estaticamente com um valor inicial que é especificado usando um formulário:</span><span class="sxs-lookup"><span data-stu-id="2cf57-167">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="2cf57-168">Como `MyComponent` é processado estaticamente, o componente não pode ser interativo.</span><span class="sxs-lookup"><span data-stu-id="2cf57-168">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="2cf57-169">Configurar o cliente do SignalR para aplicativos do Blazor Server</span><span class="sxs-lookup"><span data-stu-id="2cf57-169">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="2cf57-170">Às vezes, você precisa configurar o cliente SignalR usado por aplicativos do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="2cf57-170">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="2cf57-171">Por exemplo, talvez você queira configurar o registro em log no cliente SignalR para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="2cf57-171">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="2cf57-172">Para configurar o cliente do SignalR no arquivo *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2cf57-172">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="2cf57-173">Adicione um atributo `autostart="false"` à marca de `<script>` para o script de `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="2cf57-173">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="2cf57-174">Chame `Blazor.start` e passe um objeto de configuração que especifica o construtor de SignalR.</span><span class="sxs-lookup"><span data-stu-id="2cf57-174">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
