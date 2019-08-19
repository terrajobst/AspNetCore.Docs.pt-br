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
# <a name="aspnet-core-blazor-hosting-models"></a>Modelos de hospedagem mais amASP.NET Core

Por [Daniel Roth](https://github.com/danroth27)

O mais elaborador é uma estrutura da Web criada para executar o lado do cliente no navegador em um tempo de execução .net baseado em [Webassembly](https://webassembly.org/) (*mais de um lado do cliente*) ou no lado do servidor no ASP.NET Core (*mais alto servidor*). Independentemente do modelo de hospedagem, os modelos de aplicativo e componente *são os mesmos*.

Para criar um projeto para os modelos de hospedagem descritos neste artigo, consulte <xref:blazor/get-started>.

## <a name="client-side"></a>Lado do cliente

O modelo de hospedagem principal para o mais alto é executar o lado do cliente no navegador no Webassembly. O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador. O aplicativo é executado diretamente no thread da interface do usuário do navegador. As atualizações da interface do usuário e o tratamento de eventos ocorrem no mesmo processo. Os ativos do aplicativo são implantados como arquivos estáticos em um servidor Web ou serviço capaz de fornecer conteúdo estático aos clientes.

![No lado do cliente: O aplicativo mais incrivelmente é executado em um thread de interface do usuário dentro do navegador.](hosting-models/_static/client-side.png)

Para criar um aplicativo mais novo usando o modelo de hospedagem do lado do cliente, use o modelo de **aplicativo Webassembly** mais[novo (dotNet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Depois de selecionar o modelo de **aplicativo Webassembly** mais novo, você tem a opção de configurar o aplicativo para usar um ASP.NET Core back-end marcando a caixa de seleção **ASP.NET Core hospedado** ([dotnet New blazorwasm – hospedado](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core serve para o aplicativo mais incrivelmente para os clientes. O aplicativo do lado do cliente mais novo pode interagir com o servidor pela rede usando chamadas de API Web ou [signalr](xref:signalr/introduction).

Os modelos incluem o script mais *incrivelmente. Webassembly. js* que manipula:

* Baixar o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
* Inicialização do tempo de execução para executar o aplicativo.

O modelo de hospedagem do lado do cliente oferece vários benefícios:

* Não há nenhuma dependência do lado do servidor .NET. O aplicativo está totalmente funcionando depois de baixado para o cliente.
* Recursos e funcionalidades do cliente são totalmente aproveitados.
* O trabalho é descarregado do servidor para o cliente.
* Um servidor Web ASP.NET Core não é necessário para hospedar o aplicativo. Cenários de implantação sem servidor são possíveis (por exemplo, servindo o aplicativo de uma CDN).

Há desvantagens na hospedagem do lado do cliente:

* O aplicativo é restrito aos recursos do navegador.
* Hardware e software de cliente com capacidade (por exemplo, suporte a Webassembly) é necessário.
* O tamanho do download é maior e os aplicativos demoram mais para serem carregados.
* O suporte ao tempo de execução e às ferramentas do .NET é menos maduro. Por exemplo, existem limitações em [.net Standard](/dotnet/standard/net-standard) suporte e depuração.

## <a name="server-side"></a>Lado do servidor

Com o modelo de hospedagem do lado do servidor, o aplicativo é executado no servidor de dentro de um aplicativo ASP.NET Core. As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).

![O navegador interage com o aplicativo (hospedado dentro de um aplicativo ASP.NET Core) no servidor por meio de uma conexão de sinalização.](hosting-models/_static/server-side.png)

Para criar um aplicativo mais novo usando o modelo de hospedagem do lado do servidor, use o modelo de **aplicativo de servidor** ASP.NET Core mais alto ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core hospeda o aplicativo do lado do servidor e cria o ponto de extremidade do Signalr onde os clientes se conectam.

O aplicativo ASP.NET Core referencia a classe do `Startup` aplicativo a ser adicionada:

* Serviços do lado do servidor.
* O aplicativo para o pipeline de tratamento de solicitação.

O script&dagger; mais *incrivelmente. Server. js* estabelece a conexão do cliente. É responsabilidade do aplicativo persistir e restaurar o estado do aplicativo, conforme necessário (por exemplo, no caso de uma conexão de rede perdida).

O modelo de hospedagem do lado do servidor oferece vários benefícios:

* O tamanho do download é significativamente menor do que um aplicativo do lado do cliente e o aplicativo é carregado muito mais rapidamente.
* O aplicativo aproveita totalmente os recursos do servidor, incluindo o uso de qualquer API compatível com o .NET Core.
* O .NET Core no servidor é usado para executar o aplicativo, portanto, as ferramentas .NET existentes, como depuração, funcionam conforme o esperado.
* Há suporte para clientes finos. Por exemplo, aplicativos do lado do servidor funcionam com navegadores que não dão suporte ao Webassembly e a dispositivos com restrição de recursos.
* A base .NET/C# código do aplicativo, incluindo o código de componente do aplicativo, não é servida aos clientes.

Há desvantagens na hospedagem do lado do servidor:

* A latência mais alta geralmente existe. Cada interação do usuário envolve um salto de rede.
* Não há suporte offline. Se a conexão do cliente falhar, o aplicativo para de funcionar.
* A escalabilidade é desafiadora para aplicativos com muitos usuários. O servidor deve gerenciar várias conexões de cliente e manipular o estado do cliente.
* Um servidor de ASP.NET Core é necessário para atender ao aplicativo. Cenários de implantação sem servidor não são possíveis (por exemplo, servir o aplicativo de uma CDN).

&dagger;O script mais *incrivelmente. Server. js* é servido por meio de um recurso incorporado na estrutura compartilhada ASP.NET Core.

### <a name="reconnection-to-the-same-server"></a>Reconexão com o mesmo servidor

Os aplicativos mais no lado do servidor exigem uma conexão ativa do signalr com o servidor. Se a conexão for perdida, o aplicativo tentará se reconectar ao servidor. Desde que o estado do cliente ainda esteja na memória, a sessão do cliente será retomada sem perder o estado.
 
Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar. Se a reconexão falhar, o usuário receberá a opção de tentar novamente. Para personalizar a interface do usuário, defina um `components-reconnect-modal` elemento com `id` como na página Razor *_Host. cshtml* . O cliente atualiza esse elemento com uma das seguintes classes CSS com base no estado da conexão:
 
* `components-reconnect-show`&ndash; Mostre a interface do usuário para indicar que a conexão foi perdida e que o cliente está tentando se reconectar.
* `components-reconnect-hide`&ndash; O cliente tem uma conexão ativa, oculta a interface do usuário.
* `components-reconnect-failed`&ndash; Falha na reconexão. Para tentar reconectar novamente, chame `window.Blazor.reconnect()`.

### <a name="stateful-reconnection-after-prerendering"></a>Reconexão com estado após o pré-processamento
 
Aplicativos de mais alto do lado do servidor são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida. Isso é configurado na página Razor *_Host. cshtml* :
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
O cliente se reconecta ao servidor com o mesmo estado que foi usado para PreRender o aplicativo. Se o estado do aplicativo ainda estiver na memória, o estado do componente não será reprocessado depois que a conexão do Signalr for estabelecida.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Renderizar componentes interativos com estado de páginas e exibições do Razor
 
Os componentes interativos com estado podem ser adicionados a uma página ou exibição Razor. Quando a página ou a exibição é renderizada, o componente é renderizado com ele. O aplicativo se reconecta ao estado do componente depois que a conexão do cliente é estabelecida, desde que o estado ainda esteja na memória.
 
Por exemplo, a seguinte página Razor renderiza um `Counter` componente com uma contagem inicial que é especificada usando um formulário:
 
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

### <a name="detect-when-the-app-is-prerendering"></a>Detectar quando o aplicativo está sendo renderizado
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a>Configurar o cliente Signalr para aplicativos do lado do servidor mais incrivelmente
 
Às vezes, você precisa configurar o cliente Signalr usado pelos aplicativos mais usados no servidor. Por exemplo, talvez você queira configurar o registro em log no cliente do Signalr para diagnosticar um problema de conexão.
 
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
