---
title: Hospedar o ASP.NET Core em um serviço Windows
author: rick-anderson
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 4eed461788f8fffa2ea00d8c931b0a0f5aaf1b46
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656180"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hospedar o ASP.NET Core em um serviço Windows

::: moniker range=">= aspnetcore-3.0"

Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS. Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* [SDK do ASP.NET Core 2.1 ou posterior](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 ou posterior](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>Modelo de serviço de trabalho

O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada. Para usar o modelo como base para um aplicativo de Serviço Windows:

1. Crie um aplicativo de serviço de trabalho usando o modelo do .NET Core.
1. Siga as orientações na seção [Configuração de aplicativos](#app-configuration) para atualizar o aplicativo do Serviço de Trabalho para que ele possa ser executado como um Serviço Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>Configuração do aplicativo

O aplicativo requer uma referência de pacote para [Microsoft. Extensions. host. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

`IHostBuilder.UseWindowsService` é chamado ao compilar o host. Se o aplicativo estiver sendo executado como um Serviço Windows, o método:

* Define o tempo de vida do host como `WindowsServiceLifetime`.
* Define a [raiz do conteúdo](xref:fundamentals/index#content-root) como [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory). Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).
* Habilita o registro em log no log de eventos:
  * O nome do aplicativo é usado como o nome de origem padrão.
  * O nível de log padrão é *aviso* ou superior para um aplicativo com base em um modelo de ASP.NET Core que chama `CreateDefaultBuilder` para criar o host.
  * Substitua o nível de log padrão pela chave de `Logging:EventLog:LogLevel:Default` em *appSettings. json*/*appSettings. { Ambiente}. JSON* ou outro provedor de configuração.
  * Somente administradores podem criar novas fontes de evento. Quando uma fonte de evento não puder ser criada usando o nome do aplicativo, um aviso será registrado em log como a fonte do *Aplicativo* e os logs de eventos serão desabilitados.

Em `CreateHostBuilder` de *Program.cs*:

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Os aplicativos de exemplo a seguir acompanham este tópico:

* Exemplo de serviço de trabalho em segundo plano &ndash; um exemplo de aplicativo não Web baseado no [modelo de serviço de trabalho](#worker-service-template) que usa [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.
* Exemplo de serviço de aplicativo Web &ndash; um exemplo de aplicativo Web Razor Pages que é executado como um serviço do Windows com [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.

Para obter diretrizes do MVC, consulte os artigos em <xref:mvc/overview> e <xref:migration/22-to-30>.

## <a name="deployment-type"></a>Tipo de implantação

Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>.

Para um serviço baseado em aplicativo Web que usa as estruturas Razor Pages ou MVC, especifique o SDK Web no arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>FDD (Implantação dependente de estrutura)

A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino. Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável ( *.exe*), chamado de *executável dependente de estrutura*.

Se você estiver usando o [SDK da Web](#sdk), um arquivo *Web. config* , que normalmente é produzido ao publicar um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows. Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>SCD (Implantação Autossuficiente)

A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino. O runtime e as dependências do aplicativo são implantados com o aplicativo.

Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Para publicar para vários RIDs:

* Forneça os RIDs em uma lista delimitada por ponto e vírgula.
* Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).

## <a name="service-user-account"></a>Conta de usuário do serviço

Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.

Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.

A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.

Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).

Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado. Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Fazer logon como direitos de um serviço

Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:

1. Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.
1. Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.
1. Abra a política **Fazer logon como um serviço**.
1. Selecione **Adicionar Usuário ou Grupo**.
1. Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:
   1. Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.
   1. Selecione **Avançado**. Escolha **Localizar Agora**. Escolha a conta de usuário na lista. Selecione **OK**. Escolha **OK** novamente para adicionar o usuário à política.
1. Escolha **OK** ou **Aplicar** para aceitar as alterações.

## <a name="create-and-manage-the-windows-service"></a>Criar e gerenciar o Serviço Windows

### <a name="create-a-service"></a>Criar um serviço

Use comandos do PowerShell para registrar um serviço. Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}` &ndash; caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice`). Não inclua o executável do aplicativo no caminho. A barra à direita não é necessária.
* `{DOMAIN OR COMPUTER NAME\USER}` conta de usuário do serviço de &ndash; (por exemplo, `Contoso\ServiceUser`).
* `{SERVICE NAME}` &ndash; nome do serviço (por exemplo, `MyService`).
* `{EXE FILE PATH}` &ndash; o caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe`). Inclua nome do arquivo do executável com a extensão.
* `{DESCRIPTION}` descrição do serviço &ndash; (por exemplo, `My sample service`).
* `{DISPLAY NAME}` nome de exibição do serviço &ndash; (por exemplo, `My Service`).

### <a name="start-a-service"></a>Iniciar um serviço

Inicie o serviço com o seguinte comando do PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

O comando leva alguns segundos para iniciar o serviço.

### <a name="determine-a-services-status"></a>Determinar o status do serviço

Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

O status é relatado como um dos seguintes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Parar um serviço

Pare um serviço com o seguinte comando do Powershell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Remover um serviço

Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional. Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configurar pontos de extremidade

Por padrão, o ASP.NET Core é associado a `http://localhost:5000`. Configure a URL e a porta definindo a variável de ambiente `ASPNETCORE_URLS`.

Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS. Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.

> [!NOTE]
> Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.

## <a name="current-directory-and-content-root"></a>Diretório atual e a raiz do conteúdo

O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*. A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações). Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Usar ContentRootPath ou ContentRootFileProvider

Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para localizar os recursos do aplicativo.

Quando o aplicativo é executado como um serviço, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> define o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> como [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).

Os arquivos de configurações padrão do aplicativo, *appSettings. JSON* e *appSettings. { Environment}. JSON*, são carregados a partir da raiz do conteúdo do aplicativo chamando [CreateDefaultBuilder durante a construção do host](xref:fundamentals/host/generic-host#set-up-a-host).

Para outros arquivos de configurações carregados pelo código do desenvolvedor no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, não há necessidade de chamar <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. No exemplo a seguir, o arquivo *custom_settings. JSON* existe na raiz do conteúdo do aplicativo e é carregado sem definir explicitamente um caminho base:

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

Não tente usar <xref:System.IO.Directory.GetCurrentDirectory*> para obter um caminho de recurso porque um aplicativo de serviço do Windows retorna a pasta *C:\\Windows\\system32* como seu diretório atual.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Armazenar os arquivos do serviço em um local adequado no disco

Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.

## <a name="troubleshoot"></a>Solução de problemas

Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot>.

### <a name="common-errors"></a>Erros comuns

* Uma versão antiga ou de pré-lançamento do PowerShell está em uso.
* O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) . A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo. Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:
  * FDD do *compartimento/versão/{estrutura de destino}* (a)
  * *bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)
* O serviço não está em estado de execução.
* Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos. O caminho base de um serviço do Windows é *c:\\Windows\\system32*.
* O usuário não tem direitos de *logon como um serviço* .
* A senha do usuário expirou ou foi passada incorretamente ao executar o comando `New-Service` PowerShell.
* O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).
* A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.

### <a name="system-and-application-event-logs"></a>Logs de eventos do sistema e do aplicativo

Acesse os logs de eventos do sistema e do aplicativo:

1. Abra o menu Iniciar, procure *Visualizador de eventos*e selecione o aplicativo **Visualizador de eventos** .
1. No **Visualizador de Eventos**, abra o nó **Logs do Windows**.
1. Selecione **sistema** para abrir o log de eventos do sistema. Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.
1. Procure erros associados ao aplicativo com falha.

### <a name="run-the-app-at-a-command-prompt"></a>Execute o aplicativo em um prompt de comando

Muitos erros de inicialização não produzem informações úteis nos logs de eventos. Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem. Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Limpar caches de pacote

Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo. Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais. A maioria desses problemas pode ser corrigida seguindo estas instruções:

1. Exclua as pastas *bin* e *obj*.
1. Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.

   A limpeza dos caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e a execução do comando `nuget locals all -clear`. *nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).

1. Restaure e recompile o projeto.
1. Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.

### <a name="slow-or-hanging-app"></a>Aplicativo lento ou travando

Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.

#### <a name="app-crashes-or-encounters-an-exception"></a>O aplicativo falha ou encontra uma exceção

Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):

1. Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.
1. Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Execute o aplicativo sob as condições que causam a falha.
1. Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente. O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.

> [!WARNING]
> Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>O aplicativo trava, falha durante a inicialização ou executa normalmente

Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.

#### <a name="analyze-the-dump"></a>Analisar o despejo de memória

Um despejo de memória pode ser analisado usando várias abordagens. Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Recursos adicionais

* [Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS. Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* [SDK do ASP.NET Core 2.1 ou posterior](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 ou posterior](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Configuração do aplicativo

O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console. Verifique se o depurador está anexado ou se uma opção `--console` está presente. Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Se as condições forem falsas (o aplicativo for executado como um serviço):

* Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo. Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado. Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root). Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.
* Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.

Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.

Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.

No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo. Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Tipo de implantação

Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>.

Para um serviço baseado em aplicativo Web que usa as estruturas Razor Pages ou MVC, especifique o SDK Web no arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>FDD (Implantação dependente de estrutura)

A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino. Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável ( *.exe*), chamado de *executável dependente de estrutura*.

O [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contém a estrutura de destino. No exemplo a seguir, o RID é especificado como `win7-x64`. A propriedade `<SelfContained>` é definida como `false`. Essas propriedades instruem o SDK a gerar um arquivo executável ( *.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.

O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows. Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>SCD (Implantação Autossuficiente)

A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino. O runtime e as dependências do aplicativo são implantados com o aplicativo.

Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Para publicar para vários RIDs:

* Forneça os RIDs em uma lista delimitada por ponto e vírgula.
* Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).

Uma propriedade `<SelfContained>` está definida como `true`:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Conta de usuário do serviço

Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.

Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.

A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.

Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).

Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado. Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Fazer logon como direitos de um serviço

Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:

1. Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.
1. Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.
1. Abra a política **Fazer logon como um serviço**.
1. Selecione **Adicionar Usuário ou Grupo**.
1. Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:
   1. Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.
   1. Selecione **Avançado**. Escolha **Localizar Agora**. Escolha a conta de usuário na lista. Selecione **OK**. Escolha **OK** novamente para adicionar o usuário à política.
1. Escolha **OK** ou **Aplicar** para aceitar as alterações.

## <a name="create-and-manage-the-windows-service"></a>Criar e gerenciar o Serviço Windows

### <a name="create-a-service"></a>Criar um serviço

Use comandos do PowerShell para registrar um serviço. Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}` &ndash; caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice`). Não inclua o executável do aplicativo no caminho. A barra à direita não é necessária.
* `{DOMAIN OR COMPUTER NAME\USER}` conta de usuário do serviço de &ndash; (por exemplo, `Contoso\ServiceUser`).
* `{SERVICE NAME}` &ndash; nome do serviço (por exemplo, `MyService`).
* `{EXE FILE PATH}` &ndash; o caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe`). Inclua nome do arquivo do executável com a extensão.
* `{DESCRIPTION}` descrição do serviço &ndash; (por exemplo, `My sample service`).
* `{DISPLAY NAME}` nome de exibição do serviço &ndash; (por exemplo, `My Service`).

### <a name="start-a-service"></a>Iniciar um serviço

Inicie o serviço com o seguinte comando do PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

O comando leva alguns segundos para iniciar o serviço.

### <a name="determine-a-services-status"></a>Determinar o status do serviço

Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

O status é relatado como um dos seguintes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Parar um serviço

Pare um serviço com o seguinte comando do Powershell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Remover um serviço

Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Manipular eventos de início e de parada

Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:

1. Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional. Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configurar pontos de extremidade

Por padrão, o ASP.NET Core é associado a `http://localhost:5000`. Configure a URL e a porta definindo a variável de ambiente `ASPNETCORE_URLS`.

Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS. Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.

> [!NOTE]
> Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.

## <a name="current-directory-and-content-root"></a>Diretório atual e a raiz do conteúdo

O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*. A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações). Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Defina o caminho da raiz do conteúdo para a pasta do aplicativo

O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado. Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configurações, chame <xref:System.IO.Directory.SetCurrentDirectory*> com o caminho para a [raiz do conteúdo](xref:fundamentals/index#content-root)do aplicativo.

No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Armazenar os arquivos do serviço em um local adequado no disco

Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.

## <a name="troubleshoot"></a>Solução de problemas

Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot>.

### <a name="common-errors"></a>Erros comuns

* Uma versão antiga ou de pré-lançamento do PowerShell está em uso.
* O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) . A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo. Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:
  * FDD do *compartimento/versão/{estrutura de destino}* (a)
  * *bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)
* O serviço não está em estado de execução.
* Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos. O caminho base de um serviço do Windows é *c:\\Windows\\system32*.
* O usuário não tem direitos de *logon como um serviço* .
* A senha do usuário expirou ou foi passada incorretamente ao executar o comando `New-Service` PowerShell.
* O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).
* A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.

### <a name="system-and-application-event-logs"></a>Logs de eventos do sistema e do aplicativo

Acesse os logs de eventos do sistema e do aplicativo:

1. Abra o menu Iniciar, procure *Visualizador de eventos*e selecione o aplicativo **Visualizador de eventos** .
1. No **Visualizador de Eventos**, abra o nó **Logs do Windows**.
1. Selecione **sistema** para abrir o log de eventos do sistema. Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.
1. Procure erros associados ao aplicativo com falha.

### <a name="run-the-app-at-a-command-prompt"></a>Execute o aplicativo em um prompt de comando

Muitos erros de inicialização não produzem informações úteis nos logs de eventos. Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem. Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Limpar caches de pacote

Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo. Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais. A maioria desses problemas pode ser corrigida seguindo estas instruções:

1. Exclua as pastas *bin* e *obj*.
1. Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.

   A limpeza dos caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e a execução do comando `nuget locals all -clear`. *nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).

1. Restaure e recompile o projeto.
1. Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.

### <a name="slow-or-hanging-app"></a>Aplicativo lento ou travando

Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.

#### <a name="app-crashes-or-encounters-an-exception"></a>O aplicativo falha ou encontra uma exceção

Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):

1. Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.
1. Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Execute o aplicativo sob as condições que causam a falha.
1. Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente. O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.

> [!WARNING]
> Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>O aplicativo trava, falha durante a inicialização ou executa normalmente

Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.

#### <a name="analyze-the-dump"></a>Analisar o despejo de memória

Um despejo de memória pode ser analisado usando várias abordagens. Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Recursos adicionais

* [Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS. Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* [SDK do ASP.NET Core 2.1 ou posterior](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 ou posterior](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Configuração do aplicativo

O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console. Verifique se o depurador está anexado ou se uma opção `--console` está presente. Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Se as condições forem falsas (o aplicativo for executado como um serviço):

* Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo. Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado. Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root). Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.
* Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.

Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.

Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.

No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo. Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Tipo de implantação

Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>.

Para um serviço baseado em aplicativo Web que usa as estruturas Razor Pages ou MVC, especifique o SDK Web no arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>FDD (Implantação dependente de estrutura)

A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino. Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável ( *.exe*), chamado de *executável dependente de estrutura*.

O [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contém a estrutura de destino. No exemplo a seguir, o RID é especificado como `win7-x64`. A propriedade `<SelfContained>` é definida como `false`. Essas propriedades instruem o SDK a gerar um arquivo executável ( *.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.

A propriedade `<UseAppHost>` é definida como `true`. Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.

O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows. Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>SCD (Implantação Autossuficiente)

A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino. O runtime e as dependências do aplicativo são implantados com o aplicativo.

Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Para publicar para vários RIDs:

* Forneça os RIDs em uma lista delimitada por ponto e vírgula.
* Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).

Uma propriedade `<SelfContained>` está definida como `true`:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Conta de usuário do serviço

Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.

Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.

A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.

Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).

Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado. Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Fazer logon como direitos de um serviço

Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:

1. Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.
1. Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.
1. Abra a política **Fazer logon como um serviço**.
1. Selecione **Adicionar Usuário ou Grupo**.
1. Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:
   1. Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.
   1. Selecione **Avançado**. Escolha **Localizar Agora**. Escolha a conta de usuário na lista. Selecione **OK**. Escolha **OK** novamente para adicionar o usuário à política.
1. Escolha **OK** ou **Aplicar** para aceitar as alterações.

## <a name="create-and-manage-the-windows-service"></a>Criar e gerenciar o Serviço Windows

### <a name="create-a-service"></a>Criar um serviço

Use comandos do PowerShell para registrar um serviço. Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}` &ndash; caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice`). Não inclua o executável do aplicativo no caminho. A barra à direita não é necessária.
* `{DOMAIN OR COMPUTER NAME\USER}` conta de usuário do serviço de &ndash; (por exemplo, `Contoso\ServiceUser`).
* `{SERVICE NAME}` &ndash; nome do serviço (por exemplo, `MyService`).
* `{EXE FILE PATH}` &ndash; o caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe`). Inclua nome do arquivo do executável com a extensão.
* `{DESCRIPTION}` descrição do serviço &ndash; (por exemplo, `My sample service`).
* `{DISPLAY NAME}` nome de exibição do serviço &ndash; (por exemplo, `My Service`).

### <a name="start-a-service"></a>Iniciar um serviço

Inicie o serviço com o seguinte comando do PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

O comando leva alguns segundos para iniciar o serviço.

### <a name="determine-a-services-status"></a>Determinar o status do serviço

Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

O status é relatado como um dos seguintes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Parar um serviço

Pare um serviço com o seguinte comando do Powershell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Remover um serviço

Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Manipular eventos de início e de parada

Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:

1. Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional. Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configurar pontos de extremidade

Por padrão, o ASP.NET Core é associado a `http://localhost:5000`. Configure a URL e a porta definindo a variável de ambiente `ASPNETCORE_URLS`.

Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS. Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.

> [!NOTE]
> Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.

## <a name="current-directory-and-content-root"></a>Diretório atual e a raiz do conteúdo

O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*. A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações). Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Defina o caminho da raiz do conteúdo para a pasta do aplicativo

O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado. Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configurações, chame <xref:System.IO.Directory.SetCurrentDirectory*> com o caminho para a [raiz do conteúdo](xref:fundamentals/index#content-root)do aplicativo.

No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Armazenar os arquivos do serviço em um local adequado no disco

Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.

## <a name="troubleshoot"></a>Solução de problemas

Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot>.

### <a name="common-errors"></a>Erros comuns

* Uma versão antiga ou de pré-lançamento do PowerShell está em uso.
* O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) . A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo. Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:
  * FDD do *compartimento/versão/{estrutura de destino}* (a)
  * *bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)
* O serviço não está em estado de execução.
* Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos. O caminho base de um serviço do Windows é *c:\\Windows\\system32*.
* O usuário não tem direitos de *logon como um serviço* .
* A senha do usuário expirou ou foi passada incorretamente ao executar o comando `New-Service` PowerShell.
* O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).
* A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.

### <a name="system-and-application-event-logs"></a>Logs de eventos do sistema e do aplicativo

Acesse os logs de eventos do sistema e do aplicativo:

1. Abra o menu Iniciar, procure *Visualizador de eventos*e selecione o aplicativo **Visualizador de eventos** .
1. No **Visualizador de Eventos**, abra o nó **Logs do Windows**.
1. Selecione **sistema** para abrir o log de eventos do sistema. Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.
1. Procure erros associados ao aplicativo com falha.

### <a name="run-the-app-at-a-command-prompt"></a>Execute o aplicativo em um prompt de comando

Muitos erros de inicialização não produzem informações úteis nos logs de eventos. Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem. Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Limpar caches de pacote

Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo. Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais. A maioria desses problemas pode ser corrigida seguindo estas instruções:

1. Exclua as pastas *bin* e *obj*.
1. Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.

   A limpeza dos caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e a execução do comando `nuget locals all -clear`. *nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).

1. Restaure e recompile o projeto.
1. Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.

### <a name="slow-or-hanging-app"></a>Aplicativo lento ou travando

Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.

#### <a name="app-crashes-or-encounters-an-exception"></a>O aplicativo falha ou encontra uma exceção

Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):

1. Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.
1. Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Execute o aplicativo sob as condições que causam a falha.
1. Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente. O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.

> [!WARNING]
> Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>O aplicativo trava, falha durante a inicialização ou executa normalmente

Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.

#### <a name="analyze-the-dump"></a>Analisar o despejo de memória

Um despejo de memória pode ser analisado usando várias abordagens. Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Recursos adicionais

* [Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
