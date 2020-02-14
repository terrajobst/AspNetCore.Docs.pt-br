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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core configuração de modelo de hospedagem mais incrivelmente

Por [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Este artigo aborda a configuração do modelo de hospedagem.

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Servidor Blazor

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a>Integrar componentes do Razor em aplicativos Razor Pages e MVC

#### <a name="use-components-in-pages-and-views"></a>Usar componentes em páginas e exibições

Um aplicativo Razor Pages ou MVC existente pode integrar componentes Razor em páginas e exibições:

1. No arquivo de layout do aplicativo ( *_Layout. cshtml*):

   * Adicione a seguinte marcação de `<base>` ao elemento `<head>`:

     ```html
     <base href="~/" />
     ```

     O valor de `href` (o *caminho base do aplicativo*) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz (`/`). Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do artigo <xref:host-and-deploy/blazor/index#app-base-path>.

     O arquivo *_Layout. cshtml* está localizado na pasta *páginas/compartilhada* em um aplicativo Razor pages ou *exibições/pastas compartilhadas* em um aplicativo MVC.

   * Adicione uma marca de `<script>` para o script mais claro *. Server. js* antes da marca de fechamento `</body>`:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     A estrutura adiciona o mais de um script mais *. Server. js* ao aplicativo. Não é necessário adicionar manualmente o script ao aplicativo.

1. Adicione um arquivo *_Imports. Razor* à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace`, para o namespace do aplicativo):

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

1. Em `Startup.ConfigureServices`, registre o serviço de servidor mais incrivelmente:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Em `Startup.Configure`, adicione o ponto de extremidade do Hub mais incrivelmente a `app.UseEndpoints`:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integre componentes em qualquer página ou exibição. Para obter mais informações, consulte a seção *integrar componentes em Razor Pages e aplicativos MVC* do artigo <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.

#### <a name="use-routable-components-in-a-razor-pages-app"></a>Usar componentes roteáveis em um aplicativo Razor Pages

Para dar suporte a componentes roteáveis do Razor em aplicativos Razor Pages:

1. Siga as orientações na seção [usar componentes em páginas e exibições](#use-components-in-pages-and-views) .

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

   Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta ao arquivo *pages/_ViewImports. cshtml* . Para obter mais informações, consulte <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.

#### <a name="use-routable-components-in-an-mvc-app"></a>Usar componentes roteáveis em um aplicativo MVC

Para dar suporte a componentes roteáveis do Razor em aplicativos MVC:

1. Siga as orientações na seção [usar componentes em páginas e exibições](#use-components-in-pages-and-views) .

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

   Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta ao arquivo *views/_ViewImports. cshtml* . Para obter mais informações, consulte <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.

### <a name="reflect-the-connection-state-in-the-ui"></a>Refletir o estado da conexão na interface do usuário

Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar. Se a reconexão falhar, o usuário receberá a opção de tentar novamente.

Para personalizar a interface do usuário, defina um elemento com uma `id` de `components-reconnect-modal` na `<body>` da página do Razor *_Host. cshtml* :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

A tabela a seguir descreve as classes CSS aplicadas ao elemento `components-reconnect-modal`.

| Classe CSS                       | Indica&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Uma conexão perdida. O cliente está tentando se reconectar. Mostrar o modal. |
| `components-reconnect-hide`     | Uma conexão ativa é restabelecida com o servidor. Ocultar a janela restrita. |
| `components-reconnect-failed`   | Falha na reconexão, provavelmente devido a uma falha de rede. Para tentar a reconexão, chame `window.Blazor.reconnect()`. |
| `components-reconnect-rejected` | Reconexão rejeitada. O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido. Para recarregar o aplicativo, chame `location.reload()`. Esse estado de conexão pode resultar quando:<ul><li>Ocorre uma falha no circuito do lado do servidor.</li><li>O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário. As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</li><li>O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</li></ul> |

### <a name="render-mode"></a>Modo de renderização

Os aplicativos de servidor mais poseriais são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida. Isso é configurado na página Razor *_Host. cshtml* :

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode` configura se o componente:

* É renderizado na página.
* É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.

| `RenderMode`        | Descrição |
| ------------------- | ----------- |
| `ServerPrerendered` | Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor. |
| `Server`            | Renderiza um marcador para um aplicativo do Blazor Server. A saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor. |
| `Static`            | Renderiza o componente em HTML estático. |

Não há suporte para a renderização de componentes de servidor de uma página HTML estática.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Renderizar componentes interativos com estado de páginas e exibições do Razor

Os componentes interativos com estado podem ser adicionados a uma página ou exibição Razor.

Quando a página ou a exibição renderiza:

* O componente é renderizado com a página ou exibição.
* O estado inicial do componente usado para o pré-processamento é perdido.
* O novo estado do componente é criado quando a conexão de SignalR é estabelecida.

A seguinte página Razor renderiza um componente `Counter`:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Renderizar componentes não interativos de páginas e exibições do Razor

Na página Razor a seguir, o componente `Counter` é processado estaticamente com um valor inicial que é especificado usando um formulário:

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

Como `MyComponent` é processado estaticamente, o componente não pode ser interativo.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Configurar o cliente do SignalR para aplicativos do Blazor Server

Às vezes, você precisa configurar o cliente SignalR usado por aplicativos do Blazor Server. Por exemplo, talvez você queira configurar o registro em log no cliente SignalR para diagnosticar um problema de conexão.

Para configurar o cliente do SignalR no arquivo *pages/_Host. cshtml* :

* Adicione um atributo `autostart="false"` à marca de `<script>` para o script de `blazor.server.js`.
* Chame `Blazor.start` e passe um objeto de configuração que especifica o construtor de SignalR.

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
