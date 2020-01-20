---
title: Modelos de Hospedagem de Blazor ASP.NET Core
author: guardrex
description: Entenda Blazor Webassembly e modelos de hospedagem do Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
- blazor.webassembly.js
uid: blazor/hosting-models
ms.openlocfilehash: c9521acf40317c90d1197660bfa516710263cfc9
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160035"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>Modelos de Hospedagem de Blazor ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor é uma estrutura da Web criada para executar o lado do cliente no navegador em um tempo de execução .NET baseado em [Webassembly](https://webassembly.org/)( *Blazor Webassembly*) ou no lado do servidor no ASP.NET Core ( *Blazor Server*). Independentemente do modelo de hospedagem, os modelos de aplicativo e componente *são os mesmos*.

Para criar um projeto para os modelos de hospedagem descritos neste artigo, consulte <xref:blazor/get-started>.

## <a name="opno-locblazor-webassembly"></a>Blazor Webassembly

O modelo de hospedagem principal para Blazor está executando o lado do cliente no navegador no Webassembly. O aplicativo Blazor, suas dependências e o tempo de execução do .NET são baixados para o navegador. O aplicativo é executado diretamente no thread da interface do usuário do navegador. As atualizações da interface do usuário e o tratamento de eventos ocorrem no mesmo processo. Os ativos do aplicativo são implantados como arquivos estáticos em um servidor Web ou serviço capaz de fornecer conteúdo estático aos clientes.

![[! Parar. Webassembly não LOC (mais alto)]: o [! Parar. O aplicativo no-LOC (mais alto)] é executado em um thread de interface do usuário dentro do navegador.](hosting-models/_static/blazor-webassembly.png)

Para criar um aplicativo Blazor usando o modelo de hospedagem do lado do cliente, use o modelo de **aplicativo Webassembly doBlazor** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Depois de selecionar o modelo de **aplicativo WebassemblyBlazor** , você tem a opção de configurar o aplicativo para usar um back-end ASP.NET Core marcando a caixa de seleção **ASP.NET Core hospedado** ([dotnet New blazorwasm – hospedado](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core serve o aplicativo Blazor aos clientes. O aplicativo Webassembly Blazor pode interagir com o servidor pela rede usando chamadas de API Web ou [SignalR](xref:signalr/introduction).

Os modelos incluem o script de `blazor.webassembly.js` que manipula:

* Baixar o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
* Inicialização do tempo de execução para executar o aplicativo.

O modelo de hospedagem Webassembly Blazor oferece vários benefícios:

* Não há nenhuma dependência do lado do servidor .NET. O aplicativo está totalmente funcionando depois de baixado para o cliente.
* Recursos e funcionalidades do cliente são totalmente aproveitados.
* O trabalho é descarregado do servidor para o cliente.
* Um servidor Web ASP.NET Core não é necessário para hospedar o aplicativo. Cenários de implantação sem servidor são possíveis (por exemplo, servindo o aplicativo de uma CDN).

Há desvantagens em Blazor Hospedagem de Webassembly:

* O aplicativo é restrito aos recursos do navegador.
* Hardware e software de cliente com capacidade (por exemplo, suporte a Webassembly) é necessário.
* O tamanho do download é maior e os aplicativos demoram mais para serem carregados.
* O suporte ao tempo de execução e às ferramentas do .NET é menos maduro. Por exemplo, existem limitações em [.net Standard](/dotnet/standard/net-standard) suporte e depuração.

## <a name="opno-locblazor-server"></a>Servidor de Blazor

Com o modelo de hospedagem do servidor Blazor, o aplicativo é executado no servidor de dentro de um aplicativo ASP.NET Core. As atualizações de interface do usuário, manipulação de eventos e chamadas JavaScript são manipuladas por uma conexão [SignalR](xref:signalr/introduction) .

![O navegador interage com o aplicativo (hospedado dentro de um aplicativo ASP.NET Core) no servidor por meio de um [! Parar. Conexão não-LOC (Signalr)].](hosting-models/_static/blazor-server.png)

Para criar um aplicativo Blazor usando o modelo de hospedagem do Blazor Server, use o modelo de **aplicativo ASP.NET CoreBlazor Server** ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core hospeda o aplicativo Blazor Server e cria o ponto de extremidade SignalR onde os clientes se conectam.

O aplicativo ASP.NET Core referencia a classe `Startup` do aplicativo a ser adicionada:

* Serviços do lado do servidor.
* O aplicativo para o pipeline de tratamento de solicitação.

O script de `blazor.server.js`&dagger; estabelece a conexão do cliente. É responsabilidade do aplicativo persistir e restaurar o estado do aplicativo, conforme necessário (por exemplo, no caso de uma conexão de rede perdida).

O modelo de hospedagem do Blazor Server oferece vários benefícios:

* O tamanho do download é significativamente menor do que um aplicativo Webassembly Blazor e o aplicativo é carregado muito mais rapidamente.
* O aplicativo aproveita totalmente os recursos do servidor, incluindo o uso de qualquer API compatível com o .NET Core.
* O .NET Core no servidor é usado para executar o aplicativo, portanto, as ferramentas .NET existentes, como depuração, funcionam conforme o esperado.
* Há suporte para clientes finos. Por exemplo, os aplicativos do Blazor Server funcionam com navegadores que não dão suporte ao Webassembly e a dispositivos com restrição de recursos.
* A base .NET/C# código do aplicativo, incluindo o código de componente do aplicativo, não é servida aos clientes.

Há desvantagens em Blazor Hospedagem de servidor:

* A latência mais alta geralmente existe. Cada interação do usuário envolve um salto de rede.
* Não há suporte offline. Se a conexão do cliente falhar, o aplicativo para de funcionar.
* A escalabilidade é desafiadora para aplicativos com muitos usuários. O servidor deve gerenciar várias conexões de cliente e manipular o estado do cliente.
* Um servidor de ASP.NET Core é necessário para atender ao aplicativo. Cenários de implantação sem servidor não são possíveis (por exemplo, servir o aplicativo de uma CDN).

&dagger;o script de `blazor.server.js` é servido por meio de um recurso inserido na estrutura compartilhada ASP.NET Core.

### <a name="comparison-to-server-rendered-ui"></a>Comparação com a interface do usuário renderizada pelo servidor

Uma maneira de entender Blazor aplicativos de servidor é entender como ele difere dos modelos tradicionais para renderizar a interface do usuário em aplicativos ASP.NET Core usando exibições do Razor ou Razor Pages. Ambos os modelos usam a linguagem Razor para descrever o conteúdo HTML, mas são significativamente diferentes na forma como a marcação é renderizada.

Quando uma página Razor ou exibição é renderizada, cada linha do código do Razor emite HTML na forma de texto. Após a renderização, o servidor descarta a página ou a instância de exibição, incluindo qualquer estado produzido. Quando outra solicitação para a página ocorre, por exemplo, quando a validação do servidor falha e o resumo de validação é exibido:

* A página inteira é reprocessada para o texto HTML novamente.
* A página é enviada ao cliente.

Um aplicativo Blazor é composto por elementos reutilizáveis da interface do usuário chamada *componentes*. Um componente contém C# código, marcação e outros componentes. Quando um componente é renderizado, Blazor produz um grafo dos componentes incluídos de forma semelhante a um XML (Modelo de Objeto do Documento) ou HTML (DOM). Esse grafo inclui o estado do componente mantido em Propriedades e campos. Blazor avalia o grafo de componente para produzir uma representação binária da marcação. O formato binário pode ser:

* Transformado em texto HTML (durante o pré-processamento).
* Usado para atualizar a marcação com eficiência durante a renderização regular.

Uma atualização de interface do usuário no Blazor é disparada por:

* Interação do usuário, como selecionar um botão.
* Gatilhos de aplicativo, como um temporizador.

O grafo é rerenderizado e uma *comparação* de interface do usuário (diferença) é calculada. Essa diferença é o menor conjunto de edições DOM necessárias para atualizar a interface do usuário no cliente. A comparação é enviada ao cliente em um formato binário e aplicada pelo navegador.

Um componente é descartado depois que o usuário navega para fora dele no cliente. Embora um usuário esteja interagindo com um componente, o estado do componente (serviços, recursos) deve ser mantido na memória do servidor. Como o estado de muitos componentes pode ser mantido pelo servidor simultaneamente, o esgotamento de memória é uma preocupação que deve ser resolvida. Para obter orientação sobre como criar um aplicativo do Blazor Server para garantir o melhor uso da memória do servidor, consulte <xref:security/blazor/server>.

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

   * Adicione uma marca de `<script>` para o script mais *incrivelmente. Server. js* dentro da marca de fechamento `</body>`:

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

1. Em `Startup.ConfigureServices`, adicione o serviço do Blazor Server:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Em `Startup.Configure`, adicione o ponto de extremidade do hub de Blazor a `app.UseEndpoints`:

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

### <a name="circuits"></a>Circuitos

Um aplicativo do Blazor Server é criado sobre [ASP.NET Core SignalR](xref:signalr/introduction). Cada cliente se comunica com o servidor por meio de uma ou mais conexões SignalR chamadas de *circuito*. Um circuito é A abstração Blazorde SignalR conexões que podem tolerar interrupções de rede temporárias. Quando um cliente Blazor vê que a conexão SignalR está desconectada, ele tenta se reconectar ao servidor usando uma nova conexão SignalR.

Cada tela do navegador (guia do navegador ou iframe) que está conectada a um aplicativo do Blazor Server usa uma conexão SignalR. Essa é ainda outra distinção importante em comparação com os aplicativos típicos renderizados pelo servidor. Em um aplicativo renderizado pelo servidor, a abertura do mesmo aplicativo em várias telas do navegador geralmente não se traduz em demandas de recursos adicionais no servidor. Em um aplicativo do Blazor Server, cada tela do navegador requer um circuito separado e instâncias separadas do estado do componente a serem gerenciadas pelo servidor.

Blazor considera fechar uma guia do navegador ou navegar até uma URL externa um encerramento *normal* . No caso de um encerramento normal, o circuito e os recursos associados são lançados imediatamente. Um cliente também pode se desconectar não normalmente, por exemplo, devido a uma interrupção de rede. Blazor Server armazena circuitos desconectados para um intervalo configurável para permitir que o cliente se reconecte. Para obter mais informações, consulte a seção [reconexão com o mesmo servidor](#reconnection-to-the-same-server) .

### <a name="ui-latency"></a>Latência da interface do usuário

A latência da interface do usuário é o tempo que leva de uma ação iniciada até a hora em que a interface do usuário é atualizada. Valores menores para latência de interface do usuário são imperativos para um aplicativo se sentir responsivo a um usuário. Em um aplicativo do Blazor Server, cada ação é enviada ao servidor, processada e uma comparação da interface do usuário é enviada de volta. Consequentemente, a latência da interface do usuário é a soma da latência da rede e a latência do servidor no processamento da ação.

Para um aplicativo de linha de negócios limitado a uma rede corporativa privada, o efeito sobre as percepções do usuário de latência devido à latência da rede geralmente é imperceptível. Para um aplicativo implantado pela Internet, a latência pode ser perceptível para os usuários, especialmente se os usuários forem amplamente distribuídos geograficamente.

O uso de memória também pode contribuir para a latência do aplicativo. O aumento do uso da memória resulta em uma coleta de lixo frequente ou na paginação da memória no disco, e conseqüentemente aumenta a latência da interface do usuário. Para obter mais informações, consulte <xref:security/blazor/server>.

os aplicativos do Blazor Server devem ser otimizados para minimizar a latência da interface do usuário ao reduzir a latência de rede e o uso de memória Para obter uma abordagem para medir a latência de rede, consulte <xref:host-and-deploy/blazor/server#measure-network-latency>. Para obter mais informações sobre SignalR e Blazor, consulte:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Conexão com o servidor

os aplicativos do Blazor Server exigem uma conexão de SignalR ativa com o servidor. Se a conexão for perdida, o aplicativo tentará se reconectar ao servidor. Desde que o estado do cliente ainda esteja na memória, a sessão do cliente será retomada sem perder o estado.

#### <a name="reconnection-to-the-same-server"></a>Reconexão com o mesmo servidor

Um aplicativo do Blazor Server é renderizado em resposta à primeira solicitação do cliente, que configura o estado da interface do usuário no servidor. Quando o cliente tenta criar uma conexão SignalR, o cliente deve se reconectar ao mesmo servidor. os aplicativos do Blazor Server que usam mais de um servidor back-end devem implementar *sessões adesivas* para conexões de SignalR.

É recomendável usar o [serviço de SignalR do Azure](/azure/azure-signalr) para aplicativos do Blazor Server. O serviço permite escalar verticalmente um aplicativo do Blazor Server para um grande número de conexões de SignalR simultâneas. As sessões adesivas são habilitadas para o serviço de SignalR do Azure definindo a opção `ServerStickyMode` do serviço ou o valor de configuração como `Required`. Para obter mais informações, consulte <xref:host-and-deploy/blazor/server#signalr-configuration>.

Ao usar o IIS, as sessões adesivas são habilitadas com Application Request Routing. Para obter mais informações, consulte [balanceamento de carga http usando Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="reflect-the-connection-state-in-the-ui"></a>Refletir o estado da conexão na interface do usuário

Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar. Se a reconexão falhar, o usuário receberá a opção de tentar novamente.

Para personalizar a interface do usuário, defina um elemento com uma `id` de `components-reconnect-modal` na `<body>` da página do Razor *_Host. cshtml* :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

A tabela a seguir descreve as classes CSS aplicadas ao elemento `components-reconnect-modal`.

| Classe CSS                       | Indica&hellip; |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | Uma conexão perdida. O cliente está tentando se reconectar. Mostrar o modal. |
| `components-reconnect-hide`     | Uma conexão ativa é restabelecida com o servidor. Ocultar a janela restrita. |
| `components-reconnect-failed`   | Falha na reconexão, provavelmente devido a uma falha de rede. Para tentar a reconexão, chame `window.Blazor.reconnect()`. |
| `components-reconnect-rejected` | Reconexão rejeitada. O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido. Para recarregar o aplicativo, chame `location.reload()`. Esse estado de conexão pode resultar quando:<ul><li>Ocorre uma falha no circuito do lado do servidor.</li><li>O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário. As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</li><li>O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a>Reconexão com estado após o pré-processamento

os aplicativos do Blazor Server são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida. Isso é configurado na página Razor *_Host. cshtml* :

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
* É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo Blazor do agente do usuário.

| `RenderMode`        | Descrição |
| ------------------- | ----------- |
| `ServerPrerendered` | Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor. |
| `Server`            | Renderiza um marcador para um aplicativo do Blazor Server. A saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor. |
| `Static`            | Renderiza o componente em HTML estático. |

Não há suporte para a renderização de componentes de servidor de uma página HTML estática.

Quando `RenderMode` é `ServerPrerendered`, o componente é inicialmente renderizado estaticamente como parte da página. Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente*e o componente agora é interativo. Se o método de ciclo de vida de [{Async} OnInitialized](xref:blazor/lifecycle#component-initialization-methods) para inicializar o componente estiver presente, o método será executado *duas vezes*:

* Quando o componente é renderizado estaticamente.
* Depois que a conexão do servidor tiver sido estabelecida.

Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.

Para evitar o cenário de processamento duplo em um aplicativo do Blazor Server:

* Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.
* Use o identificador durante o pré-processamento para salvar o estado do componente.
* Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.

O código a seguir demonstra uma `WeatherForecastService` atualizada em um aplicativo do Blazor Server baseado em modelo que evita a renderização dupla:

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

### <a name="detect-when-the-app-is-prerendering"></a>Detectar quando o aplicativo está sendo renderizado

[!INCLUDE[](~/includes/blazor-prerendering.md)]

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

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/get-started>
* <xref:signalr/introduction>
