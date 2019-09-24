---
title: Modelos de hospedagem mais amASP.NET Core
author: guardrex
description: Entenda os modelos de Hospedagem de Webassembly e de mais incrivelmente do servidor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/hosting-models
ms.openlocfilehash: 766b52df82f75ea1223e20d8471faa5732311f91
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207234"
---
# <a name="aspnet-core-blazor-hosting-models"></a>Modelos de hospedagem mais amASP.NET Core

Por [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

É uma estrutura da Web criada para executar o lado do cliente no navegador em um tempo de execução .NET baseado em [Webassembly](https://webassembly.org/)(mais alto*Webassembly*) ou no lado do servidor no ASP.NET Core (*servidor*de mais Altova). Independentemente do modelo de hospedagem, os modelos de aplicativo e componente *são os mesmos*.

Para criar um projeto para os modelos de hospedagem descritos neste artigo, consulte <xref:blazor/get-started>.

## <a name="blazor-webassembly"></a>WebAssembly Blazor

O modelo de hospedagem principal para o mais alto é executar o lado do cliente no navegador no Webassembly. O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador. O aplicativo é executado diretamente no thread da interface do usuário do navegador. As atualizações da interface do usuário e o tratamento de eventos ocorrem no mesmo processo. Os ativos do aplicativo são implantados como arquivos estáticos em um servidor Web ou serviço capaz de fornecer conteúdo estático aos clientes.

![Webassembly mais incrivelmente: O aplicativo mais incrivelmente é executado em um thread de interface do usuário dentro do navegador.](hosting-models/_static/blazor-webassembly.png)

Para criar um aplicativo mais novo usando o modelo de hospedagem do lado do cliente, use o modelo de **aplicativo Webassembly** mais[novo (dotNet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Depois de selecionar o modelo de **aplicativo Webassembly** mais novo, você tem a opção de configurar o aplicativo para usar um ASP.NET Core back-end marcando a caixa de seleção **ASP.NET Core hospedado** ([dotnet New blazorwasm – hospedado](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core serve para o aplicativo mais incrivelmente para os clientes. O aplicativo Webassembly mais novo pode interagir com o servidor pela rede usando chamadas de API Web ou [signalr](xref:signalr/introduction).

Os modelos incluem o script mais *incrivelmente. Webassembly. js* que manipula:

* Baixar o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
* Inicialização do tempo de execução para executar o aplicativo.

O modelo de hospedagem Webassembly mais incrivelmente oferece vários benefícios:

* Não há nenhuma dependência do lado do servidor .NET. O aplicativo está totalmente funcionando depois de baixado para o cliente.
* Recursos e funcionalidades do cliente são totalmente aproveitados.
* O trabalho é descarregado do servidor para o cliente.
* Um servidor Web ASP.NET Core não é necessário para hospedar o aplicativo. Cenários de implantação sem servidor são possíveis (por exemplo, servindo o aplicativo de uma CDN).

Há desvantagens na Hospedagem de Webassembly mais incrivelmente:

* O aplicativo é restrito aos recursos do navegador.
* Hardware e software de cliente com capacidade (por exemplo, suporte a Webassembly) é necessário.
* O tamanho do download é maior e os aplicativos demoram mais para serem carregados.
* O suporte ao tempo de execução e às ferramentas do .NET é menos maduro. Por exemplo, existem limitações em [.net Standard](/dotnet/standard/net-standard) suporte e depuração.

## <a name="blazor-server"></a>Servidor Blazor

Com o modelo de Hospedagem de servidor mais incrivelmente, o aplicativo é executado no servidor de dentro de um aplicativo ASP.NET Core. As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).

![O navegador interage com o aplicativo (hospedado dentro de um aplicativo ASP.NET Core) no servidor por meio de uma conexão de sinalização.](hosting-models/_static/blazor-server.png)

Para criar um aplicativo mais novo usando o modelo de Hospedagem de servidor mais novo, use o modelo de **aplicativo de servidor** ASP.NET Core mais incrivelmente ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core hospeda o aplicativo de servidor mais incrivelmente e cria o ponto de extremidade do Signalr onde os clientes se conectam.

O aplicativo ASP.NET Core referencia a classe do `Startup` aplicativo a ser adicionada:

* Serviços do lado do servidor.
* O aplicativo para o pipeline de tratamento de solicitação.

O script&dagger; mais *incrivelmente. Server. js* estabelece a conexão do cliente. É responsabilidade do aplicativo persistir e restaurar o estado do aplicativo, conforme necessário (por exemplo, no caso de uma conexão de rede perdida).

O modelo de Hospedagem de servidor mais incrivelmente oferece vários benefícios:

* O tamanho do download é significativamente menor do que um aplicativo Webassembly mais grande e o aplicativo é carregado com muito mais rapidez.
* O aplicativo aproveita totalmente os recursos do servidor, incluindo o uso de qualquer API compatível com o .NET Core.
* O .NET Core no servidor é usado para executar o aplicativo, portanto, as ferramentas .NET existentes, como depuração, funcionam conforme o esperado.
* Há suporte para clientes finos. Por exemplo, os aplicativos de servidor mais úteis funcionam com navegadores que não dão suporte ao Webassembly e a dispositivos com restrição de recursos.
* A base .NET/C# código do aplicativo, incluindo o código de componente do aplicativo, não é servida aos clientes.

Há desvantagens na Hospedagem de servidores mais incrivelmente:

* A latência mais alta geralmente existe. Cada interação do usuário envolve um salto de rede.
* Não há suporte offline. Se a conexão do cliente falhar, o aplicativo para de funcionar.
* A escalabilidade é desafiadora para aplicativos com muitos usuários. O servidor deve gerenciar várias conexões de cliente e manipular o estado do cliente.
* Um servidor de ASP.NET Core é necessário para atender ao aplicativo. Cenários de implantação sem servidor não são possíveis (por exemplo, servir o aplicativo de uma CDN).

&dagger;O script mais *incrivelmente. Server. js* é servido por meio de um recurso incorporado na estrutura compartilhada ASP.NET Core.

### <a name="comparison-to-server-rendered-ui"></a>Comparação com a interface do usuário renderizada pelo servidor

Uma maneira de entender os aplicativos de servidor mais incrivelmente é entender como ele difere dos modelos tradicionais de renderização da interface do usuário em aplicativos ASP.NET Core usando exibições do Razor ou Razor Pages. Ambos os modelos usam a linguagem Razor para descrever o conteúdo HTML, mas são significativamente diferentes na forma como a marcação é renderizada.

Quando uma página Razor ou exibição é renderizada, cada linha do código do Razor emite HTML na forma de texto. Após a renderização, o servidor descarta a página ou a instância de exibição, incluindo qualquer estado produzido. Quando outra solicitação para a página ocorre, por exemplo, quando a validação do servidor falha e o resumo de validação é exibido:

* A página inteira é reprocessada para o texto HTML novamente.
* A página é enviada ao cliente.

Um aplicativo mais fácil é composto de elementos reutilizáveis da interface do usuário chamada de *componentes*. Um componente contém C# código, marcação e outros componentes. Quando um componente é renderizado, um mais claro produz um gráfico dos componentes incluídos de forma semelhante a um XML ou Modelo de Objeto do Documento (DOM). Esse grafo inclui o estado do componente mantido em Propriedades e campos. O mais claro avalia o grafo de componentes para produzir uma representação binária da marcação. O formato binário pode ser:

* Transformado em texto HTML (durante o pré-processamento).
* Usado para atualizar a marcação com eficiência durante a renderização regular.

Uma atualização de interface do usuário no mais incrivelmente é disparada por:

* Interação do usuário, como selecionar um botão.
* Gatilhos de aplicativo, como um temporizador.

O grafo é rerenderizado e uma *comparação* de interface do usuário (diferença) é calculada. Essa diferença é o menor conjunto de edições DOM necessárias para atualizar a interface do usuário no cliente. A comparação é enviada ao cliente em um formato binário e aplicada pelo navegador.

Um componente é descartado depois que o usuário navega para fora dele no cliente. Embora um usuário esteja interagindo com um componente, o estado do componente (serviços, recursos) deve ser mantido na memória do servidor. Como o estado de muitos componentes pode ser mantido pelo servidor simultaneamente, o esgotamento de memória é uma preocupação que deve ser resolvida. Para obter orientação sobre como criar um aplicativo de servidor mais incrivelmente para garantir o melhor uso da memória do servidor <xref:security/blazor/server>, consulte.

### <a name="circuits"></a>Circuitos

Um aplicativo de servidor mais elaborado é criado com base no [signalr ASP.NET Core](xref:signalr/introduction). Cada cliente se comunica com o servidor por meio de uma ou mais conexões de Signalr chamadas de *circuito*. Um circuito é uma abstração de mais de uma vez sobre conexões de sinalização que podem tolerar interrupções de rede temporárias. Quando um cliente mais novo perceber que a conexão do Signalr está desconectada, ele tentará se reconectar ao servidor usando uma nova conexão com o Signalr.

Cada tela do navegador (guia do navegador ou iframe) que está conectada a um aplicativo de servidor mais incrivelmente usa uma conexão de Signalr. Essa é ainda outra distinção importante em comparação com os aplicativos típicos renderizados pelo servidor. Em um aplicativo renderizado pelo servidor, a abertura do mesmo aplicativo em várias telas do navegador geralmente não se traduz em demandas de recursos adicionais no servidor. Em um aplicativo de servidor mais incrivelmente, cada tela do navegador requer um circuito separado e instâncias separadas do estado do componente a serem gerenciadas pelo servidor.

O mais incrivelmente considera fechar uma guia do navegador ou navegar até uma URL externa um encerramento *normal* . No caso de um encerramento normal, o circuito e os recursos associados são lançados imediatamente. Um cliente também pode se desconectar não normalmente, por exemplo, devido a uma interrupção de rede. O servidor mais incrivelmente armazena circuitos desconectados para um intervalo configurável para permitir que o cliente se reconecte. Para obter mais informações, consulte a seção [reconexão com o mesmo servidor](#reconnection-to-the-same-server) .

### <a name="ui-latency"></a>Latência da interface do usuário

A latência da interface do usuário é o tempo que leva de uma ação iniciada até a hora em que a interface do usuário é atualizada. Valores menores para latência de interface do usuário são imperativos para um aplicativo se sentir responsivo a um usuário. Em um aplicativo de servidor mais novo, cada ação é enviada para o servidor, processada e uma comparação da interface do usuário é enviada de volta. Consequentemente, a latência da interface do usuário é a soma da latência da rede e a latência do servidor no processamento da ação.

Para um aplicativo de linha de negócios limitado a uma rede corporativa privada, o efeito sobre as percepções do usuário de latência devido à latência da rede geralmente é imperceptível. Para um aplicativo implantado pela Internet, a latência pode ser perceptível para os usuários, especialmente se os usuários forem amplamente distribuídos geograficamente.

O uso de memória também pode contribuir para a latência do aplicativo. O aumento do uso da memória resulta em uma coleta de lixo frequente ou na paginação da memória no disco, e conseqüentemente aumenta a latência da interface do usuário. Para obter mais informações, consulte <xref:security/blazor/server>.

Aplicativos de servidor mais incrivelmente devem ser otimizados para minimizar a latência da interface do usuário, reduzindo a latência de rede e o uso de memória. Para obter uma abordagem para medir a latência de <xref:host-and-deploy/blazor/server#measure-network-latency>rede, consulte. Para obter mais informações sobre o Signalr e o mais incrivelmente, consulte:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="reconnection-to-the-same-server"></a>Reconexão com o mesmo servidor

Os aplicativos de servidor mais poseriais exigem uma conexão ativa de sinalização com o servidor. Se a conexão for perdida, o aplicativo tentará se reconectar ao servidor. Desde que o estado do cliente ainda esteja na memória, a sessão do cliente será retomada sem perder o estado.

Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar. Se a reconexão falhar, o usuário receberá a opção de tentar novamente. Para personalizar a interface do usuário, defina um `components-reconnect-modal` elemento com `id` como na página Razor *_Host. cshtml* . O cliente atualiza esse elemento com uma das seguintes classes CSS com base no estado da conexão:

* `components-reconnect-show`&ndash; Mostre a interface do usuário para indicar que a conexão foi perdida e que o cliente está tentando se reconectar.
* `components-reconnect-hide`&ndash; O cliente tem uma conexão ativa, oculta a interface do usuário.
* `components-reconnect-failed`&ndash; Falha na reconexão. Para tentar reconectar novamente, chame `window.Blazor.reconnect()`.

### <a name="stateful-reconnection-after-prerendering"></a>Reconexão com estado após o pré-processamento

Os aplicativos de servidor mais poseriais são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida. Isso é configurado na página Razor *_Host. cshtml* :

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`configura se o componente:

* É renderizado na página.
* É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.

| `RenderMode`        | Descrição |
| ------------------- | ----------- |
| `ServerPrerendered` | Renderiza o componente em HTML estático e inclui um marcador para um aplicativo de servidor mais incrivelmente. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo mais incrivelmente. Não há suporte para parâmetros. |
| `Server`            | Renderiza um marcador para um aplicativo de servidor mais incrivelmente. A saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo mais incrivelmente. Não há suporte para parâmetros. |
| `Static`            | Renderiza o componente em HTML estático. Há suporte para os parâmetros. |

Não há suporte para a renderização de componentes de servidor de uma página HTML estática.

O cliente se reconecta ao servidor com o mesmo estado que foi usado para PreRender o aplicativo. Se o estado do aplicativo ainda estiver na memória, o estado do componente não será reprocessado depois que a conexão do Signalr for estabelecida.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Renderizar componentes interativos com estado de páginas e exibições do Razor

Os componentes interativos com estado podem ser adicionados a uma página ou exibição Razor.

Quando a página ou a exibição renderiza:

* O componente é renderizado com a página ou exibição.
* O estado inicial do componente usado para o pré-processamento é perdido.
* O novo estado do componente é criado quando a conexão do Signalr é estabelecida.

A seguinte página do Razor renderiza um `Counter` componente:

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Renderizar componentes não interativos de páginas e exibições do Razor

Na página Razor a seguir, o `MyComponent` componente é processado estaticamente com um valor inicial que é especificado usando um formulário:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Como `MyComponent` é processado estaticamente, o componente não pode ser interativo.

### <a name="detect-when-the-app-is-prerendering"></a>Detectar quando o aplicativo está sendo renderizado

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Configurar o cliente Signalr para aplicativos de servidor mais incrivelmente

Às vezes, você precisa configurar o cliente Signalr usado por aplicativos de servidor mais incrivelmente. Por exemplo, talvez você queira configurar o registro em log no cliente do Signalr para diagnosticar um problema de conexão.

Para configurar o cliente Signalr no arquivo *pages/_Host. cshtml* :

* Adicione um `autostart="false"` atributo `<script>` à marca para o script mais *incrivelmente. Server. js* .
* Chame `Blazor.start` e passe um objeto de configuração que especifica o construtor de signalr.

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

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/get-started>
* <xref:signalr/introduction>
