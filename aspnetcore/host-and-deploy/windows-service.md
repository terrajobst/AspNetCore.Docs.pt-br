---
title: Hospedar o ASP.NET Core em um serviço Windows
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: b02e627af875f15a81d68b0d625a2eccf25c0657
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333799"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hospedar o ASP.NET Core em um serviço Windows

Por [Luke Latham](https://github.com/guardrex) e [Tom Dykstra](https://github.com/tdykstra)

Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS. Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisites

* [SDK do ASP.NET Core 2.1 ou posterior](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 ou posterior](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a>Modelo de serviço de trabalho

O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada. Para usar o modelo como base para um aplicativo de Serviço Windows:

1. Crie um aplicativo de serviço de trabalho usando o modelo do .NET Core.
1. Siga as orientações na seção [Configuração de aplicativos](#app-configuration) para atualizar o aplicativo do Serviço de Trabalho para que ele possa ser executado como um Serviço Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

::: moniker-end

## <a name="app-configuration"></a>Configuração do aplicativo

::: moniker range=">= aspnetcore-3.0"

`IHostBuilder.UseWindowsService`, fornecido pelo pacote [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices), é chamado na compilação do host. Se o aplicativo estiver sendo executado como um Serviço Windows, o método:

* Define o tempo de vida do host como `WindowsServiceLifetime`.
* Define a [raiz do conteúdo](xref:fundamentals/index#content-root).
* Habilita o registro de log no log de eventos com o nome do aplicativo como o nome da fonte padrão.
  * O nível do log pode ser configurado com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.json*.
  * Somente administradores podem criar novas fontes de evento. Quando uma fonte de evento não puder ser criada usando o nome do aplicativo, um aviso será registrado em log como a fonte do *Aplicativo* e os logs de eventos serão desabilitados.

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console. Verifique se o depurador está anexado ou se uma opção `--console` está presente. Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Se as condições forem falsas (o aplicativo for executado como um serviço):

* Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo. Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado. Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root). Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.
* Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.

Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.

Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.

No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo. Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a>Tipo de implantação

Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

### <a name="framework-dependent-deployment-fdd"></a>FDD (Implantação dependente de estrutura)

A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino. Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável ( *.exe*), chamado de *executável dependente de estrutura*.

::: moniker range=">= aspnetcore-3.0"

Adicione os seguintes elementos de propriedade ao arquivo de projeto:

* `<OutputType>` &ndash; O tipo de saída do aplicativo (`Exe` para o executável).
* `<LangVersion>` &ndash; A versão da linguagem C# (`latest` ou `preview`).

O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows. Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <OutputType>Exe</OutputType>
  <LangVersion>preview</LangVersion>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

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

::: moniker-end

::: moniker range="= aspnetcore-2.1"

O [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contém a estrutura de destino. No exemplo a seguir, o RID é especificado como `win7-x64`. A propriedade `<SelfContained>` é definida como `false`. Essas propriedades instruem o SDK a gerar um arquivo executável ( *.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.

A propriedade `<UseAppHost>` é definida como `true`. Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.

O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows. Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a>SCD (Implantação Autossuficiente)

A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino. O tempo de execução e as dependências do aplicativo são implantados com o aplicativo.

Um [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows está incluído no `<PropertyGroup>` que contém a estrutura de destino:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Para publicar para vários RIDs:

* Forneça os RIDs em uma lista delimitada por ponto e vírgula.
* Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).

::: moniker range="< aspnetcore-3.0"

Uma propriedade `<SelfContained>` está definida como `true`:

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a>Conta de usuário do serviço

Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.

Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:

```PowerShell
New-LocalUser -Name {NAME}
```

No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {NAME}"
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

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}` &ndash; Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice`). Não inclua o executável do aplicativo no caminho. A barra à direita não é necessária.
* `{DOMAIN OR COMPUTER NAME\USER}` &ndash; Conta de usuário do serviço (por exemplo, `Contoso\ServiceUser`).
* `{NAME}` &ndash; Nome do serviço (por exemplo, `MyService`).
* `{EXE FILE PATH}` &ndash; O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe`). Inclua nome do arquivo do executável com a extensão.
* `{DESCRIPTION}` &ndash; Descrição do serviço (por exemplo, `My sample service`).
* `{DISPLAY NAME}` &ndash; Nome de exibição do serviço (por exemplo, `My Service`).

### <a name="start-a-service"></a>Iniciar um serviço

Inicie o serviço com o seguinte comando do PowerShell 6:

```powershell
Start-Service -Name {NAME}
```

O comando leva alguns segundos para iniciar o serviço.

### <a name="determine-a-services-status"></a>Determinar o status do serviço

Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:

```powershell
Get-Service -Name {NAME}
```

O status é relatado como um dos seguintes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Parar um serviço

Pare um serviço com o seguinte comando do Powershell 6:

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a>Remover um serviço

Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

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

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Usar ContentRootPath ou ContentRootFileProvider

Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para localizar os recursos do aplicativo.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Armazenar os arquivos do serviço em um local adequado no disco

Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.

## <a name="additional-resources"></a>Recursos adicionais

::: moniker range=">= aspnetcore-3.0"

* [Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
