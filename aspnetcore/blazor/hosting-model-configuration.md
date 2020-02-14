---
title: ASP.NET Core Blazor configuração do modelo de hospedagem
author: guardrex
description: Saiba mais sobre a configuração do modelo de hospedagem Blazor, incluindo como integrar componentes do Razor em aplicativos Razor Pages e MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 91dd1aa32bb5165845eb4794377a50ccbd01adc3
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77215056"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="aaf54-103">ASP.NET Core configuração de modelo de hospedagem mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="aaf54-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="aaf54-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="aaf54-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="aaf54-105">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="aaf54-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="aaf54-106">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="aaf54-106">Blazor Server</span></span>

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="aaf54-107">Integrar componentes do Razor em aplicativos Razor Pages e MVC</span><span class="sxs-lookup"><span data-stu-id="aaf54-107">Integrate Razor components into Razor Pages and MVC apps</span></span>

#### <a name="use-components-in-pages-and-views"></a><span data-ttu-id="aaf54-108">Usar componentes em páginas e exibições</span><span class="sxs-lookup"><span data-stu-id="aaf54-108">Use components in pages and views</span></span>

<span data-ttu-id="aaf54-109">Um aplicativo Razor Pages ou MVC existente pode integrar componentes Razor em páginas e exibições:</span><span class="sxs-lookup"><span data-stu-id="aaf54-109">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="aaf54-110">No arquivo de layout do aplicativo ( *_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="aaf54-110">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="aaf54-111">Adicione a seguinte marcação de `<base>` ao elemento `<head>`:</span><span class="sxs-lookup"><span data-stu-id="aaf54-111">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="aaf54-112">O valor de `href` (o *caminho base do aplicativo*) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz (`/`).</span><span class="sxs-lookup"><span data-stu-id="aaf54-112">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="aaf54-113">Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do artigo <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="aaf54-113">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="aaf54-114">O arquivo *_Layout. cshtml* está localizado na pasta *páginas/compartilhada* em um aplicativo Razor pages ou *exibições/pastas compartilhadas* em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="aaf54-114">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="aaf54-115">Adicione uma marca de `<script>` para o script mais claro *. Server. js* antes da marca de fechamento `</body>`:</span><span class="sxs-lookup"><span data-stu-id="aaf54-115">Add a `<script>` tag for the *blazor.server.js* script right before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="aaf54-116">A estrutura adiciona o mais de um script mais *. Server. js* ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aaf54-116">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="aaf54-117">Não é necessário adicionar manualmente o script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aaf54-117">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="aaf54-118">Adicione um arquivo *_Imports. Razor* à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace`, para o namespace do aplicativo):</span><span class="sxs-lookup"><span data-stu-id="aaf54-118">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="aaf54-119">Em `Startup.ConfigureServices`, registre o serviço de servidor mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="aaf54-119">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="aaf54-120">Em `Startup.Configure`, adicione o ponto de extremidade do Hub mais incrivelmente a `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="aaf54-120">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="aaf54-121">Integre componentes em qualquer página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="aaf54-121">Integrate components into any page or view.</span></span> <span data-ttu-id="aaf54-122">Para obter mais informações, consulte a seção *integrar componentes em Razor Pages e aplicativos MVC* do artigo <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="aaf54-122">For more information, see the *Integrate components into Razor Pages and MVC apps* section of the <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> article.</span></span>

#### <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="aaf54-123">Usar componentes roteáveis em um aplicativo Razor Pages</span><span class="sxs-lookup"><span data-stu-id="aaf54-123">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="aaf54-124">Para dar suporte a componentes roteáveis do Razor em aplicativos Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="aaf54-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="aaf54-125">Siga as orientações na seção [usar componentes em páginas e exibições](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="aaf54-125">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="aaf54-126">Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="aaf54-126">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="aaf54-127">Adicione um arquivo *_Host. cshtml* à pasta *páginas* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="aaf54-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="aaf54-128">Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="aaf54-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="aaf54-129">Adicione uma rota de baixa prioridade para a página *_Host. cshtml* à configuração do ponto de extremidade no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="aaf54-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="aaf54-130">Adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aaf54-130">Add routable components to the app.</span></span> <span data-ttu-id="aaf54-131">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="aaf54-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="aaf54-132">Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta ao arquivo *pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="aaf54-132">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Pages/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="aaf54-133">Para obter mais informações, consulte <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="aaf54-133">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

#### <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="aaf54-134">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="aaf54-134">Use routable components in an MVC app</span></span>

<span data-ttu-id="aaf54-135">Para dar suporte a componentes roteáveis do Razor em aplicativos MVC:</span><span class="sxs-lookup"><span data-stu-id="aaf54-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="aaf54-136">Siga as orientações na seção [usar componentes em páginas e exibições](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="aaf54-136">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="aaf54-137">Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="aaf54-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="aaf54-138">Adicione um arquivo *_Host. cshtml* à pasta *views/Home* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="aaf54-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="aaf54-139">Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="aaf54-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="aaf54-140">Adicione uma ação ao controlador Home:</span><span class="sxs-lookup"><span data-stu-id="aaf54-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="aaf54-141">Adicione uma rota de baixa prioridade para a ação do controlador que retorna a exibição *_Host. cshtml* para a configuração do ponto de extremidade no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="aaf54-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="aaf54-142">Crie uma pasta de *páginas* e adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aaf54-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="aaf54-143">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="aaf54-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="aaf54-144">Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta ao arquivo *views/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="aaf54-144">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="aaf54-145">Para obter mais informações, consulte <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="aaf54-145">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="aaf54-146">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="aaf54-146">Reflect the connection state in the UI</span></span>

<span data-ttu-id="aaf54-147">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="aaf54-147">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="aaf54-148">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="aaf54-148">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="aaf54-149">Para personalizar a interface do usuário, defina um elemento com uma `id` de `components-reconnect-modal` na `<body>` da página do Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="aaf54-149">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="aaf54-150">A tabela a seguir descreve as classes CSS aplicadas ao elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="aaf54-150">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="aaf54-151">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="aaf54-151">CSS class</span></span>                       | <span data-ttu-id="aaf54-152">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="aaf54-152">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="aaf54-153">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="aaf54-153">A lost connection.</span></span> <span data-ttu-id="aaf54-154">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="aaf54-154">The client is attempting to reconnect.</span></span> <span data-ttu-id="aaf54-155">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="aaf54-155">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="aaf54-156">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="aaf54-156">An active connection is re-established to the server.</span></span> <span data-ttu-id="aaf54-157">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="aaf54-157">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="aaf54-158">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="aaf54-158">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="aaf54-159">Para tentar a reconexão, chame `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="aaf54-159">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="aaf54-160">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="aaf54-160">Reconnection rejected.</span></span> <span data-ttu-id="aaf54-161">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="aaf54-161">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="aaf54-162">Para recarregar o aplicativo, chame `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="aaf54-162">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="aaf54-163">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="aaf54-163">This connection state may result when:</span></span><ul><li><span data-ttu-id="aaf54-164">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="aaf54-164">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="aaf54-165">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="aaf54-165">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="aaf54-166">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="aaf54-166">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="aaf54-167">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="aaf54-167">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="aaf54-168">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="aaf54-168">Render mode</span></span>

<span data-ttu-id="aaf54-169">Os aplicativos de servidor mais poseriais são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="aaf54-169">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="aaf54-170">Isso é configurado na página Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="aaf54-170">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="aaf54-171">`RenderMode` configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="aaf54-171">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="aaf54-172">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="aaf54-172">Is prerendered into the page.</span></span>
* <span data-ttu-id="aaf54-173">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="aaf54-173">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="aaf54-174">Descrição</span><span class="sxs-lookup"><span data-stu-id="aaf54-174">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="aaf54-175">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="aaf54-175">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="aaf54-176">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="aaf54-176">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="aaf54-177">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="aaf54-177">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="aaf54-178">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="aaf54-178">Output from the component isn't included.</span></span> <span data-ttu-id="aaf54-179">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="aaf54-179">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="aaf54-180">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="aaf54-180">Renders the component into static HTML.</span></span> |

<span data-ttu-id="aaf54-181">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="aaf54-181">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="aaf54-182">Renderizar componentes interativos com estado de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="aaf54-182">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="aaf54-183">Os componentes interativos com estado podem ser adicionados a uma página ou exibição Razor.</span><span class="sxs-lookup"><span data-stu-id="aaf54-183">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="aaf54-184">Quando a página ou a exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="aaf54-184">When the page or view renders:</span></span>

* <span data-ttu-id="aaf54-185">O componente é renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="aaf54-185">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="aaf54-186">O estado inicial do componente usado para o pré-processamento é perdido.</span><span class="sxs-lookup"><span data-stu-id="aaf54-186">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="aaf54-187">O novo estado do componente é criado quando a conexão de SignalR é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="aaf54-187">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="aaf54-188">A seguinte página Razor renderiza um componente `Counter`:</span><span class="sxs-lookup"><span data-stu-id="aaf54-188">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="aaf54-189">Renderizar componentes não interativos de páginas e exibições do Razor</span><span class="sxs-lookup"><span data-stu-id="aaf54-189">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="aaf54-190">Na página Razor a seguir, o componente `Counter` é processado estaticamente com um valor inicial que é especificado usando um formulário:</span><span class="sxs-lookup"><span data-stu-id="aaf54-190">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="aaf54-191">Como `MyComponent` é processado estaticamente, o componente não pode ser interativo.</span><span class="sxs-lookup"><span data-stu-id="aaf54-191">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="aaf54-192">Configurar o cliente do SignalR para aplicativos do Blazor Server</span><span class="sxs-lookup"><span data-stu-id="aaf54-192">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="aaf54-193">Às vezes, você precisa configurar o cliente SignalR usado por aplicativos do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="aaf54-193">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="aaf54-194">Por exemplo, talvez você queira configurar o registro em log no cliente SignalR para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="aaf54-194">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="aaf54-195">Para configurar o cliente do SignalR no arquivo *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="aaf54-195">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="aaf54-196">Adicione um atributo `autostart="false"` à marca de `<script>` para o script de `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="aaf54-196">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="aaf54-197">Chame `Blazor.start` e passe um objeto de configuração que especifica o construtor de SignalR.</span><span class="sxs-lookup"><span data-stu-id="aaf54-197">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
