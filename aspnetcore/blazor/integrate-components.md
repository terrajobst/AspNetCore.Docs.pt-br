---
title: Integre ASP.NET Core componentes do Razor em aplicativos Razor Pages e MVC
author: guardrex
description: Saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em aplicativos Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218928"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integre ASP.NET Core componentes do Razor em aplicativos Razor Pages e MVC

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Os componentes do Razor podem ser integrados em aplicativos Razor Pages e MVC. Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>Preparar o aplicativo para usar componentes em páginas e exibições

Um aplicativo Razor Pages ou MVC existente pode integrar componentes Razor em páginas e exibições:

1. No arquivo de layout do aplicativo ( *_Layout. cshtml*):

   * Adicione a seguinte marcação de `<base>` ao elemento `<head>`:

     ```html
     <base href="~/" />
     ```

     O valor de `href` (o *caminho base do aplicativo*) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz (`/`). Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do artigo <xref:host-and-deploy/blazor/index#app-base-path>.

     O arquivo *_Layout. cshtml* está localizado na pasta *páginas/compartilhada* em um aplicativo Razor pages ou *exibições/pastas compartilhadas* em um aplicativo MVC.

   * Adicione uma marca de `<script>` para o script mais *incrivelmente. Server. js* imediatamente antes da marca de fechamento `</body>`:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     A estrutura adiciona o mais de um script mais *. Server. js* ao aplicativo. Não é necessário adicionar manualmente o script ao aplicativo.

1. Adicione um arquivo *_Imports. Razor* à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace`, para o namespace do aplicativo):

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

1. Em `Startup.ConfigureServices`, registre o serviço do Blazor Server:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Em `Startup.Configure`, adicione o ponto de extremidade do hub de Blazor a `app.UseEndpoints`:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integre componentes em qualquer página ou exibição. Para obter mais informações, consulte a seção [renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-razor-pages-app"></a>Usar componentes roteáveis em um aplicativo Razor Pages

*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*

Para dar suporte a componentes roteáveis do Razor em aplicativos Razor Pages:

1. Siga as orientações na seção [preparar o aplicativo para usar componentes em páginas e exibições](#prepare-the-app-to-use-components-in-pages-and-views) .

1. Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:

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

1. Adicione um arquivo *_Host. cshtml* à pasta *páginas* com o seguinte conteúdo:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.

1. Adicione uma rota de baixa prioridade para a página *_Host. cshtml* à configuração do ponto de extremidade no `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Adicione componentes roteáveis ao aplicativo. Por exemplo:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Usar componentes roteáveis em um aplicativo MVC

*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*

Para dar suporte a componentes roteáveis do Razor em aplicativos MVC:

1. Siga as orientações na seção [preparar o aplicativo para usar componentes em páginas e exibições](#prepare-the-app-to-use-components-in-pages-and-views) .

1. Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:

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

1. Adicione um arquivo *_Host. cshtml* à pasta *views/Home* com o seguinte conteúdo:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.

1. Adicione uma ação ao controlador Home:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Adicione uma rota de baixa prioridade para a ação do controlador que retorna a exibição *_Host. cshtml* para a configuração do ponto de extremidade no `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Crie uma pasta de *páginas* e adicione componentes roteáveis ao aplicativo. Por exemplo:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .

## <a name="component-namespaces"></a>Namespaces de componente

Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta à página/exibição ou ao arquivo *_ViewImports. cshtml* . No exemplo a seguir:

* Altere `MyAppNamespace` para o namespace do aplicativo.
* Se uma pasta chamada *Components* não for usada para manter os componentes, altere `Components` para a pasta onde residem os componentes.

```cshtml
@using MyAppNamespace.Components
```

O arquivo *_ViewImports. cshtml* está localizado na pasta *páginas* de um aplicativo Razor pages ou na pasta *views* de um aplicativo MVC.

Para obter mais informações, consulte <xref:blazor/components#import-components>.

## <a name="render-components-from-a-page-or-view"></a>Renderizar componentes de uma página ou exibição

*Esta seção pertence à adição de componentes a páginas ou exibições, em que os componentes não são roteáveis diretamente das solicitações do usuário.*

Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

Para obter mais informações sobre como os componentes são renderizados, estado do componente e o auxiliar de marca de `Component`, consulte os seguintes artigos:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
