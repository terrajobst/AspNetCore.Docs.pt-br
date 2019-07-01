---
title: Solucionar problemas no ASP.NET Core no Serviço de Aplicativo do Azure
author: guardrex
description: Saiba como diagnosticar problemas com implantações do Serviço de Aplicativo do Azure do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: d78499c1a82a011239f6b62b546f304a5d5017e2
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313753"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a>Solucionar problemas no ASP.NET Core no Serviço de Aplicativo do Azure

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Este artigo fornece instruções sobre como diagnosticar um problema de inicialização do aplicativo ASP.NET Core ao usar as ferramentas de diagnóstico do Serviço de Aplicativo do Azure. Para obter conselhos sobre solução de problemas adicionais, consulte [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](/azure/app-service/app-service-diagnostics) e [Como: monitorar aplicativos no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-monitor) na documentação do Azure.

Tópicos adicionais de solução de problemas:

* O IIS também usa o [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para hospedar aplicativos. Para obter conselhos sobre solução de problemas especificamente do IIS, confira <xref:host-and-deploy/iis/troubleshoot>.
* <xref:fundamentals/error-handling> aborda como tratar erros em aplicativos do ASP.NET Core durante o desenvolvimento em um sistema local.
* [Aprender a depurar usando o Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) apresenta os recursos do depurador do Visual Studio.
* [Depurar com o Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) descreve o suporte de depuração interno do Visual Studio Code.

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a>Solucionar problemas de inicialização do aplicativo

### <a name="application-event-log"></a>Log de Eventos do Aplicativo

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

##### <a name="current-release"></a>Versão atual

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

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>Implantação dependente da estrutura em execução em uma versão de visualização

*Requer a instalação da extensão de site de tempo de execução do ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` é a versão de tempo de execução)
1. Execute o aplicativo: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testar um aplicativo de 64 bits (x64)

##### <a name="current-release"></a>Versão atual

* Se o aplicativo for uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd) de 64 bits (x64):
  1. `cd D:\Program Files\dotnet`
  1. Execute o aplicativo: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Se o aplicativo é uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd):
  1. `cd D:\home\site\wwwroot`
  1. Execute o aplicativo: `{ASSEMBLY NAME}.exe`

A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>Implantação dependente da estrutura em execução em uma versão de visualização

*Requer a instalação da extensão de site de tempo de execução do ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` é a versão de tempo de execução)
1. Execute o aplicativo: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

A saída do console do aplicativo, mostrando eventuais erros, é conectada ao console do Kudu.

### <a name="aspnet-core-module-stdout-log"></a>Log de stdout do Módulo do ASP.NET Core

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

> [!WARNING]
> Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor. Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados. Somente use o log de stdout para solucionar problemas de inicialização de aplicativo.
>
> Para registro em log geral em um aplicativo ASP.NET Core após a inicialização, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs. Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a>Log de depuração do Módulo do ASP.NET Core

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

1. Para desabilitar o log de depuração avançado, execute um destes procedimentos:
   * Remova o `<handlerSettings>` do arquivo *web.config* localmente e reimplante o aplicativo.
   * Use o console do Kudu para editar o arquivo *web.config* e remover a seção `<handlerSettings>`. Salve o arquivo.

> [!WARNING]
> A falha ao desabilitar o log de depuração pode levar a falhas de aplicativo ou de servidor. Não há nenhum limite no tamanho do arquivo de log. Somente use o log de depuração para solucionar problemas de inicialização de aplicativo.
>
> Para registro em log geral em um aplicativo ASP.NET Core após a inicialização, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs. Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).

::: moniker-end

## <a name="common-startup-errors"></a>Erros de inicialização comuns

Consulte <xref:host-and-deploy/azure-iis-errors-reference>. A maioria dos problemas comuns que impedem a inicialização do aplicativo é abordada no tópico de referência.

## <a name="slow-or-hanging-app"></a>Aplicativo lento ou travando

Para saber mais sobre quando um aplicativo responde lentamente ou trava em uma solicitação, confira os seguintes artigos:

* [Solucionar problemas de desempenho lento do aplicativo Web no Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Use a Extensão de Site do Diagnosticador de Falhas para capturar o despejo para problemas de exceção intermitentes ou problemas de desempenho no aplicativo Web do Azure](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a>Depuração remota

Confira os seguintes tópicos:

* [Seção "Depuração remota de aplicativos Web" de "Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio"](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (documentação do Azure)
* [Depuração Remota do ASP.NET Core no IIS no Azure no Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure) (documentação do Visual Studio)

## <a name="application-insights"></a>Informações do aplicativo

O [Application Insights](https://azure.microsoft.com/services/application-insights/) fornece telemetria de aplicativos hospedados no Serviço de Aplicativo do Azure, incluindo recursos de relatório e de registro de erros em log. O Application Insights só pode relatar erros ocorridos depois que o aplicativo é iniciado quando os recursos de registro em log do aplicativo se tornam disponíveis. Para obter mais informações, veja [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="monitoring-blades"></a>Folhas de monitoramento

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

Sempre desabilite o registro em log de stdout após concluir a solução de problemas. Veja as instruções na seção [log de stdout do Módulo do ASP.NET Core](#aspnet-core-module-stdout-log).

Para exibir os logs de rastreamento de solicitação com falha (logs FREB):

1. Navegue até a folha **Diagnosticar e resolver problemas** no portal do Azure.
1. Selecione **Logs de Rastreamento de Solicitação com Falha** da área **FERRAMENTAS DE SUPORTE** da barra lateral.

Confira a [seção de Rastreamentos de solicitação com falha no tópico Habilitar o log de diagnósticos para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) e as [Perguntas frequentes do desempenho do aplicativo para Aplicativos Web no Azure: como ativar o rastreamento de solicitação com falha?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) para obter mais informações.

Para obter mais informações, veja [Habilitar log de diagnósticos para aplicativos Web no Serviço de Aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou de servidor. Não há limites para o tamanho do arquivo de log ou para o número de arquivos de log criados.
>
> Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs. Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Solucionar problemas de erros HTTP de "502 – gateway incorreto" e "503 – serviço não disponível" em seus aplicativos Web do Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Solucionar problemas de desempenho lento do aplicativo Web no Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Perguntas frequentes sobre o desempenho do aplicativo para aplicativos Web no Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Área restrita do aplicativo Web do Azure (limitações de execução de tempo de execução do Serviço de Aplicativo)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: experiência de diagnóstico e solução de problemas do Serviço de Aplicativo do Azure (vídeo com 12 minutos)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
