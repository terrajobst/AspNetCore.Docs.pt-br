---
title: Solucionar problemas do ASP.NET Core no IIS
author: guardrex
description: Saiba como diagnosticar problemas com as implantações do IIS (Serviços de Informações da Internet) de aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 4df370dd9b1a5a651bcf767b8b9ace4220bdc345
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313651"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a>Solucionar problemas do ASP.NET Core no IIS

Por [Luke Latham](https://github.com/guardrex)

Este artigo fornece instruções sobre como diagnosticar um problema de inicialização do aplicativo ASP.NET Core ao hospedar com [IIS (Serviços de Informações da Internet)](/iis). As informações neste artigo se aplicam à hospedagem em IIS no Windows Server e Windows Desktop.

Tópicos adicionais de solução de problemas:

* O Serviço de Aplicativo do Azure também usa o [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) e o IIS para hospedar aplicativos. Para obter conselhos sobre solução de problemas especificamente do Serviço de Aplicativo do Azure, confira <xref:host-and-deploy/azure-apps/troubleshoot>.
* <xref:fundamentals/error-handling> aborda como tratar erros em aplicativos do ASP.NET Core durante o desenvolvimento em um sistema local.
* [Aprender a depurar usando o Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) apresenta os recursos do depurador do Visual Studio.
* [Depurar com o Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) descreve o suporte de depuração interno do Visual Studio Code.

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

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

## <a name="create-a-dump"></a>Criar um despejo de memória

Um *despejo de memória* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, falha de inicialização ou lentidão de aplicativo.

### <a name="app-crashes-or-encounters-an-exception"></a>O aplicativo falha ou encontra uma exceção

Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):

1. Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`. O pool de aplicativos deve ter acesso para gravação à pasta.
1. Execute o [script EnableDumps do PowerShell](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):
   * Se o aplicativo usa o [modelo de hospedagem em processo](xref:host-and-deploy/iis/index#in-process-hosting-model), execute o script para *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Se o aplicativo usa o [modelo de hospedagem fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), execute o script para *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Execute o aplicativo sob as condições que causam a falha.
1. Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):
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

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>O aplicativo trava, falha durante a inicialização ou executa normalmente

Quando um aplicativo *travar* (para de responder, mas não falha), falhar durante a inicialização ou executar normalmente, veja [Arquivos de despejo de memória do modo de usuário: escolher a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta adequada para produzir o despejo de memória.

### <a name="analyze-the-dump"></a>Analisar o despejo de memória

Um despejo de memória pode ser analisado usando várias abordagens. Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

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
