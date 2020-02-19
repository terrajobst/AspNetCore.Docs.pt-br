---
title: Integre ASP.NET Core componentes do Razor em aplicativos Razor Pages e MVC
author: guardrex
description: Saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em aplicativos Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453292"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="9a380-103">Integre ASP.NET Core componentes do Razor em aplicativos Razor Pages e MVC</span><span class="sxs-lookup"><span data-stu-id="9a380-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="9a380-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9a380-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9a380-105">Os componentes do Razor podem ser integrados em aplicativos Razor Pages e MVC.</span><span class="sxs-lookup"><span data-stu-id="9a380-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="9a380-106">Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="9a380-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="9a380-107">Preparar o aplicativo para usar componentes em páginas e exibições</span><span class="sxs-lookup"><span data-stu-id="9a380-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="9a380-108">Um aplicativo Razor Pages ou MVC existente pode integrar componentes Razor em páginas e exibições:</span><span class="sxs-lookup"><span data-stu-id="9a380-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="9a380-109">No arquivo de layout do aplicativo ( *_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="9a380-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="9a380-110">Adicione a seguinte marcação de `<base>` ao elemento `<head>`:</span><span class="sxs-lookup"><span data-stu-id="9a380-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="9a380-111">O valor de `href` (o *caminho base do aplicativo*) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz (`/`).</span><span class="sxs-lookup"><span data-stu-id="9a380-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="9a380-112">Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do artigo <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="9a380-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="9a380-113">O arquivo *_Layout. cshtml* está localizado na pasta *páginas/compartilhada* em um aplicativo Razor pages ou *exibições/pastas compartilhadas* em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="9a380-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="9a380-114">Adicione uma marca de `<script>` para o script mais *incrivelmente. Server. js* imediatamente antes da marca de fechamento `</body>`:</span><span class="sxs-lookup"><span data-stu-id="9a380-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="9a380-115">A estrutura adiciona o mais de um script mais *. Server. js* ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a380-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="9a380-116">Não é necessário adicionar manualmente o script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a380-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="9a380-117">Adicione um arquivo *_Imports. Razor* à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace`, para o namespace do aplicativo):</span><span class="sxs-lookup"><span data-stu-id="9a380-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="9a380-118">Em `Startup.ConfigureServices`, registre o serviço de servidor mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="9a380-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="9a380-119">Em `Startup.Configure`, adicione o ponto de extremidade do Hub mais incrivelmente a `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="9a380-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="9a380-120">Integre componentes em qualquer página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="9a380-120">Integrate components into any page or view.</span></span> <span data-ttu-id="9a380-121">Para obter mais informações, consulte a seção [renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="9a380-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="9a380-122">Usar componentes roteáveis em um aplicativo Razor Pages</span><span class="sxs-lookup"><span data-stu-id="9a380-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="9a380-123">*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="9a380-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="9a380-124">Para dar suporte a componentes roteáveis do Razor em aplicativos Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="9a380-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="9a380-125">Siga as orientações na seção [preparar o aplicativo para usar componentes em páginas e exibições](#prepare-the-app-to-use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="9a380-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="9a380-126">Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="9a380-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="9a380-127">Adicione um arquivo *_Host. cshtml* à pasta *páginas* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="9a380-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="9a380-128">Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="9a380-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="9a380-129">Adicione uma rota de baixa prioridade para a página *_Host. cshtml* à configuração do ponto de extremidade no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9a380-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="9a380-130">Adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a380-130">Add routable components to the app.</span></span> <span data-ttu-id="9a380-131">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9a380-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="9a380-132">Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="9a380-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="9a380-133">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="9a380-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="9a380-134">*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="9a380-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="9a380-135">Para dar suporte a componentes roteáveis do Razor em aplicativos MVC:</span><span class="sxs-lookup"><span data-stu-id="9a380-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="9a380-136">Siga as orientações na seção [preparar o aplicativo para usar componentes em páginas e exibições](#prepare-the-app-to-use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="9a380-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="9a380-137">Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="9a380-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="9a380-138">Adicione um arquivo *_Host. cshtml* à pasta *views/Home* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="9a380-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="9a380-139">Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="9a380-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="9a380-140">Adicione uma ação ao controlador Home:</span><span class="sxs-lookup"><span data-stu-id="9a380-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="9a380-141">Adicione uma rota de baixa prioridade para a ação do controlador que retorna a exibição *_Host. cshtml* para a configuração do ponto de extremidade no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9a380-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="9a380-142">Crie uma pasta de *páginas* e adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a380-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="9a380-143">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9a380-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="9a380-144">Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="9a380-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="9a380-145">Namespaces de componente</span><span class="sxs-lookup"><span data-stu-id="9a380-145">Component namespaces</span></span>

<span data-ttu-id="9a380-146">Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta à página/exibição ou ao arquivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9a380-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="9a380-147">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9a380-147">In the following example:</span></span>

* <span data-ttu-id="9a380-148">Altere `MyAppNamespace` para o namespace do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a380-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="9a380-149">Se uma pasta chamada *Components* não for usada para manter os componentes, altere `Components` para a pasta onde residem os componentes.</span><span class="sxs-lookup"><span data-stu-id="9a380-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="9a380-150">O arquivo *_ViewImports. cshtml* está localizado na pasta *páginas* de um aplicativo Razor pages ou na pasta *views* de um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="9a380-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="9a380-151">Para obter mais informações, consulte <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="9a380-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="9a380-152">Renderizar componentes de uma página ou exibição</span><span class="sxs-lookup"><span data-stu-id="9a380-152">Render components from a page or view</span></span>

<span data-ttu-id="9a380-153">*Esta seção pertence à adição de componentes a páginas ou exibições, em que os componentes não são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="9a380-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="9a380-154">Para renderizar um componente de uma página ou exibição, use o `Component` o auxiliar de marca:</span><span class="sxs-lookup"><span data-stu-id="9a380-154">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="9a380-155">O tipo de parâmetro deve ser serializável em JSON, o que normalmente significa que o tipo deve ter propriedades de construtor e settable padrão.</span><span class="sxs-lookup"><span data-stu-id="9a380-155">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="9a380-156">Por exemplo, você pode especificar um valor para `IncrementAmount` porque o tipo de `IncrementAmount` é um `int`, que é um tipo primitivo suportado pelo serializador JSON.</span><span class="sxs-lookup"><span data-stu-id="9a380-156">For example, you can specify a value for `IncrementAmount` because the type of `IncrementAmount` is an `int`, which is a primitive type supported by the JSON serializer.</span></span>

<span data-ttu-id="9a380-157">`RenderMode` configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="9a380-157">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="9a380-158">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="9a380-158">Is prerendered into the page.</span></span>
* <span data-ttu-id="9a380-159">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="9a380-159">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="9a380-160">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="9a380-160">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="9a380-161">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="9a380-161">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="9a380-162">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="9a380-162">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="9a380-163">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="9a380-163">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="9a380-164">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="9a380-164">Output from the component isn't included.</span></span> <span data-ttu-id="9a380-165">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="9a380-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="9a380-166">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="9a380-166">Renders the component into static HTML.</span></span> |

<span data-ttu-id="9a380-167">Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro.</span><span class="sxs-lookup"><span data-stu-id="9a380-167">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="9a380-168">Os componentes não podem usar cenários específicos de exibição e de página, como exibições parciais e seções.</span><span class="sxs-lookup"><span data-stu-id="9a380-168">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="9a380-169">Para usar a lógica da exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.</span><span class="sxs-lookup"><span data-stu-id="9a380-169">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="9a380-170">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="9a380-170">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="9a380-171">Para obter mais informações sobre como os componentes são renderizados, estado do componente e o auxiliar de marca de `Component`, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="9a380-171">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
