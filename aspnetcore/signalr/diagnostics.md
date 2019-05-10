---
title: Registro em log e diagnóstico no SignalR do ASP.NET Core
author: anurse
description: Saiba como coletar o diagnóstico do seu aplicativo SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 02/27/2019
uid: signalr/diagnostics
ms.openlocfilehash: b6bd21314ed183488999bcff3553e53493537a11
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896883"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>Registro em log e diagnóstico no SignalR do ASP.NET Core

Por [Andrew Stanton-Nurse](https://twitter.com/anurse)

Este artigo fornece orientações para reunir diagnósticos de seu aplicativo SignalR do ASP.NET Core para ajudar a solucionar problemas.

## <a name="server-side-logging"></a>Registro em log do lado do servidor

> [!WARNING]
> Logs do lado do servidor podem conter informações confidenciais de seu aplicativo. **Nunca** postar logs brutos de aplicativos de produção em fóruns públicos, como o GitHub.

Como o SignalR é parte do ASP.NET Core, ele usa o sistema de registro do ASP.NET Core. Na configuração padrão, SignalR registra informações muito pouco, mas isso pode configurado. Consulte a documentação sobre [registro do ASP.NET Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre como configurar o registro do ASP.NET Core.

O SignalR usa duas categorias de agente:

* `Microsoft.AspNetCore.SignalR` -para logs relacionados aos protocolos de Hub, ativando Hubs, invocar métodos e outras atividades relacionadas ao Hub.
* `Microsoft.AspNetCore.Http.Connections` -para os logs relacionados a todos os transportes, como WebSockets, sondagem longa e eventos do Server-sent e infraestrutura de baixo nível SignalR.

Para habilitar logs detalhados do SignalR, configure ambos os prefixos anteriores para o `Debug` nível em seu `appsettings.json` arquivo adicionando itens a seguir para o `LogLevel` na seção `Logging`:

[!code-json[Configuring logging](diagnostics/logging-config.json?highlight=7-8)]

Você também pode configurar isso no código em seu `CreateWebHostBuilder` método:

[!code-csharp[Configuring logging in code](diagnostics/logging-config-code.cs?highlight=5-6)]

Se você não estiver usando a configuração baseada em JSON, defina os seguintes valores de configuração no seu sistema de configuração:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Verifique a documentação para o seu sistema de configuração determinar como especificar valores de configuração aninhada. Por exemplo, ao usar variáveis de ambiente, duas `_` os caracteres são usados em vez do `:` (como: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).

É recomendável usar o `Debug` nível ao reunir mais detalhadas de diagnóstico para seu aplicativo. O `Trace` nível produz diagnósticos de nível muito baixo e raramente é necessária para diagnosticar problemas em seu aplicativo.

## <a name="access-server-side-logs"></a>Logs do lado do servidor de acesso

Como você pode acessar os logs do lado do servidor depende do ambiente no qual você está executando.

### <a name="as-a-console-app-outside-iis"></a>Como um aplicativo de console fora do IIS

Se você estiver executando em um aplicativo de console, o [agente de Console](xref:fundamentals/logging/index#console-provider) deve ser habilitado por padrão. Logs do SignalR serão exibidos no console.

### <a name="within-iis-express-from-visual-studio"></a>Dentro do IIS Express do Visual Studio

O Visual Studio exibe a saída de log na **saída** janela. Selecione o **servidor de Web do ASP.NET Core** lista suspensa da opção.

### <a name="azure-app-service"></a>Serviço de Aplicativo do Azure

Habilitar a opção de "Log de aplicativo (Filesystem)" na seção "Logs de diagnóstico" do portal do serviço de aplicativo do Azure e configurar o nível para `Verbose`. Os logs devem estar disponíveis no "Log" serviço de streaming, bem como em logs no sistema de arquivos do seu serviço de aplicativo. Para obter mais informações, consulte a documentação sobre [streaming de log do Azure](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Outros ambientes

Se você estiver executando em outro ambiente (Docker, Kubernetes, serviço do Windows, etc.), consulte a documentação completa sobre [log do ASP.NET Core](xref:fundamentals/logging/index) para obter mais informações sobre como configurar provedores de log adequados ao seu ambiente.

## <a name="javascript-client-logging"></a>Log de cliente JavaScript

> [!WARNING]
> Logs do lado do cliente podem conter informações confidenciais de seu aplicativo. **Nunca** postar logs brutos de aplicativos de produção em fóruns públicos, como o GitHub.

Ao usar o cliente JavaScript, você pode configurar opções de registro em log usando o `configureLogging` método no `HubConnectionBuilder`:

[!code-javascript[Configuring logging in the JavaScript client](diagnostics/logging-config-js.js?highlight=3)]

Para desabilitar o registro em log totalmente, especifique `signalR.LogLevel.None` no `configureLogging` método.

A tabela a seguir mostra os níveis de log disponíveis para o cliente JavaScript. Definindo o nível de log para um desses valores permite o log no nível e todos os níveis acima na tabela.

| Nível | Descrição |
| ----- | ----------- |
| `None` | Nenhuma mensagem será registrada. |
| `Critical` | Mensagens que indicam uma falha em todo o aplicativo. |
| `Error` | Mensagens que indicam uma falha na operação atual. |
| `Warning` | Mensagens que indicam um problema de não-fatais. |
| `Information` | Mensagens informativas. |
| `Debug` | Mensagens de diagnóstico útil para depuração. |
| `Trace` | Mensagens de diagnóstico muito detalhadas projetadas para diagnosticar problemas específicos. |

Depois de configurar o detalhamento, os logs serão gravados para o Console do navegador (ou a saída padrão em um aplicativo NodeJS).

Se você quiser enviar logs para um sistema de registro em log personalizado, você pode fornecer um objeto JavaScript que implementa o `ILogger` interface. O único método que precisa ser implementado é `log`, que usa o nível do evento e a mensagem associada ao evento. Por exemplo:

[!code-typescript[Creating a custom logger](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>Log de cliente .NET

> [!WARNING]
> Logs do lado do cliente podem conter informações confidenciais de seu aplicativo. **Nunca** postar logs brutos de aplicativos de produção em fóruns públicos, como o GitHub.

Para obter os logs do cliente .NET, você pode usar o `ConfigureLogging` método no `HubConnectionBuilder`. Isso funciona da mesma forma que o `ConfigureLogging` método no `WebHostBuilder` e `HostBuilder`. Você pode configurar os mesmos provedores de log que você usar no ASP.NET Core. No entanto, você precisa instalar e habilitar os pacotes do NuGet para os provedores de log individuais manualmente.

### <a name="console-logging"></a>Log de console

Para habilitar o log de Console, adicione a [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) pacote. Em seguida, use o `AddConsole` método para configurar o agente de console:

[!code-csharp[Configuring console logging in .NET client](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>O log de janela de saída de depuração

Você também pode configurar logs para ir para o **saída** janela no Visual Studio. Instalar o [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) de pacote e usar o `AddDebug` método:

[!code-csharp[Configuring debug output window logging in .NET client](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Outros provedores de log

O SignalR dá suporte a outros provedores de log como Serilog, Seq, NLog ou qualquer outro sistema de registro em log que se integra ao `Microsoft.Extensions.Logging`. Se o seu sistema de log fornece um `ILoggerProvider`, você pode registrá-lo com `AddProvider`:

[!code-csharp[Configuring a custom logging provider in .NET client](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Nível de detalhes de controle

Se você está fazendo logon de outros locais em seu aplicativo, alterando o nível padrão a `Debug` pode ser muito detalhado. Você pode usar um filtro para configurar o nível de log para logs do SignalR. Isso pode ser feito no código, em grande parte da mesma maneira que no servidor:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Rastreamentos de rede

> [!WARNING]
> Um rastreamento de rede contém todo o conteúdo de cada mensagem enviada pelo aplicativo. **Nunca** postar rastreamentos de rede brutos de aplicativos de produção em fóruns públicos, como o GitHub.

Se você encontrar um problema, um rastreamento de rede, às vezes, pode fornecer várias informações úteis. Isso é particularmente útil se você pretender arquivar um problema no nosso rastreador de problemas.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Coletar um rastreamento de rede com o Fiddler (opção preferencial)

Esse método funciona para todos os aplicativos.

O Fiddler é uma ferramenta muito poderosa para coletar rastreamentos HTTP. Instale-o em [telerik.com/fiddler](https://www.telerik.com/fiddler), iniciá-lo e, em seguida, execute o aplicativo e reproduza o problema. O Fiddler está disponível para Windows, e há versões beta para macOS e Linux.

Se você se conectar usando HTTPS, há algumas etapas adicionais para garantir que o Fiddler pode descriptografar o tráfego HTTPS. Para obter mais detalhes, consulte o [documentação do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Depois de coletar o rastreamento, você pode exportar o rastreamento, escolhendo **arquivo** > **salvar** > **todas as sessões...**  na barra de menus.

![Exportando todas as sessões do Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Coletar um rastreamento de rede com tcpdump (macOS e Linux somente)

Esse método funciona para todos os aplicativos.

Você pode coletar rastreamentos TCP brutos usando tcpdump, executando o seguinte comando em um shell de comando. Você talvez precise ser `root` ou prefixar o comando com `sudo` se você receber um erro de permissões:

```console
tcpdump -i [interface] -w trace.pcap
```

Substitua `[interface]` com a interface de rede que você deseja capturar no. Geralmente, isso é algo como `/dev/eth0` (para sua interface Ethernet padrão) ou `/dev/lo0` (para tráfego de localhost). Para obter mais informações, consulte o `tcpdump` página do manual no sistema de host.

## <a name="collect-a-network-trace-in-the-browser"></a>Coletar um rastreamento de rede no navegador

Esse método só funciona para aplicativos baseados em navegador.

A maioria das ferramentas de desenvolvedor do navegador tem uma guia de "Rede" que permite que você capture a atividade de rede entre o navegador e o servidor. No entanto, esses rastreamentos não incluem o WebSocket e Server-Sent mensagens de evento. Se você estiver usando esses transportes, usando uma ferramenta como o Fiddler ou TcpDump (descritos abaixo) é uma abordagem melhor.

### <a name="microsoft-edge-and-internet-explorer"></a>Internet Explorer e Microsoft Edge

(As instruções são as mesmas para o Microsoft Edge e Internet Explorer)

1. Pressione F12 para abrir as ferramentas de desenvolvimento
2. Clique na guia rede
3. Atualize a página (se necessário) e reproduzir o problema
4. Clique no ícone Salvar na barra de ferramentas para exportar o rastreamento como um arquivo de "HAR":

![O salvamento guia rede das ferramentas de ícone no desenvolvimento do Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Pressione F12 para abrir as ferramentas de desenvolvimento
2. Clique na guia rede
3. Atualize a página (se necessário) e reproduzir o problema
4. Clique com botão direito em qualquer lugar na lista de solicitações e escolha "Salvar como HAR com conteúdo":

![Opção "Salvar como HAR com conteúdo" no guia de rede de ferramentas de desenvolvimento do Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Pressione F12 para abrir as ferramentas de desenvolvimento
2. Clique na guia rede
3. Atualize a página (se necessário) e reproduzir o problema
4. Clique com botão direito em qualquer lugar na lista de solicitações e escolha "Salvar todos os como HAR"

![Opção "Salvar tudo como HAR" no guia de rede de ferramentas de desenvolvimento do Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Anexar arquivos de diagnóstico de problemas do GitHub

Você pode anexar arquivos de diagnóstico para problemas do GitHub ao renomeá-las para que eles tenham um `.txt` extensão arrastando e soltando-os para o problema.

> [!NOTE]
> Não cole o conteúdo de arquivos de log ou de rastreamentos de rede no problema do GitHub. Esses logs e rastreamentos podem ser muito grandes e GitHub geralmente truncará-los.

![Arrastar arquivos de log para um problema do GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
