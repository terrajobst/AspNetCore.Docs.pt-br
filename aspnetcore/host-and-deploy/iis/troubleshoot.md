---
title: Solucionar problemas do ASP.NET Core no IIS
author: guardrex
description: Saiba como diagnosticar problemas com as implantações do IIS (Serviços de Informações da Internet) de aplicativos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 68fcd578c051ae9ba6234cad0465a7ef42f1ed14
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637684"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a>Solucionar problemas do ASP.NET Core no IIS

Por [Luke Latham](https://github.com/guardrex)

Este artigo fornece instruções sobre como diagnosticar um problema de inicialização do aplicativo ASP.NET Core ao hospedar com [IIS (Serviços de Informações da Internet)](/iis). As informações neste artigo se aplicam à hospedagem em IIS no Windows Server e Windows Desktop.

::: moniker range=">= aspnetcore-2.2"

No Visual Studio, um projeto do ASP.NET Core usa por padrão a hospedagem do [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante a depuração. Uma *502.5 – falha de processo* ou uma *500.30 – falha de inicialização* que ocorre ao depurar localmente pode ser solucionada usando as recomendações presentes neste tópico.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

No Visual Studio, um projeto do ASP.NET Core usa por padrão a hospedagem do [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante a depuração. Uma *502.5 – Falha de Processo* que ocorre ao depurar localmente pode ser solucionada usando as recomendações presentes neste tópico.

::: moniker-end

Tópicos adicionais de solução de problemas:

<xref:host-and-deploy/azure-apps/troubleshoot>  
Embora o Serviço de Aplicativo use o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) e o IIS para hospedar aplicativos, veja o tópico dedicado para obter instruções específicas para o Serviço de Aplicativo.

<xref:fundamentals/error-handling>  
Descubra como tratar erros em aplicativos do ASP.NET Core durante o desenvolvimento em um sistema local.

[Aprenda a depurar usando o Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)  
Este tópico apresenta os recursos do depurador do Visual Studio.

[Depurar com o Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)  
Saiba mais sobre o suporte de depuração interno do Visual Studio Code.

## <a name="app-startup-errors"></a>Erros de inicialização do aplicativo

### <a name="5025-process-failure"></a>502.5 Falha de processo

O processo de trabalho falha. O aplicativo não foi iniciado.

O Módulo do ASP.NET Core tenta iniciar o processo dotnet de back-end, mas falha ao iniciar. A causa de uma falha de inicialização do processo geralmente pode ser determinada com base em entradas no [Log de Eventos do Aplicativo](#application-event-log) e no [log de stdout do Módulo do ASP.NET Core](#aspnet-core-module-stdout-log). 

Uma condição de falha comum é o aplicativo configurado incorretamente, direcionado a uma versão da estrutura compartilhada do ASP.NET Core que não está presente. Verifique quais versões da estrutura compartilhada do ASP.NET Core estão instaladas no computador de destino.

A página do erro *502.5 – Falha no Processo* é retornada quando um erro de configuração de hospedagem ou do aplicativo faz com que o processo de trabalho falhe:

![Janela do navegador mostrando a página 502.5 – Falha no Processo](troubleshoot/_static/process-failure-page.png)

::: moniker range=">= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a>500.30 Falha de inicialização em processo

O processo de trabalho falha. O aplicativo não foi iniciado.

O Módulo do ASP.NET Core tenta iniciar o CLR do .NET Core em processo, mas falha ao iniciar. A causa de uma falha de inicialização do processo geralmente pode ser determinada com base em entradas no [Log de Eventos do Aplicativo](#application-event-log) e no [log de stdout do Módulo do ASP.NET Core](#aspnet-core-module-stdout-log). 

Uma condição de falha comum é o aplicativo configurado incorretamente, direcionado a uma versão da estrutura compartilhada do ASP.NET Core que não está presente. Verifique quais versões da estrutura compartilhada do ASP.NET Core estão instaladas no computador de destino.

### <a name="5000-in-process-handler-load-failure"></a>500.0 Falha de carregamento de manipulador em processo

O processo de trabalho falha. O aplicativo não foi iniciado.

O Módulo do ASP.NET Core falha ao encontrar o CLR do .NET Core e o manipulador de solicitação em processo (*aspnetcorev2_inprocess.dll*). Verifique se:

* O aplicativo destina-se ao pacote NuGet [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) ou ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).
* A versão da estrutura compartilhada do ASP.NET Core a que o aplicativo se destina está instalada no computador de destino.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 Falha de carregamento de manipulador fora de processo

O processo de trabalho falha. O aplicativo não foi iniciado.

O Módulo do ASP.NET Core falha ao encontrar o manipulador de solicitações de hospedagem de fora do processo. Verifique se a *aspnetcorev2_outofprocess.dll* está presente em uma subpasta próxima a *aspnetcorev2.dll*. 

::: moniker-end

### <a name="500-internal-server-error"></a>500 Erro Interno do Servidor

O aplicativo é iniciado, mas um erro impede o servidor de atender à solicitação.

Esse erro ocorre no código do aplicativo durante a inicialização ou durante a criação de uma resposta. A resposta poderá não conter nenhum conteúdo, ou a resposta poderá ser exibida como um *500 – Erro Interno do Servidor* no navegador. O Log de Eventos do Aplicativo geralmente indica que o aplicativo iniciou normalmente. Da perspectiva do servidor, isso está correto. O aplicativo foi iniciado, mas não é capaz de gerar uma resposta válida. [Execute o aplicativo em um prompt de comando](#run-the-app-at-a-command-prompt) no servidor ou [habilite o log de stdout do Módulo do ASP.NET Core](#aspnet-core-module-stdout-log) para solucionar o problema.

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Falha ao iniciar o aplicativo (ErrorCode '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

O aplicativo falhou ao ser iniciado porque o assembly do aplicativo (*.dll*) não pôde ser carregado.

Esse erro ocorre quando há uma incompatibilidade de número de bits entre o aplicativo publicado e o processo w3wp/iisexpress.

Confirme se a configuração de 32 bits do pool de aplicativos está correta:

1. Selecione o pool de aplicativos no **Pools de Aplicativos** do Gerenciador do IIS.
1. Selecione **Configurações Avançadas** em **Editar Pool de Aplicativos** no painel **Ações**.
1. Defina **Habilitar Aplicativos de 32 bits**:
   * Se estiver implantando um aplicativo de 32 bits (x86), defina o valor como `True`.
   * Se estiver implantando um aplicativo de 64 bits (x64), defina o valor como `False`.

### <a name="connection-reset"></a>Redefinição de conexão

Se um erro ocorrer após os cabeçalhos serem enviados, será tarde demais para o servidor enviar um **500 – Erro Interno do Servidor** no caso de um erro ocorrer. Isso geralmente acontece quando ocorre um erro durante a serialização de objetos complexos para uma resposta. Esse tipo de erro é exibida como um erro de *redefinição de conexão* no cliente. O [Log de aplicativo](xref:fundamentals/logging/index) pode ajudar a solucionar esses tipos de erros.

## <a name="default-startup-limits"></a>Limites de inicialização padrão

O Módulo do ASP.NET Core está configurado com um *startupTimeLimit* padrão de 120 segundos. Quando deixado no valor padrão, um aplicativo pode levar até dois minutos para iniciar antes que uma falha do processo seja registrada em log pelo módulo. Para obter informações sobre como configurar o módulo, veja [Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-app-startup-errors"></a>Solucionar problemas de inicialização do aplicativo

### <a name="enable-the-aspnet-core-module-debug-log"></a>Habilitar o log de depuração do Módulo do ASP.NET Core

Adicione as seguintes configurações de manipulador ao arquivo *web.config* do aplicativo para habilitar os logs de depuração do Módulo do ASP.NET Core:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Confirme se o caminho especificado para o log existe e se a identidade do pool de aplicativos tem permissões de gravação no local.

### <a name="application-event-log"></a>Log de Eventos do Aplicativo

Acesse o Log de Eventos do Aplicativo:

1. Abra o menu Iniciar, procure **Visualizador de Eventos** e, em seguida, selecione o aplicativo **Visualizador de Eventos**.
1. No **Visualizador de Eventos**, abra o nó **Logs do Windows**.
1. Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.
1. Procure erros associados ao aplicativo com falha. Os erros têm um valor *Módulo AspNetCore do IIS* ou *Módulo AspNetCore do IIS Express* na coluna *Origem*.

### <a name="run-the-app-at-a-command-prompt"></a>Execute o aplicativo em um prompt de comando

Muitos erros de inicialização não produzem informações úteis no Log de Eventos do Aplicativo. Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.

#### <a name="framework-dependent-deployment"></a>Implantação dependente de estrutura

Se o aplicativo é uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. Em um prompt de comando, navegue até a pasta de implantação e execute o aplicativo, executando o assembly do aplicativo com *dotnet.exe*. No comando a seguir, substitua o nome do assembly do aplicativo por \<assembly_name>: `dotnet .\<assembly_name>.dll`.
1. A saída do console do aplicativo, mostrando eventuais erros, é gravada na janela do console.
1. Se os erros ocorrerem ao fazer uma solicitação para o aplicativo, faça uma solicitação para o host e a porta em que o Kestrel escuta. Usando o host e a porta padrão, faça uma solicitação para `http://localhost:5000/`. Se o aplicativo responde normalmente no endereço do ponto de extremidade do Kestrel, a probabilidade de o problema estar relacionado à configuração de hospedagem é maior e, de estar relacionado ao aplicativo, menor.

#### <a name="self-contained-deployment"></a>Implantação autocontida

Se o aplicativo é uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd):

1. Em um prompt de comando, navegue até a pasta de implantação e execute o arquivo executável do aplicativo. No comando a seguir, substitua o nome do assembly do aplicativo por \<assembly_name>: `<assembly_name>.exe`.
1. A saída do console do aplicativo, mostrando eventuais erros, é gravada na janela do console.
1. Se os erros ocorrerem ao fazer uma solicitação para o aplicativo, faça uma solicitação para o host e a porta em que o Kestrel escuta. Usando o host e a porta padrão, faça uma solicitação para `http://localhost:5000/`. Se o aplicativo responde normalmente no endereço do ponto de extremidade do Kestrel, a probabilidade de o problema estar relacionado à configuração de hospedagem é maior e, de estar relacionado ao aplicativo, menor.

### <a name="aspnet-core-module-stdout-log"></a>Log de stdout do Módulo do ASP.NET Core

Para habilitar e exibir logs de stdout:

1. Navegue até a pasta de implantação do site no sistema de hospedagem.
1. Se a pasta *logs* não estiver presente, crie-a. Para obter instruções sobre como habilitar o MSBuild para criar a pasta *logs* na implantação automaticamente, veja o tópico [Estrutura de diretórios](xref:host-and-deploy/directory-structure).
1. Edite o arquivo *web.config*. Defina **stdoutLogEnabled** para `true` e altere o caminho **stdoutLogFile** para apontar para a pasta *logs* (por exemplo, `.\logs\stdout`). `stdout` no caminho é o prefixo do nome do arquivo de log. Uma extensão de arquivo, uma ID do processo e um carimbo de data/hora são adicionados automaticamente quando o log é criado. Usando `stdout` como o prefixo do nome do arquivo, um arquivo de log típico é nomeado *stdout_20180205184032_5412.log*. 
1. Verifique se a identidade do pool de aplicativos tem permissões de gravação para a pasta *logs*.
1. Salve o arquivo *web.config* atualizado.
1. Faça uma solicitação ao aplicativo.
1. Navegue até a pasta *logs*. Localize e abra o log de stdout mais recente.
1. Estude o log em busca de erros.

> [!IMPORTANT]
> Desabilite o registro em log de stdout quando a solução de problemas for concluída.

1. Edite o arquivo *web.config*.
1. Defina **stdoutLogEnabled** para `false`.
1. Salve o arquivo.

> [!WARNING]
> Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor. Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados.
>
> Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs. Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="enable-the-developer-exception-page"></a>Habilitar a página de exceção do desenvolvedor

A [variável de ambiente `ASPNETCORE_ENVIRONMENT` pode ser adicionada ao web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) para executar o aplicativo no ambiente de desenvolvimento. Desde que o ambiente não seja substituído na inicialização do aplicativo por `UseEnvironment` no compilador do host, definir a variável de ambiente permite que a [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) apareça quando o aplicativo é executado.

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

Configurar a variável de ambiente para `ASPNETCORE_ENVIRONMENT` só é recomendado para servidores de preparo e de teste que não estejam expostos à Internet. Remova a variável de ambiente do arquivo *web.config* após a solução de problemas. Para obter informações sobre como definir variáveis de ambiente no *web.config*, confira [Elemento filho environmentVariables de aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

## <a name="common-startup-errors"></a>Erros de inicialização comuns

Consulte <xref:host-and-deploy/azure-iis-errors-reference>. A maioria dos problemas comuns que impedem a inicialização do aplicativo é abordada no tópico de referência.

## <a name="obtain-data-from-an-app"></a>Obter dados de um aplicativo

Se um aplicativo for capaz de responder às solicitações, obtenha as solicitações, conexão e dados adicionais do aplicativo que usar o middleware embutido de terminal. Para saber mais e obter um código de exemplo, consulte <xref:test/troubleshoot#obtain-data-from-an-app>.

## <a name="slow-or-hanging-app"></a>Aplicativo lento ou travando

Quando um aplicativo responde lentamente ou trava em uma solicitação, obtenha e analise um [arquivo de despejo](/visualstudio/debugger/using-dump-files). Arquivos de despejo podem ser obtidos usando qualquer uma das ferramentas a seguir:

* [ProcDump](/sysinternals/downloads/procdump)
* [DebugDiag](https://www.microsoft.com/download/details.aspx?id=49924)
* WinDbg: [Baixar as ferramentas de depuração para Windows](https://developer.microsoft.com/windows/hardware/download-windbg), [Depuração usando o WinDbg](/windows-hardware/drivers/debugger/debugging-using-windbg)

## <a name="remote-debugging"></a>Depuração remota

Veja [Depuração remota do ASP.NET Core em um computador de IIS remoto no Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) na documentação do Visual Studio.

## <a name="application-insights"></a>Informações do aplicativo

O [Application Insights](/azure/application-insights/) fornece telemetria de aplicativos hospedados pelo IIS, incluindo recursos de relatório e de registro de erros em log. O Application Insights só pode relatar erros ocorridos depois que o aplicativo é iniciado quando os recursos de registro em log do aplicativo se tornam disponíveis. Para obter mais informações, veja [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="additional-advice"></a>Orientações adicionais

Algumas vezes um aplicativo em funcionamento falha imediatamente após atualizar o SDK do .NET Core nas versões de pacote ou de computador de desenvolvimento no aplicativo. Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais. A maioria desses problemas pode ser corrigida seguindo estas instruções:

1. Exclua as pastas *bin* e *obj*.
1. Limpe os caches de pacote em *%UserProfile%\\.nuget\\packages* e *%LocalAppData%\\Nuget\\v3-cache*.
1. Restaure e recompile o projeto.
1. Confirme que a implantação anterior no servidor foi completamente excluída antes de reimplantar o aplicativo.

> [!TIP]
> Uma maneira prática de se limpar caches do pacote é executar `dotnet nuget locals all --clear` de um prompt de comando.
>
> Também é possível limpar os caches de pacote usando a ferramenta [nuget.exe](https://www.nuget.org/downloads) e executando o comando `nuget locals all -clear`. *nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
