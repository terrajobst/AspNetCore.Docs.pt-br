---
title: Solucionar problemas ASP.NET Core no serviço Azure App e no IIS
author: guardrex
description: Saiba como diagnosticar problemas com implantações de serviço Azure App e Serviços de Informações da Internet (IIS) de aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/17/2019
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 46d4a11c594844e059fa8555255d7321f7b48631
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308789"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a>Solucionar problemas ASP.NET Core no serviço Azure App e no IIS

De [Luke Latham](https://github.com/guardrex) e [Justin Kotalik](https://github.com/jkotalik)

Este artigo fornece informações sobre erros de inicialização de aplicativo comuns e instruções sobre como diagnosticar erros quando um aplicativo é implantado no serviço de Azure App ou IIS:

[Erros de inicialização do aplicativo](#app-startup-errors)  
Explica cenários de código de status HTTP de inicialização comuns.

[Solucionar problemas no serviço Azure App](#troubleshoot-on-azure-app-service)  
Fornece conselhos de solução de problemas para aplicativos implantados no serviço Azure App.

[Solução de problemas no IIS](#troubleshoot-on-iis)  
Fornece conselhos de solução de problemas para aplicativos implantados no IIS ou em execução no IIS Express localmente. A orientação se aplica às implantações do Windows Server e do Windows desktop.

[Limpar caches de pacote](#clear-package-caches)  
Explica o que fazer quando pacotes incoerentes interrompem um aplicativo ao executar atualizações importantes ou alterar versões de pacotes.

[Recursos adicionais](#additional-resources)  
Lista tópicos adicionais de solução de problemas.

## <a name="app-startup-errors"></a>Erros de inicialização do aplicativo

::: moniker range=">= aspnetcore-2.2"

No Visual Studio, um projeto do ASP.NET Core usa por padrão a hospedagem do [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante a depuração. Uma *falha de 502,5 processo* ou uma *falha de início de 500,30* que ocorre quando a depuração local pode ser diagnosticada usando o Conselho neste tópico.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

No Visual Studio, um projeto do ASP.NET Core usa por padrão a hospedagem do [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) durante a depuração. Uma *falha de processo 502,5* que ocorre ao depurar localmente pode ser diagnosticada usando o Conselho neste tópico.

::: moniker-end

### <a name="500-internal-server-error"></a>500 Erro Interno do Servidor

O aplicativo é iniciado, mas um erro impede o servidor de atender à solicitação.

Esse erro ocorre no código do aplicativo durante a inicialização ou durante a criação de uma resposta. A resposta poderá não conter nenhum conteúdo, ou a resposta poderá ser exibida como um *500 – Erro Interno do Servidor* no navegador. O Log de Eventos do Aplicativo geralmente indica que o aplicativo iniciou normalmente. Da perspectiva do servidor, isso está correto. O aplicativo foi iniciado, mas não é capaz de gerar uma resposta válida. Execute o aplicativo em um prompt de comando no servidor ou habilite o log de stdout do módulo ASP.NET Core para solucionar o problema.

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a>500.0 Falha de carregamento de manipulador em processo

O processo de trabalho falha. O aplicativo não foi iniciado.

O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) falha ao localizar o .NET Core CLR e encontrar o manipulador de solicitação em processo (*aspnetcorev2_inprocess. dll*). Verifique se:

* O aplicativo destina-se ao pacote NuGet [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) ou ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).
* A versão da estrutura compartilhada do ASP.NET Core a que o aplicativo se destina está instalada no computador de destino.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 Falha de carregamento de manipulador fora de processo

O processo de trabalho falha. O aplicativo não foi iniciado.

O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) falha ao localizar o manipulador de solicitação de hospedagem fora do processo. Verifique se a *aspnetcorev2_outofprocess.dll* está presente em uma subpasta próxima a *aspnetcorev2.dll*.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a>500.0 Falha de carregamento de manipulador em processo

O processo de trabalho falha. O aplicativo não foi iniciado.

Erro desconhecido ao carregar ASP.NET Core componentes do [módulo](xref:host-and-deploy/aspnet-core-module) . Execute uma das seguintes ações:

* Entre em contato com o [Suporte da Microsoft](https://support.microsoft.com/oas/default.aspx?prid=15832) (selecione **Ferramentas para Desenvolvedores** e, em seguida, **ASP.NET Core**).
* Faça uma pergunta no Stack Overflow.
* Registre um problema no nosso [Repositório do GitHub](https://github.com/aspnet/AspNetCore).

### <a name="50030-in-process-startup-failure"></a>500.30 Falha de inicialização em processo

O processo de trabalho falha. O aplicativo não foi iniciado.

O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tenta iniciar o .NET Core CLR em processo, mas falha ao iniciar. A causa de uma falha de inicialização do processo normalmente pode ser determinada das entradas no log de eventos do aplicativo e do log de stdout do módulo ASP.NET Core.

Uma condição de falha comum é o aplicativo configurado incorretamente, direcionado a uma versão da estrutura compartilhada do ASP.NET Core que não está presente. Verifique quais versões da estrutura compartilhada do ASP.NET Core estão instaladas no computador de destino.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500.31 O ANCM não pôde encontrar dependências nativas

O processo de trabalho falha. O aplicativo não foi iniciado.

O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tenta iniciar o tempo de execução do .NET Core em processo, mas falha ao iniciar. A causa mais comum dessa falha de inicialização é quando o tempo de execução `Microsoft.NETCore.App` ou `Microsoft.AspNetCore.App` não está instalado. Se o aplicativo for implantado no ASP.NET Core 3.0 de destino e essa versão não existir no computador, esse erro ocorrerá. Segue um exemplo de mensagem de erro:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

A mensagem de erro lista todas as versões instaladas do .NET Core e a versão solicitada pelo aplicativo. Para corrigir esse erro:

* Instale a versão adequada do .NET Core no computador.
* Altere o aplicativo para uma versão do .NET Core que está presente no computador de destino.
* Publique o aplicativo como uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd).

Durante a execução no desenvolvimento (quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` está definida como `Development`), o erro específico é gravado na resposta HTTP. A causa de uma falha de inicialização do processo também é encontrada no log de eventos do aplicativo.

### <a name="50032-ancm-failed-to-load-dll"></a>500.32 O ANCM não pôde carregar o dll

O processo de trabalho falha. O aplicativo não foi iniciado.

A causa mais comum para esse erro é que o aplicativo foi publicado para uma arquitetura de processador incompatível. Esse erro ocorrerá se o processo de trabalho estiver em execução como um aplicativo de 32 bits e o aplicativo tiver sido publicado para o destino de 64 bits.

Para corrigir esse erro:

* Republique o aplicativo para a mesma arquitetura de processador que o processo de trabalho.
* Publique o aplicativo como uma [implantação dependente da estrutura](/dotnet/core/deploying/#framework-dependent-executables-fde).

### <a name="50033-ancm-request-handler-load-failure"></a>500.33 O ANCM não pôde carregar o manipulador de solicitação

O processo de trabalho falha. O aplicativo não foi iniciado.

O aplicativo não referenciou a estrutura `Microsoft.AspNetCore.App`. Somente aplicativos direcionados `Microsoft.AspNetCore.App` à estrutura podem ser hospedados pelo [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Para corrigir esse erro, confirme se o aplicativo está direcionado para a estrutura `Microsoft.AspNetCore.App`. Confira o `.runtimeconfig.json` para verificar a estrutura de destino do aplicativo.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500.34 Não há suporte para modelos de hospedagem mistos do ANCM

O processo de trabalho não pode executar um aplicativo em processo e um aplicativo fora do processo no mesmo processo.

Para corrigir esse erro, execute aplicativos em pools de aplicativos do IIS separados.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500.35 Vários aplicativos do ANCM em processo no mesmo processo

O processo de trabalho não pode executar um aplicativo em processo e um aplicativo fora do processo no mesmo processo.

Para corrigir esse erro, execute aplicativos em pools de aplicativos do IIS separados.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500.36 Falha ao carregar o manipulador de fora do processo do ANCM

O manipulador de solicitação de fora do processo *aspnetcorev2_outofprocess.dll* não está próximo do arquivo *aspnetcorev2.dll*. Isso indica uma instalação corrompida do [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Para corrigir esse erro, repare a instalação do [Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (para IIS) ou do Visual Studio (para o IIS Express).

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500.37 O ANCM não pôde ser iniciado dentro do limite de tempo de inicialização

O ANCM não pôde ser iniciado dentro do limite de tempo de inicialização fornecido. Por padrão, o tempo limite é de 120 segundos.

Esse erro pode ocorrer ao iniciar um grande número de aplicativos no mesmo computador. Verifique se há picos de uso de CPU/memória no servidor durante a inicialização. Talvez você precise balancear o processo de inicialização de vários aplicativos.

::: moniker-end

### <a name="5025-process-failure"></a>502.5 Falha de processo

O processo de trabalho falha. O aplicativo não foi iniciado.

O [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tenta iniciar o processo de trabalho, mas falhar ao iniciar. A causa de uma falha de inicialização do processo normalmente pode ser determinada das entradas no log de eventos do aplicativo e do log de stdout do módulo ASP.NET Core.

Uma condição de falha comum é o aplicativo configurado incorretamente, direcionado a uma versão da estrutura compartilhada do ASP.NET Core que não está presente. Verifique quais versões da estrutura compartilhada do ASP.NET Core estão instaladas no computador de destino. A *estrutura compartilhada* é o conjunto de assemblies (arquivos *. dll*) instalado no computador e referenciado por um metapacote como `Microsoft.AspNetCore.App`. A referência do metapacote pode especificar a versão mínima necessária. Saiba mais em [A estrutura compartilhada](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

A página do erro *502.5 – Falha no Processo* é retornada quando um erro de configuração de hospedagem ou do aplicativo faz com que o processo de trabalho falhe:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Falha ao iniciar o aplicativo (ErrorCode '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

O aplicativo falhou ao ser iniciado porque o assembly do aplicativo ( *.dll*) não pôde ser carregado.

Esse erro ocorre quando há uma incompatibilidade de número de bits entre o aplicativo publicado e o processo w3wp/iisexpress.

Confirme se a configuração de 32 bits do pool de aplicativos está correta:

1. Selecione o pool de aplicativos no **Pools de Aplicativos** do Gerenciador do IIS.
1. Selecione **Configurações Avançadas** em **Editar Pool de Aplicativos** no painel **Ações**.
1. Defina **Habilitar Aplicativos de 32 bits**:
   * Se estiver implantando um aplicativo de 32 bits (x86), defina o valor como `True`.
   * Se estiver implantando um aplicativo de 64 bits (x64), defina o valor como `False`.

### <a name="connection-reset"></a>Redefinição de conexão

Se um erro ocorrer após os cabeçalhos serem enviados, será tarde demais para o servidor enviar um **500 – Erro Interno do Servidor** no caso de um erro ocorrer. Isso geralmente acontece quando ocorre um erro durante a serialização de objetos complexos para uma resposta. Esse tipo de erro é exibida como um erro de *redefinição de conexão* no cliente. O [Log de aplicativo](xref:fundamentals/logging/index) pode ajudar a solucionar esses tipos de erros.

### <a name="default-startup-limits"></a>Limites de inicialização padrão

O [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) é configurado com um *startupTimeLimit* padrão de 120 segundos. Quando deixado no valor padrão, um aplicativo pode levar até dois minutos para iniciar antes que uma falha do processo seja registrada em log pelo módulo. Para obter informações sobre como configurar o módulo, veja [Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Solucionar problemas no serviço Azure App

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Log de eventos do aplicativo (serviço Azure App)

Para acessar o Log de Eventos do Aplicativo, use a folha **Diagnosticar e solucionar problemas** no portal do Azure:

1. No portal do Azure, abra o aplicativo nos **Serviços de Aplicativos**.
1. Selecione **Diagnóstico e solução de problemas**.
1. Selecione o título **Ferramentas de Diagnóstico**.
1. Em **Ferramentas de Suporte**, selecione o botão **Eventos do Aplicativo**.
1. Examine o erro mais recente fornecido pela entrada *IIS AspNetCoreModule* ou *IIS AspNetCoreModule V2* na coluna **Origem**.

Uma alternativa ao uso da folha **Diagnosticar e resolver problemas** é examinar o arquivo de Log de Eventos do Aplicativo diretamente usando o [Kudu](https://github.com/projectkudu/kudu/wiki):

1. Abra **Ferramentas Avançadas** na área **Ferramentas de Desenvolvimento**. Selecione o botão **Ir&rarr;** . O console do Kudu é aberto em uma nova janela ou guia do navegador.
1. Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.
1. Abra a pasta **LogFiles**.
1. Selecione o ícone de lápis ao lado do arquivo *eventlog.xml*.
1. Examine o log. Role até o final do log para ver os eventos mais recentes.

### <a name="run-the-app-in-the-kudu-console"></a>Execute o aplicativo no console do Kudu

Muitos erros de inicialização não produzem informações úteis no Log de Eventos do Aplicativo. Você pode executar o aplicativo no Console de Execução Remota do [Kudu](https://github.com/projectkudu/kudu/wiki) para descobrir o erro:

1. Abra **Ferramentas Avançadas** na área **Ferramentas de Desenvolvimento**. Selecione o botão **Ir&rarr;** . O console do Kudu é aberto em uma nova janela ou guia do navegador.
1. Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Testar um aplicativo de 32 bits (x86)

**Versão atual**

1. `cd d:\home\site\wwwroot`
1. Execute o aplicativo:
   * Se o aplicativo é uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Se o aplicativo é uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd):

     ```console
     {ASSEMBLY NAME}.exe
     ```

A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.

**Implantação dependente de estrutura em execução em uma versão de visualização**

*Requer a instalação da extensão de site de tempo de execução do ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` é a versão de tempo de execução)
1. Execute o aplicativo: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testar um aplicativo de 64 bits (x64)

**Versão atual**

* Se o aplicativo for uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd) de 64 bits (x64):
  1. `cd D:\Program Files\dotnet`
  1. Execute o aplicativo: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Se o aplicativo é uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd):
  1. `cd D:\home\site\wwwroot`
  1. Execute o aplicativo: `{ASSEMBLY NAME}.exe`

A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.

**Implantação dependente de estrutura em execução em uma versão de visualização**

*Requer a instalação da extensão de site de tempo de execução do ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` é a versão de tempo de execução)
1. Execute o aplicativo: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Log de stdout do módulo ASP.NET Core (serviço Azure App)

O log de stdout do Módulo do ASP.NET Core geralmente registra mensagens de erro úteis não encontradas no Log de Eventos do Aplicativo. Para habilitar e exibir logs de stdout:

1. Navegue até a folha **Diagnosticar e resolver problemas** no portal do Azure.
1. Em **SELECIONAR CATEGORIA DE PROBLEMA**, selecione o botão **Aplicativo Web Inoperante**.
1. Em **Soluções Sugeridas** > **Habilitar o Redirecionamento de Log de Stdout**, selecione o botão para **Abrir o Console do Kudu para editar o Web.Config**.
1. No **Console de Diagnóstico** do Kudu, abra as pastas no caminho **site** > **wwwroot**. Role para baixo para revelar o arquivo *web.config* na parte inferior da lista.
1. Clique no ícone de lápis ao lado do arquivo *web.config*.
1. Defina **stdoutLogEnabled** para `true` e altere o caminho **stdoutLogFile** para `\\?\%home%\LogFiles\stdout`.
1. Selecione **Salvar** para salvar o arquivo *web.config* atualizado.
1. Faça uma solicitação ao aplicativo.
1. Retorne para o portal do Azure. Selecione a folha **Ferramentas Avançadas** na área **FERRAMENTAS DE DESENVOLVIMENTO**. Selecione o botão **Ir&rarr;** . O console do Kudu é aberto em uma nova janela ou guia do navegador.
1. Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.
1. Selecione a pasta **LogFiles**.
1. Inspecione a coluna **Modificado em** e selecione o ícone de lápis para editar o log de stdout com a data da última modificação.
1. Quando o arquivo de log é aberto, o erro é exibido.

Desabilite o registro em log de stdout quando a solução de problemas for concluída:

1. No **Console de Diagnóstico** do Kudu, retorne para o caminho **site** > **wwwroot** para revelar o arquivo *web.config*. Abra o arquivo **web.config** novamente, selecionando o ícone de lápis.
1. Defina **stdoutLogEnabled** para `false`.
1. Selecione **Salvar** para salvar o arquivo.

Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor. Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados. Somente use o log de stdout para solucionar problemas de inicialização de aplicativo.
>
> Para registro em log geral em um aplicativo ASP.NET Core após a inicialização, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs. Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>Log de depuração do módulo ASP.NET Core (serviço Azure App)

O log de depuração do Módulo do ASP.NET Core fornece registro em log adicional e mais profundo do Módulo do ASP.NET Core. Para habilitar e exibir logs de stdout:

1. Para habilitar o log de diagnóstico avançado, execute um destes procedimentos:
   * Siga as instruções em [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) para configurar o aplicativo para um log de diagnósticos avançado. Reimplante o aplicativo.
   * Adicione a `<handlerSettings>` mostrada em [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) para o arquivo *web.config* do aplicativo ao vivo usando o console do Kudu:
     1. Abra **Ferramentas Avançadas** na área **Ferramentas de Desenvolvimento**. Selecione o botão **Ir&rarr;** . O console do Kudu é aberto em uma nova janela ou guia do navegador.
     1. Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.
     1. Abra as pastas no caminho **site** > **wwwroot**. Edite o arquivo *web.config* selecionando o botão de lápis. Adicione a seção `<handlerSettings>` conforme mostrado em [Logs de diagnóstico avançados](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Selecione o botão **Salvar**.
1. Abra **Ferramentas Avançadas** na área **Ferramentas de Desenvolvimento**. Selecione o botão **Ir&rarr;** . O console do Kudu é aberto em uma nova janela ou guia do navegador.
1. Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.
1. Abra as pastas no caminho **site** > **wwwroot**. Se você não fornecer um caminho para o arquivo *aspnetcore-debug.log*, o arquivo aparecerá na lista. Se você tiver fornecido um caminho, navegue até o local do arquivo de log.
1. Abra o arquivo de log com o botão de lápis ao lado do nome do arquivo.

Desabilite o registro em log de depuração quando a solução de problemas for concluída:

Para desabilitar o log de depuração avançado, execute um destes procedimentos:

* Remova o `<handlerSettings>` do arquivo *web.config* localmente e reimplante o aplicativo.
* Use o console do Kudu para editar o arquivo *web.config* e remover a seção `<handlerSettings>`. Salve o arquivo.

Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> A falha ao desabilitar o log de depuração pode levar a falhas de aplicativo ou de servidor. Não há nenhum limite no tamanho do arquivo de log. Somente use o log de depuração para solucionar problemas de inicialização de aplicativo.
>
> Para registro em log geral em um aplicativo ASP.NET Core após a inicialização, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs. Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).

::: moniker-end

### <a name="slow-or-hanging-app-azure-app-service"></a>Aplicativo lento ou suspenso (serviço de Azure App)

Para saber mais sobre quando um aplicativo responde lentamente ou trava em uma solicitação, confira os seguintes artigos:

* [Solucionar problemas de desempenho lento do aplicativo Web no Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Use a Extensão de Site do Diagnosticador de Falhas para capturar o despejo para problemas de exceção intermitentes ou problemas de desempenho no aplicativo Web do Azure](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Folhas de monitoramento

As folhas de monitoramento fornecem uma experiência alternativa de solução de problemas para os métodos descritos anteriormente no tópico. Essas folhas podem ser usadas para diagnosticar erros da série 500.

Verifique se as Extensões do ASP.NET Core estão instaladas. Se as extensões não estiverem instaladas, instale-as manualmente:

1. Na seção de folha **FERRAMENTAS DE DESENVOLVIMENTO**, selecione a folha **Extensões**.
1. As **Extensões do ASP.NET Core** devem aparecer na lista.
1. Se as extensões não estiverem instaladas, selecione o botão **Adicionar**.
1. Escolha as **Extensões do ASP.NET Core** da lista.
1. Selecione **OK** para aceitar os termos legais.
1. Selecione **OK** na folha **Adicionar extensão**.
1. Uma mensagem pop-up informativa indica quando as extensões são instaladas com êxito.

Se o registro em log de stdout não estiver habilitado, siga estas etapas:

1. No portal do Azure, selecione a folha **Ferramentas Avançadas** na área **FERRAMENTAS DE DESENVOLVIMENTO**. Selecione o botão **Ir&rarr;** . O console do Kudu é aberto em uma nova janela ou guia do navegador.
1. Usando a barra de navegação na parte superior da página, abra **Console de depuração** e selecione **CMD**.
1. Abra as pastas no caminho **site** > **wwwroot** e role para baixo para revelar o arquivo *web.config* na parte inferior da lista.
1. Clique no ícone de lápis ao lado do arquivo *web.config*.
1. Defina **stdoutLogEnabled** para `true` e altere o caminho **stdoutLogFile** para `\\?\%home%\LogFiles\stdout`.
1. Selecione **Salvar** para salvar o arquivo *web.config* atualizado.

Prossiga para ativar o log de diagnóstico:

1. No portal do Azure, selecione a folha **Logs de diagnóstico**.
1. Selecione a opção **Ligado** para **Log de Aplicativo (Sistema de arquivos)** e **Mensagens de erro detalhadas**. Selecione o botão **Salvar** na parte superior da folha.
1. Para incluir o rastreamento de solicitação com falha, também conhecido como FREB (Buffer de Evento de Solicitação com Falha), selecione a opção **Ligado** para o **Rastreamento de solicitação com falha**.
1. Selecione a folha **Fluxo de log**, que é listada imediatamente sob a folha **Logs de diagnóstico** no portal.
1. Faça uma solicitação ao aplicativo.
1. Dentro dos dados de fluxo de log, a causa do erro é indicada.

Sempre desabilite o registro em log de stdout após concluir a solução de problemas.

Para exibir os logs de rastreamento de solicitação com falha (logs FREB):

1. Navegue até a folha **Diagnosticar e resolver problemas** no portal do Azure.
1. Selecione **Logs de Rastreamento de Solicitação com Falha** da área **FERRAMENTAS DE SUPORTE** da barra lateral.

Confira a [seção de Rastreamentos de solicitação com falha no tópico Habilitar o log de diagnósticos para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) e as [Perguntas frequentes do desempenho do aplicativo para Aplicativos Web no Azure: como ativar o rastreamento de solicitação com falha?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) para obter mais informações.

Para obter mais informações, veja [Habilitar log de diagnósticos para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor. Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados.
>
> Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs. Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Solucionar problemas no IIS

### <a name="application-event-log-iis"></a>Log de eventos do aplicativo (IIS)

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

### <a name="aspnet-core-module-stdout-log-iis"></a>Log de stdout do módulo ASP.NET Core (IIS)

Para habilitar e exibir logs de stdout:

1. Navegue até a pasta de implantação do site no sistema de hospedagem.
1. Se a pasta *logs* não estiver presente, crie-a. Para obter instruções sobre como habilitar o MSBuild para criar a pasta *logs* na implantação automaticamente, veja o tópico [Estrutura de diretórios](xref:host-and-deploy/directory-structure).
1. Edite o arquivo *web.config*. Defina **stdoutLogEnabled** para `true` e altere o caminho **stdoutLogFile** para apontar para a pasta *logs* (por exemplo, `.\logs\stdout`). `stdout` no caminho é o prefixo do nome do arquivo de log. Uma extensão de arquivo, uma ID do processo e um carimbo de data/hora são adicionados automaticamente quando o log é criado. Usando `stdout` como o prefixo do nome do arquivo, um arquivo de log típico é nomeado *stdout_20180205184032_5412.log*.
1. Verifique se a identidade do pool de aplicativos tem permissões de gravação para a pasta *logs*.
1. Salve o arquivo *web.config* atualizado.
1. Faça uma solicitação ao aplicativo.
1. Navegue até a pasta *logs*. Localize e abra o log de stdout mais recente.
1. Estude o log em busca de erros.

Desabilite o registro em log de stdout quando a solução de problemas for concluída:

1. Edite o arquivo *web.config*.
1. Defina **stdoutLogEnabled** para `false`.
1. Salve o arquivo.

Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor. Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados.
>
> Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs. Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-iis"></a>Log de depuração do módulo ASP.NET Core (IIS)

Adicione as seguintes configurações do manipulador ao arquivo *Web. config* do aplicativo para habilitar ASP.NET Core log de depuração do módulo:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Confirme se o caminho especificado para o log existe e se a identidade do pool de aplicativos tem permissões de gravação no local.

Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

::: moniker-end

### <a name="enable-the-developer-exception-page"></a>Habilitar a página de exceção do desenvolvedor

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

### <a name="obtain-data-from-an-app"></a>Obter dados de um aplicativo

Se um aplicativo for capaz de responder às solicitações, obtenha as solicitações, conexão e dados adicionais do aplicativo que usar o middleware embutido de terminal. Para saber mais e obter um código de exemplo, consulte <xref:test/troubleshoot#obtain-data-from-an-app>.

### <a name="slow-or-hanging-app-iis"></a>Aplicativo lento ou suspenso (IIS)

Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.

#### <a name="app-crashes-or-encounters-an-exception"></a>O aplicativo falha ou encontra uma exceção

Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):

1. Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`. O pool de aplicativos deve ter acesso para gravação à pasta.
1. Execute o [script EnableDumps do PowerShell](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):
   * Se o aplicativo usa o [modelo de hospedagem em processo](xref:host-and-deploy/iis/index#in-process-hosting-model), execute o script para *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Se o aplicativo usa o [modelo de hospedagem fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), execute o script para *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Execute o aplicativo sob as condições que causam a falha.
1. Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Se o aplicativo usa o [modelo de hospedagem em processo](xref:host-and-deploy/iis/index#in-process-hosting-model), execute o script para *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Se o aplicativo usa o [modelo de hospedagem fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), execute o script para *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente. O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.

> [!WARNING]
> Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>O aplicativo trava, falha durante a inicialização ou executa normalmente

Quando um aplicativo *travar* (para de responder, mas não falha), falhar durante a inicialização ou executar normalmente, veja [Arquivos de despejo de memória do modo de usuário: escolher a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta adequada para produzir o despejo de memória.

#### <a name="analyze-the-dump"></a>Analisar o despejo de memória

Um despejo de memória pode ser analisado usando várias abordagens. Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Limpar caches de pacote

Às vezes, um aplicativo funcional falha imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo. Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais. A maioria desses problemas pode ser corrigida seguindo estas instruções:

1. Exclua as pastas *bin* e *obj*.
1. Limpe os caches de pacote executando `dotnet nuget locals all --clear` em um shell de comando.

   A limpeza de caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e `nuget locals all -clear`executando o comando. *nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).

1. Restaure e recompile o projeto.
1. Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Documentação do Azure

* [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Seção aplicativos Web de depuração remota de solução de problemas de um aplicativo Web no serviço Azure App usando o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](/azure/app-service/app-service-diagnostics)
* [Como: monitorar aplicativos no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-monitor)
* [Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Solucionar problemas de erros HTTP de "502 – gateway incorreto" e "503 – serviço não disponível" em seus aplicativos Web do Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Solucionar problemas de desempenho lento do aplicativo Web no Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Perguntas frequentes sobre o desempenho do aplicativo para aplicativos Web no Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Área restrita do aplicativo Web do Azure (limitações de execução de tempo de execução do Serviço de Aplicativo)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: experiência de diagnóstico e solução de problemas do Serviço de Aplicativo do Azure (vídeo com 12 minutos)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Documentação do Visual Studio

* [ASP.NET Core de depuração remota no IIS no Azure no Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [ASP.NET Core de depuração remota em um computador IIS remoto no Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Aprenda a depurar usando o Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Documentação do Visual Studio Code

* [Depurar com o Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)
