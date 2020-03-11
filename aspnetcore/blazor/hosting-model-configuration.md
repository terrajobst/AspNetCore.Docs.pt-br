---
title: ASP.NET Core Blazor configuração do modelo de hospedagem
author: guardrex
description: Saiba mais sobre a configuração do modelo de hospedagem Blazor, incluindo como integrar componentes do Razor em aplicativos Razor Pages e MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658301"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="b2c3a-103">ASP.NET Core configuração de modelo de hospedagem mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="b2c3a-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="b2c3a-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b2c3a-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b2c3a-105">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="b2c3a-106">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="b2c3a-106">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="b2c3a-107">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="b2c3a-107">Reflect the connection state in the UI</span></span>

<span data-ttu-id="b2c3a-108">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-108">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="b2c3a-109">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-109">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="b2c3a-110">Para personalizar a interface do usuário, defina um elemento com uma `id` de `components-reconnect-modal` na `<body>` da página do Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b2c3a-110">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="b2c3a-111">A tabela a seguir descreve as classes CSS aplicadas ao elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-111">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="b2c3a-112">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="b2c3a-112">CSS class</span></span>                       | <span data-ttu-id="b2c3a-113">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="b2c3a-113">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="b2c3a-114">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-114">A lost connection.</span></span> <span data-ttu-id="b2c3a-115">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-115">The client is attempting to reconnect.</span></span> <span data-ttu-id="b2c3a-116">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-116">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="b2c3a-117">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-117">An active connection is re-established to the server.</span></span> <span data-ttu-id="b2c3a-118">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-118">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="b2c3a-119">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-119">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="b2c3a-120">Para tentar a reconexão, chame `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-120">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="b2c3a-121">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-121">Reconnection rejected.</span></span> <span data-ttu-id="b2c3a-122">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-122">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="b2c3a-123">Para recarregar o aplicativo, chame `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-123">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="b2c3a-124">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="b2c3a-124">This connection state may result when:</span></span><ul><li><span data-ttu-id="b2c3a-125">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-125">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="b2c3a-126">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-126">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="b2c3a-127">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-127">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="b2c3a-128">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-128">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="b2c3a-129">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="b2c3a-129">Render mode</span></span>

<span data-ttu-id="b2c3a-130">Os aplicativos de servidor mais poseriais são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-130">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="b2c3a-131">Isso é configurado na página Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b2c3a-131">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="b2c3a-132">`RenderMode` configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="b2c3a-132">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="b2c3a-133">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-133">Is prerendered into the page.</span></span>
* <span data-ttu-id="b2c3a-134">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-134">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="b2c3a-135">Descrição</span><span class="sxs-lookup"><span data-stu-id="b2c3a-135">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="b2c3a-136">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-136">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="b2c3a-137">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="b2c3a-138">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-138">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="b2c3a-139">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-139">Output from the component isn't included.</span></span> <span data-ttu-id="b2c3a-140">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-140">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="b2c3a-141">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-141">Renders the component into static HTML.</span></span> |

<span data-ttu-id="b2c3a-142">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-142">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="b2c3a-143">Renderizar componentes interativos com estado de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="b2c3a-143">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="b2c3a-144">Os componentes interativos com estado podem ser adicionados a uma página ou exibição Razor.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-144">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="b2c3a-145">Quando a página ou a exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="b2c3a-145">When the page or view renders:</span></span>

* <span data-ttu-id="b2c3a-146">O componente é renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-146">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="b2c3a-147">O estado inicial do componente usado para o pré-processamento é perdido.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-147">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="b2c3a-148">O novo estado do componente é criado quando a conexão de SignalR é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-148">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="b2c3a-149">A seguinte página Razor renderiza um componente `Counter`:</span><span class="sxs-lookup"><span data-stu-id="b2c3a-149">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="b2c3a-150">Renderizar componentes não interativos de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="b2c3a-150">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="b2c3a-151">Na página Razor a seguir, o componente `Counter` é processado estaticamente com um valor inicial que é especificado usando um formulário:</span><span class="sxs-lookup"><span data-stu-id="b2c3a-151">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="b2c3a-152">Como `MyComponent` é processado estaticamente, o componente não pode ser interativo.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-152">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="b2c3a-153">Configurar o cliente do SignalR para aplicativos do Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b2c3a-153">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="b2c3a-154">Às vezes, você precisa configurar o cliente SignalR usado por aplicativos do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="b2c3a-155">Por exemplo, talvez você queira configurar o registro em log no cliente SignalR para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="b2c3a-156">Para configurar o cliente do SignalR no arquivo *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b2c3a-156">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="b2c3a-157">Adicione um atributo `autostart="false"` à marca de `<script>` para o script de `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b2c3a-158">Chame `Blazor.start` e passe um objeto de configuração que especifica o construtor de SignalR.</span><span class="sxs-lookup"><span data-stu-id="b2c3a-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
