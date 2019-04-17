---
title: Hospedar o ASP.NET Core em um serviço Windows
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/04/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 544eefa87898e82ec2bf8f9f61ce4e26dd554bb7
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068330"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hospedar o ASP.NET Core em um serviço Windows

Por [Luke Latham](https://github.com/guardrex) e [Tom Dykstra](https://github.com/tdykstra)

Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS. Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

* [PowerShell 6.2 ou posterior](https://github.com/PowerShell/PowerShell)

> [!NOTE]
> Para sistemas operacionais do Windows anteriores à atualização do Windows 10 de outubro de 2018 (versão 1809/build 10.0.17763), o módulo [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts) deve ser importado com o [módulo WindowsCompatibility](https://github.com/PowerShell/WindowsCompatibility) para acessar o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) usado na seção [Criar uma conta de usuário](#create-a-user-account):
>
> ```powershell
> Install-Module WindowsCompatibility -Scope CurrentUser
> Import-WinModule Microsoft.PowerShell.LocalAccounts
> ```

## <a name="deployment-type"></a>Tipo de implantação

Você pode criar uma implantação de Serviço Windows dependente de estrutura ou autocontida. Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

### <a name="framework-dependent-deployment"></a>Implantação dependente de estrutura

A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino. Quando o cenário FDD é usado com um aplicativo de Serviço Windows do ASP.NET Core, o SDK produz um executável (*\*.exe*), chamado de *executável dependente de estrutura*.

### <a name="self-contained-deployment"></a>Implantação autocontida

A SCD (Implantação Autocontida) não se baseia na presença de componentes compartilhados no sistema de destino. O tempo de execução e as dependências do aplicativo são implantados com o aplicativo para o sistema de hospedagem.

## <a name="convert-a-project-into-a-windows-service"></a>Converter um projeto em um serviço Windows

Faça as seguintes alterações em um projeto ASP.NET Core existente para executar o aplicativo como um serviço:

### <a name="project-file-updates"></a>Atualizações de arquivo de projeto

Com base na sua escolha de [tipo de implantação](#deployment-type), atualize o arquivo de projeto:

#### <a name="framework-dependent-deployment-fdd"></a>FDD (Implantação dependente de estrutura)

Adicione um [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ao `<PropertyGroup>` que contém a estrutura de destino. No exemplo a seguir, o RID é especificado como `win7-x64`. Adicione a propriedade `<SelfContained>` definida como `false`. Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows.

O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows. Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

::: moniker range=">= aspnetcore-2.2"

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

Adicione a propriedade `<UseAppHost>` definida como `true`. Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.

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

#### <a name="self-contained-deployment-scd"></a>SCD (Implantação Autossuficiente)

Confirme a presença de um [RID (Identificador de Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ou adicione um RID ao `<PropertyGroup>` que contém a estrutura de destino. Desabilite a criação de um arquivo *web.config* adicionando a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Para publicar para vários RIDs:

* Forneça os RIDs em uma lista delimitada por ponto e vírgula.
* Use o nome da propriedade `<RuntimeIdentifiers>` (plural).

  Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).

Adicionar uma referência de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).

Para habilitar o registro em log do Log de Eventos do Windows, adicione uma referência de pacote para [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).

### <a name="programmain-updates"></a>Atualizações de Program.Main

Faça as seguintes alterações em `Program.Main`:

* Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console. Verifique se o depurador está anexado ou se há um argumento de linha de comado do `--console` presente.

  Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> no Host da Web.

  Se as condições forem falsas (o aplicativo for executado como um serviço):

  * Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo. Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado. Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).
  * Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.

  Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.

* Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*. Para fins de teste e demonstração, o arquivo de configurações de Produção do aplicativo de exemplo define o nível de log para `Information`. Na produção, o valor normalmente é definido como `Error`. Para obter mais informações, consulte <xref:fundamentals/logging/index#windows-eventlog-provider>.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="publish-the-app"></a>Publique o aplicativo

Publique o aplicativo usando [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), um [perfil de publicação do Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles) ou o Visual Studio Code. Ao usar o Visual Studio, selecione o **FolderProfile** e configure o **Local de destino** antes de selecionar o botão **Publicar**.

Para publicar o aplicativo de exemplo usando as ferramentas da CLI (Interface de Linha de Comando), execute o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) em um shell de comando do Windows na pasta do projeto, passando a configuração de uma versão para a opção [-c|--configuration](/dotnet/core/tools/dotnet-publish#options). Use a opção [-o|--output](/dotnet/core/tools/dotnet-publish#options) com um caminho para publicar em uma pasta fora do aplicativo.

### <a name="publish-a-framework-dependent-deployment-fdd"></a>Publicar uma FDD (Implantação Dependente de Estrutura)

No exemplo a seguir, o aplicativo é publicado na pasta *c:\\svc*:

```console
dotnet publish --configuration Release --output c:\svc
```

### <a name="publish-a-self-contained-deployment-scd"></a>Publicar uma SCD (Implantação Autossuficiente)

O RID deve ser especificado na propriedade `<RuntimeIdenfifier>` (ou `<RuntimeIdentifiers>`) do arquivo de projeto. Insira o tempo de execução na opção [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) do comando `dotnet publish`.

No exemplo a seguir, o aplicativo é publicado para o tempo de execução `win7-x64` para a pasta *c:\\svc*:

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

## <a name="create-a-user-account"></a>Criar uma conta de usuário

Crie uma conta de usuário para o serviço usando o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6:

```powershell
New-LocalUser -Name {NAME}
```

Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.

Crie uma conta de usuário com o nome `ServiceUser` para o aplicativo de exemplo.

```powershell
New-LocalUser -Name ServiceUser
```

A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.

Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).

Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado. Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="set-permission-log-on-as-a-service"></a>Definir permissão: Fazer logon como serviço

Conceda acesso de gravação/leitura/execução à pasta do aplicativo usando o comando [icacls](/windows-server/administration/windows-commands/icacls) de um shell de comando administrativo do PowerShell 6.

```powershell
icacls "{PATH}" /grant "{USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS}" /t
```

* `{PATH}` &ndash; Caminho para a pasta do aplicativo.
* `{USER ACCOUNT}` &ndash; A conta de usuário (SID).
* `(OI)` &ndash; O sinalizador de Herança de Objeto propaga as permissão para os arquivos subordinados.
* `(CI)` &ndash; O sinalizador de Herança de Contêiner propaga as permissão para as pastas subordinadas.
* `{PERMISSION FLAGS}` &ndash; Define as permissões de acesso do aplicativo.
  * Gravar (`W`)
  * Ler (`R`)
  * Executar (`X`)
  * Completo (`F`)
  * Modificar (`M`)
* `/t` &ndash; Aplique recursivamente aos arquivos e pastas subordinadas existentes.

Use o comando a seguir de um shell de comando administrativo do PowerShell 6 para o aplicativo de exemplo publicado na pasta *c:\\svc* e a conta `ServiceUser` com permissões de gravação/leitura/execução.

```powershell
icacls "c:\svc" /grant "ServiceUser:(OI)(CI)WRX" /t
```

Para obter mais informações, confira [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="create-the-service"></a>Criar o serviço

Use o script [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts) do PowerShell para registrar o serviço. Em um shell de comando administrativo do PowerShell 6, execute o script com o seguinte comando:

```powershell
.\RegisterService.ps1 
    -Name {NAME} 
    -DisplayName "{DISPLAY NAME}" 
    -Description "{DESCRIPTION}" 
    -Exe "{PATH TO EXE}\{ASSEMBLY NAME}.exe" 
    -User {DOMAIN\USER}
```

Observe o seguinte aplicativo de exemplo:

* O nome do serviço é **MyService**.
* O serviço publicado reside na pasta *c:\\svc*. O executável do aplicativo é chamado de *SampleApp.exe*.
* O serviço é executado na conta `ServiceUser`. No comando de exemplo a seguir, o nome do computador local é `Desktop-PC`. Substitua `Desktop-PC` pelo nome do computador ou do domínio do seu sistema.

```powershell
.\RegisterService.ps1 
    -Name MyService 
    -DisplayName "My Cool Service" 
    -Description "This is the Sample App service." 
    -Exe "c:\svc\SampleApp.exe" 
    -User Desktop-PC\ServiceUser
```

## <a name="manage-the-service"></a>Gerenciar o serviço

### <a name="start-the-service"></a>Iniciar o serviço

Inicie o serviço com o comando `Start-Service -Name {NAME}` do PowerShell 6.

Para iniciar o serviço de aplicativo de exemplo, use o seguinte comando:

```powershell
Start-Service -Name MyService
```

O comando leva alguns segundos para iniciar o serviço.

### <a name="determine-the-service-status"></a>Determinar o status do serviço

Para verificar o status do serviço, use o comando `Get-Service -Name {NAME}` do PowerShell 6. O status é relatado como um dos seguintes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

Use o seguinte comando para verificar o status do serviço de aplicativo de exemplo:

```powershell
Get-Service -Name MyService
```

### <a name="browse-a-web-app-service"></a>Procurar um serviço de aplicativo Web

Quando o serviço estiver no estado `RUNNING` e se o serviço for um aplicativo Web, procure o aplicativo em seu caminho (por padrão, `http://localhost:5000`, que redireciona para `https://localhost:5001` ao usar [Middleware de Redirecionamento HTTPS](xref:security/enforcing-ssl)).

Para o serviço de aplicativo de exemplo, procure o aplicativo em `http://localhost:5000`.

### <a name="stop-the-service"></a>Parar o serviço

Pare o serviço com o comando `Stop-Service -Name {NAME}` do PowerShell 6.

O comando a seguir interrompe o serviço de aplicativo de exemplo:

```powershell
Stop-Service -Name MyService
```

### <a name="remove-the-service"></a>Remover o serviço

Após um pequeno atraso para interromper um serviço, remova o serviço com o comando `Remove-Service -Name {NAME}` do PowerShell 6.

Verifique o status do serviço de aplicativo de exemplo:

```powershell
Remove-Service -Name MyService
```

## <a name="handle-starting-and-stopping-events"></a>Manipular eventos de início e de parada

Para manipular os eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, faça as seguintes alterações adicionais:

1. Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Para ver o local do <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, consulte o exemplo de código mostrado na seção [Converter um projeto em um Serviço Windows](#convert-a-project-into-a-windows-service).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional. Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-https"></a>Configurar o HTTPS

Para configurar o serviço com um ponto de extremidade seguro:

1. Crie um certificado X.509 para o sistema de hospedagem usando os mecanismos de implantação e aquisição do certificado da sua plataforma.

1. Especifique uma [Configuração do ponto de extremidade HTTPS do servidor Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) para usar o certificado.

Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.

## <a name="current-directory-and-content-root"></a>Diretório atual e a raiz do conteúdo

O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*. A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações). Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Defina o caminho da raiz do conteúdo para a pasta do aplicativo

O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando o serviço é criado. Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configuração, chame <xref:System.IO.Directory.SetCurrentDirectory*> com o caminho para a raiz do conteúdo do aplicativo.

No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a>Armazene os arquivos do serviço em um local adequado no disco

Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.

## <a name="additional-resources"></a>Recursos adicionais

* [Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
