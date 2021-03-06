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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core configuração de modelo de hospedagem mais incrivelmente

Por [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Este artigo aborda a configuração do modelo de hospedagem.

## <a name="blazor-webassembly"></a>WebAssembly Blazor

A partir da versão ASP.NET Core 3,2 Preview 3, o Webassembly mais claro dá suporte à configuração de:

* *wwwroot/appSettings. JSON*
* *wwwroot/appSettings. {ENVIRONMENT}. JSON*

Em um aplicativo hospedado mais, o [ambiente de tempo de execução](xref:fundamentals/environments) é o mesmo que o valor do aplicativo do servidor.

Ao executar o aplicativo localmente, o ambiente assume como padrão o desenvolvimento. Quando o aplicativo é publicado, o ambiente assume como padrão a produção. Para obter mais informações, incluindo como configurar o ambiente, consulte <xref:fundamentals/environments>.

> [!WARNING]
> A configuração em um aplicativo Webassembly mais incrivelmente é visível para os usuários. **Não armazene os segredos ou as credenciais do aplicativo na configuração.**

Os arquivos de configuração são armazenados em cache para uso offline. Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação. A edição de arquivos de configuração entre implantações não tem efeito porque:

* Os usuários têm versões em cache dos arquivos que eles continuam a usar.
* Os arquivos *Service-Worker. js* e *Service-Worker-assets. js* do PWA devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.

Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates>.

## <a name="blazor-server"></a>Servidor Blazor

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
